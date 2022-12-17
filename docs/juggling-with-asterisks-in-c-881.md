# 在 C #中使用星号(*)变戏法

> 原文：<https://dev.to/yohhoy/juggling-with-asterisks-in-c-881>

要编写 C 程序，我们不能避免使用“指针”来管理空终止的字符串、链表结构、多维数组等。例如，你可以使用双指针——指向类型`T`的指针，简称`T**`——来处理动态分配的二维数组，声明`main`函数的`argv`参数。

对了，你在实践中见过或写过多少指针型的星号(`*`)。

据我所知， [ITU T-Rec 中有带七个星号的指针类型。H.264.2 参考软件](https://www.itu.int/rec/T-REC-H.264.2/en)。

是的，它是带有七(7)个星号的“七重指针”。它在英语中的意思是“指针指向指针指向指针指向指针指向指针指向指针指向类型`T`的指针”，看起来像 C 语言中的`T*******`。

```
// lcommon/src/memalloc.c
int get_mem6Dmv(MotionVector *******array6D,
                int dim0, int dim1, int dim2, int dim3, int dim4, int dim5) 
```

Enter fullscreen mode Exit fullscreen mode

```
// lencod/src/slice.c
static int get_mem_bipred_mv(Slice *currSlice, MotionVector ******* bipred_mv)
{
  get_mem6Dmv(bipred_mv, 2, 2, currSlice->max_num_references, 9, 4, 4);
  return 576 * currSlice->max_num_references * sizeof(MotionVector);
} 
```

Enter fullscreen mode Exit fullscreen mode

附注:参考软件也有八个星号的“八元组指针”来处理七维数组。幸运的是，这个功能已经不再使用了！

```
// lcommon/src/memalloc.c
int get_mem7Dmv(MotionVector ********array7D,
                int dim0, int dim1, int dim2, int dim3, int dim4, int dim5, int dim6) 
```

Enter fullscreen mode Exit fullscreen mode