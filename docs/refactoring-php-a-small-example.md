# 一个小的重构例子

> 原文：<https://dev.to/phpandcigars/refactoring-php-a-small-example>

我目前正在审查一个新同事的一小段代码。下周他回来工作时，我会在这里分享一个如何重构代码的小例子，这样我就不会忘记向他提及。

我发现了这个:

```
 ...

foreach ($p->mProductUnits as $unit) {
    $ret .= hHtml::Clear();
    $ret .= $this->RenderInfoRow(
        $unit->showUnit(),
        $unit->RenderPriceWithCurrency(),
        $unit->AvailabilityMessage()
    );
}

...

private function RenderInfoRow($name, $price, $availbility){

    return
        '<div class="col-l-4 col-m-4">'.$name.'</div>'
        .'<div class="col-l-2 col-m-2">'.$price.'</div>'
        .'<div class="col-l-4 col-m-4">'. $availbility.'</div>';

} 
```

我们在这里有什么问题？匈牙利符号？嗯，是的，但这是我的错，是由于我写的框架。除此之外？

莱特。单一责任原则受到损害。鲍勃叔叔也教导我们，函数的一个参数是可以的。在这个例子中，函数 render inforrow()
完全可以只使用一个参数，所以让我们看看如何在第一步中重构它:

```
 foreach ($p->mProductUnits as $unit) {
    $ret .= $this->RenderInfoRow($unit);
}

private function RenderInfoRow(oProductUnit $u) {

    return
    hHtml::Clear()
    . '<div class="col-l-4 col-m-4">' . $u->showUnit() . '</div>'
    . '<div class="col-l-2 col-m-2">' . $u->RenderPriceWithCurrency() . '</div>'
    . '<div class="col-l-4 col-m-4">' . $u->AvailabilityMessage() . '</div>'
    ;

} 
```

这有不止一个优点。第一:读起来容易，写起来少。但是第二点(在这种情况下可能更重要):现在 IDE 知道$u 的类型是 oProductUnit。所以现在你不必打开类来查看函数名。只要在函数中键入$u，它们就会呈现出来。