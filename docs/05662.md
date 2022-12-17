# JavaScript 数组 push、pop、shift、unshift:如何记住区别

> 原文：<https://dev.to/yaphi1/javascript-array-push-pop-shift-unshift-how-to-remember-the-difference>

当您在 JavaScript 数组的开头和结尾添加和删除内容时，很难记住哪个方法做什么。以下是一些建议:

*   **Unshift** 和 **shift** 使整个数组**向侧面移动**(通过从头添加和删除项目)。

*   **Push** 和 **pop** 不会使阵列向侧面移动(因为它们在最后添加和移除项目)。

*   在每一对(push/pop 和 unshift/shift)中，**越长的字使数组越长**。

如果你好奇，我制作了一个快速的动画来说明这些观点。

希望这有帮助！

* * *

请注意，与其说这是一个完整的解释，不如说这是一个记忆技巧，所以请随意查看 MDN 以获得完整的细节和示例，如[按下](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push)、[弹出](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/pop)、[移动](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/shift)和[未移动](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift)。