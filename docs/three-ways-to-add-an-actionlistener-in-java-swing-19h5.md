# 在 Java Swing 中添加 ActionListener 的三种方法

> 原文：<https://dev.to/aussieguy/three-ways-to-add-an-actionlistener-in-java-swing-19h5>

当构建 GUI 时，界面需要一种监听和响应事件的方式。当用户与 GUI 交互时触发事件，例如按下按钮和插入文本。Java Swing 中的事件类型之一是 ActionEvent。生成 ActionEvent 的一个常用组件是 JButton，它只是一个按钮，按下时会产生一个事件。本文旨在解释将 ActionListener 添加到组件的三种不同方式。

## 组件作为 ActionListener

这是组件本身监听动作的时候。为此，只需在组件中实现 ActionListener 接口，例如:

```
public class ButtonExample extends JButton implements ActionListener {

    public ButtonExample() {
        addActionListener(this);
    }

    public void actionPerformed(ActionEvent ae) {
        //handle event here
    }

} 
```

因此，实现 ActionListener 的组件同时成为源和侦听器。如果事件相对简单且属于内部事件(例如，不连接多个组件)，您可能希望使用此方法。

这种添加 ActionListener 的方法打破了[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle),因为组件既负责创建事件又负责处理事件。

## 内心行动倾听者

当您使用“new”关键字为每个组件创建新的 ActionListener 时，使用此方法。

```
component.addActionListener(new ActionListener() {
        @Override
        public void actionPerformed(ActionEvent ae) {
            //handle event here
        }
}); 
```

这种实现非常适合简单的监听器，而且它还将监听器从组件中分离出来。如果您试图将 ActionListeners 添加到子组件，例如 JPanel 的子组件，请避免这种情况。这可能会导致混乱的代码，因为每个组件都在创建自己版本的 ActionListener。

## 分离动作监听器

这项技术要求您创建一个实现 ActionListener 的单独的类。需要这个 ActionListener 的组件只需创建这个类的一个新实例并添加它。

```
public class ButtonExample extends JButton {

      public ButtonExample() {
          addActionListener(new ButtonHandler());
      }   
}

public class ButtonHandler implements ActionListener {
      public ButtonHandler() {

      }   

      public void actionPerformed(ActionEvent ae) {
          //handle event here
      }   
} 
```

这是添加 ActionListener 的最干净的方式。它将组件与侦听器分离，创建了一个可重用的侦听器，可以跨不同的类使用，并允许组件之间的复杂交互。