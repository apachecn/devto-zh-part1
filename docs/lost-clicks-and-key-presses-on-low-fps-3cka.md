# 在低 FPS 下丢失点击和按键

> 原文：<https://dev.to/reg__/lost-clicks-and-key-presses-on-low-fps-3cka>

我刚刚解决了一个在游戏和其他交互式应用程序中处理鼠标和键盘输入的问题。我想分享我的解决方案的代码。当你的应用程序使用一个不断计算和渲染帧的循环时，就像游戏通常做的那样，只读取每个帧上每个鼠标和键盘按键的当前状态(无论是按下还是按下)似乎是很自然的。然后你可以通过比较新的状态和前一帧的状态来计算衍生的信息，比如一个按钮是否刚刚被按下。这就是[亲爱的 ImGui](https://github.com/ocornut/imgui) 库的工作方式。所以第一个解决方案可能是这样的:

```
void UpdateFrame()
{
    // Fill ImGui::GetIO().DeltaTime, KeyCtrl, KeyShift, KeyAlt etc.
    ImGui::GetIO().MouseDown[0] = (GetKeyState(VK_LBUTTON) & 0x8000) != 0;
    ImGui::GetIO().MouseDown[1] = (GetKeyState(VK_RBUTTON) & 0x8000) != 0;
    ImGui::GetIO().MouseDown[2] = (GetKeyState(VK_MBUTTON) & 0x8000) != 0;
    for(uint32_t i = 0; i < 512; ++i)
        ImGui::GetIO().KeysDown[i] = (GetKeyState(i) & 0x8000) != 0;

    ImGui::NewFrame();

    if(ImGui::IsKeyPressed('A'))
        // Do something...
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法有一个问题。如果用户按下并释放一个键的时间很短，那么按下和释放都发生在两个帧之间，这将不会被注意到。这很讨厌。尤其是在以下情况下:

*   您的节目帧率低。
*   点击非常快，因为它们是由程序生成的，例如由 [X-Mouse 按钮控件](https://www.highrez.co.uk/downloads/xmousebuttoncontrol.htm)或其他鼠标软件生成的自动双击。

解决这个问题的第一步是对操作系统发送的“真实”事件做出反应:

```
LRESULT WINAPI WndProc(HWND wnd, UINT msg, WPARAM wParam, LPARAM lParam)
{
    switch(msg)
    {
    case WM_LBUTTONDOWN:
    case WM_LBUTTONDBLCLK:
        SetCapture(wnd);
        ImGui::GetIO().MouseDown[0] = true;
        return 0;
    case WM_RBUTTONDOWN:
    case WM_RBUTTONDBLCLK:
        SetCapture(wnd);
        ImGui::GetIO().MouseDown[1] = true;
        return 0;
    case WM_MBUTTONDOWN:
    case WM_MBUTTONDBLCLK:
        SetCapture(wnd);
        ImGui::GetIO().MouseDown[2] = true;
        return 0;
    case WM_LBUTTONUP:
        ImGui::GetIO().MouseDown[0] = false;
        ReleaseCapture();
        return 0;
    case WM_RBUTTONUP:
        ImGui::GetIO().MouseDown[1] = false;
        ReleaseCapture();
        return 0;
    case WM_MBUTTONUP:
        ImGui::GetIO().MouseDown[2] = false;
        ReleaseCapture();
        return 0;
    case WM_KEYDOWN:
        if (wParam < 512)
            ImGui::GetIO().KeysDown[wParam] = true;
        return 0;
    case WM_KEYUP:
        if (wParam < 512)
            ImGui::GetIO().KeysDown[wParam] = false;
        return 0;
    case WM_CHAR:
        // Text input should be processed here, not in WM_KEYDOWN.
        // wParam is entered characater.
        return 0;
    case WM_SYSCOMMAND:
        // Stop ALT from freezing entire application.
        if((wParam & 0xfff0) == SC_KEYMENU)
            return 0;
        break;
    // Handle WM_CREATE, WM_DESTROY, WM_CLOSE and everything else you need...
    }
    return DefWindowProc(wnd, msg, wParam, lParam);
}

void UpdateFrame()
{
    // Fill ImGui::GetIO().DeltaTime, KeyCtrl, KeyShift, KeyAlt etc.

    ImGui::NewFrame();

    if(ImGui::IsKeyPressed('A'))
        // Do something...
} 
```

Enter fullscreen mode Exit fullscreen mode

但是这并没有解决问题。当按下和释放事件都发生在两个帧之间时，`ImGui::GetIO()`中的状态来回改变，并且在下一帧中仍然不被注意。

当然，最好是直接对这些 Windows 消息作出反应，但是如果您需要将输入状态传递给 ImGui 或其他一些系统，而这些系统只需要在每个新帧开始时得到当前状态，那么解决方案就是创建我们自己的输入消息队列。这些消息将在`WndProc`中发布，并在`UpdateFrame`中使用，其中`ImGui::GetIO()`状态也将被更新。`UpdateFrame`可以消耗队列中的多条消息，除非它们引用同一个键——那么第二条消息和所有后续消息必须留到下一帧。这将会引入一些延迟，但是用这种方法处理输入，除了将快速双击扩展到多个帧之外，没有其他方法可以检测到快速双击:

```
Frame i: MouseDown[button] = true;
Frame i+1: MouseDown[button] = false;
Frame i+2: MouseDown[button] = true;
Frame i+3: MouseDown[button] = false; 
```

Enter fullscreen mode Exit fullscreen mode

我最终的解决方案是这样的:

```
struct SMouseEvent
{
    int8_t Button;
    bool Down;
};
struct SKeyboardEvent
{
    uint16_t Key;
    bool Down;
};
static const uint32_t EVENT_QUEUE_MAX_COUNT = 8;
static SMouseEvent g_MouseEvents[EVENT_QUEUE_MAX_COUNT];
static uint32_t g_MouseEventCount;
static SKeyboardEvent g_KeyboardEvents[EVENT_QUEUE_MAX_COUNT];
static uint32_t g_KeyboardEventCount;

void AddMouseEvent(int8_t button, bool down)
{
    if(g_MouseEventCount < EVENT_QUEUE_MAX_COUNT)
        g_MouseEvents[g_MouseEventCount++] = { button, down };
}

void AddKeyboardEvent(uint16_t key, bool down)
{
    if(g_KeyboardEventCount < EVENT_QUEUE_MAX_COUNT)
        g_KeyboardEvents[g_KeyboardEventCount++] = { key, down };
}

LRESULT WINAPI WndProc(HWND wnd, UINT msg, WPARAM wParam, LPARAM lParam)
{
    switch(msg)
    {
    case WM_LBUTTONDOWN:
    case WM_LBUTTONDBLCLK:
        SetCapture(wnd);
        AddMouseEvent(0, true);
        return 0;
    case WM_RBUTTONDOWN:
    case WM_RBUTTONDBLCLK:
        SetCapture(wnd);
        AddMouseEvent(1, true);
        return 0;
    case WM_MBUTTONDOWN:
    case WM_MBUTTONDBLCLK:
        SetCapture(wnd);
        AddMouseEvent(2, true);
        return 0;
    case WM_LBUTTONUP:
        AddMouseEvent(0, false);
        ReleaseCapture();
        return 0;
    case WM_RBUTTONUP:
        AddMouseEvent(1, false);
        ReleaseCapture();
        return 0;
    case WM_MBUTTONUP:
        AddMouseEvent(2, false);
        ReleaseCapture();
        return 0;
    case WM_KEYDOWN:
        if (wParam < 512)
            AddKeyboardEvent((uint16_t)wParam, true);
        return 0;
    case WM_KEYUP:
        if (wParam < 512)
            AddKeyboardEvent((uint16_t)wParam, false);
        return 0;
    case WM_CHAR:
        // Text input should be processed here, not in WM_KEYDOWN.
        // wParam is entered characater.
        return 0;
    case WM_SYSCOMMAND:
        // Stop ALT from freezing entire application.
        if((wParam & 0xfff0) == SC_KEYMENU)
            return 0;
        break;
    // Handle WM_CREATE, WM_DESTROY, WM_CLOSE and everything else you need...
    }
    return DefWindowProc(wnd, msg, wParam, lParam);
}

void UpdateFrame()
{
    ImGuiIO& io = ImGui::GetIO();
    // Fill io.DeltaTime, KeyCtrl, KeyShift, KeyAlt etc.

    // Process mouse events.
    {
        uint32_t processCount = g_MouseEventCount;
        // Limit processCount to avoid multiple events with same button.
        for(uint32_t i = 0; i + 1 < processCount; ++i)
        {
            for(uint32_t j = i + 1; j < processCount; ++j)
            {
                if(g_MouseEvents[j].Button == g_MouseEvents[i].Button)
                {
                    processCount = j;
                    break;
                }
            }
        }
        // Apply mouse events.
        for(uint32_t i = 0; i < processCount; ++i)
        {
            const SMouseEvent& event = g_MouseEvents[i];
            io.MouseDown[event.Button] = event.Down;
        }
        // Remove events from queue.
        for(uint32_t i = 0; i < g_MouseEventCount - processCount; ++i)
            g_MouseEvents[i] = g_MouseEvents[processCount + i];
        g_MouseEventCount -= processCount;
    }

    // Process keyboard events.
    {
        uint32_t processCount = g_KeyboardEventCount;
        // Limit processCount to avoid multiple events with same key.
        for(uint32_t i = 0; i + 1 < processCount; ++i)
        {
            for(uint32_t j = i + 1; j < processCount; ++j)
            {
                if(g_KeyboardEvents[j].Key == g_KeyboardEvents[i].Key)
                {
                    processCount = j;
                    break;
                }
            }
        }
        // Apply keyboard events.
        for(uint32_t i = 0; i < processCount; ++i)
        {
            const SKeyboardEvent& event = g_KeyboardEvents[i];
            io.KeysDown[event.Key] = event.Down;
        }
        // Remove events from queue.
        for(uint32_t i = 0; i < g_KeyboardEventCount - processCount; ++i)
            g_KeyboardEvents[i] = g_KeyboardEvents[processCount + i];
        g_KeyboardEventCount -= processCount;
    }

    ImGui::NewFrame();

    if(ImGui::IsKeyPressed('A'))
        // Do something...
} 
```

Enter fullscreen mode Exit fullscreen mode