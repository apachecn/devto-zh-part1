# 哪个更快:obj . hasownproperty(prop)vs object . keys(obj)。包括(道具)

> 原文：<https://dev.to/ethanarrowood/which-is-faster-objhasownpropertyprop-vs-objectkeysobjincludesprop-5704>

我不知道如何测试运行时速度，但我感兴趣的是这些方法中哪一个会导致更多的开销。

给定一个具有 **n** *属性*(也称为*键*)的对象，使用`obj.hasOwnProperty(prop)`或`Object.keys(obj).includes(prop)`检查该属性是否存在会更快。需要考虑的是，如果您需要多次检查多个属性，该怎么办？

如果你将`keys`存储在一个变量中:`const keys = Object.keys(obj)`然后进行一系列检查`keys.includes(prop1); keys.includes(prop2);`这比`obj.hasOwnProperty(prop1); obj.hasOwnProperty(prop2);`快还是慢