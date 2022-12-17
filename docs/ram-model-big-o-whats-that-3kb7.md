# 公羊模型，大 O 那是什么？

> 原文：<https://dev.to/johndoesup/ram-model-big-o-whats-that-3kb7>

这里有个问题我不明白。有人能帮忙吗？

## 使用 RAM 模型，为下面的代码段估算运行时间的 big-oh。

```
 for(i = 1; i < n; i++){
   small_pos = i;
   smallest = Array[samll_pos];
   for(j = i + 1; J <= n; j++){
     if(Array[j] < smallest){
       small_pos = j;
       smallest = Array[small_pos]
     }
   }
}
Array[small_pos] = Array[i];
Array[i] = smallest; 
```

Enter fullscreen mode Exit fullscreen mode