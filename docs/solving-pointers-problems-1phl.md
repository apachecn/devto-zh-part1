# 解决指针问题

> 原文：<https://dev.to/dzeban/solving-pointers-problems-1phl>

众所周知，C 编程中最难的部分是使用指针。它们看起来很简单——“指针是包含变量地址的变量”(K&R 第五章)。但是当你开始使用它的时候，很容易把星号、符号、箭头之类的弄混。

大多数时候，你可以对指针有一些肤浅的理解。事实上，即使在生产代码中，除了从`malloc`获取一个指针并将其传递给一些函数之外，你很少看到其他东西。这就是你在 C 编程面试问题上被抓住的地方，因为人们喜欢问一些棘手的问题。比如，写一个函数来反转一个链表或者做一个二叉树的有序遍历。

实际上，我在 2012 年的一次面试中失败了，因为我没能写出一个函数来回复一个链表。是的，我很沮丧。从那时起，我向自己承诺，我会弄清楚这狗屎到底是如何工作的。所以这是我的指点顿悟贴。

我认为**解决任何指针问题的关键是正确绘制它们**。让我给你看一个链表的例子，因为它有很多指针:

[![Linked list](img/bc901e7adfbd7eebd9173bb66b953965.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--78Vl24bH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/slist/slist-basic.png)

每个元素有 2 个方块——一个是“有效载荷”变量，另一个是指针变量。最后一个指针值当然是空的。列表的头是一个指针，它和其他变量一样画在一个“盒子”里。

像其他变量一样在框中画指针并用箭头显示指针值指向的地方是非常重要的，因为这种表示将帮助你理解指针代码。

例如，下面是迭代链表的代码:

```
struct list *cur = head;
while (cur) {
    printf("cur is %p, val is %d\n", cur, cur->n);
    cur = cur->next;
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以通过直觉理解它，但是你真的理解为什么和如何工作吗？画个图！

[![Linked list iteration](img/f5a748a2d8a37c0f5dbee853e24828f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M_hfEvIk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/slist/slist-iter.png)

`cur = cur->next`正在施展它的魔法，因为 C 语言中的箭头操作符翻译过来就是:`cur = (*cur).next`。首先，你要去引用一个指针——在指针下给你一个值。其次，你得到了`next`指针的值。第三，将该值复制到`cur`。这就是它如何让你跳过指针。

如果不合适，不要担心。慢慢来，自己画，自己沉。

现在，当它看起来很容易时，让我们看看双指针或指针对指针。

下面是相同的迭代，但是使用了双指针:

```
struct list **pp = &head;
while (*pp) {
    cur = *pp;
    printf("cur is %p, val is %d\n", cur, cur->n);
    *pp = &(cur->next);
} 
```

Enter fullscreen mode Exit fullscreen mode

这是它的表现形式:

[![Pointer to pointer](img/8e3b013c7be2bf527683a1dc8fdb1465.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u7jEcQnG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/slist/slist-pp.png)

双指针很有用，因为它们允许你改变底层指针**和**的值。以下是为什么可能的例证:

[![Double pointer dereference](img/8a05bfb0c138224614144eadcd15b1ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3rXVhBzk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/slist/slist-pp-val.png)

注意，`*pp`是一个指针，但它是一个不同于`pp`的“盒子”。`pp`指向指针，而`*pp`指向数值。

所有这些乍一听起来可能没什么用，但是，如果没有双指针，有些代码就很难阅读，有些甚至不可能。

以从链表中移除元素的任务为例。你必须遍历列表找到要删除的元素，然后删除它。从链表中删除一个元素是对相邻指针的更新。这包括`head`指针，因为你可能需要删除第一个元素。

如果你用一个简单的指针迭代元素，就像我的第一个例子，你必须有`cur`和`prev`指针来使前一个指针围绕被删除的元素。没关系，但是你也需要一个特例，如果`prev`指针是`head`，因为头必须更新。下面是代码:

```
void list_remove(int i, struct list **head)
{
    struct list *cur = *head;
    struct list *prev = NULL;
    while (cur->next) {
        if (cur->n == i) {
            if (prev) {
                // Make previous pointer around deleted element
                prev->next = cur->next;
            } else {
                // prev == NULL means we removing head,
                // so shift head to next element.
                *head = cur->next;
            }
            free(cur);
        }

        // Iterating...
        prev = cur;
        cur = cur->next;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

它可以工作，但是看起来有点复杂——它*需要*注释来解释这里发生了什么。有了双指针，它看起来像一个微风:

```
void list_remove_pp(int i, struct list **head)
{
    struct list **pp;
    struct list *cur;

    pp = head;
    while (*pp) {
        cur = *pp;
        if (cur->n == i) {
            *pp = cur->next;
            free(cur);
        }
        pp = &((*pp)->next);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们使用双指针，我们没有 head 的特例——用`pp`我们可以像修改列表中的其他指针一样修改它。

所以下一次你会发现自己在与一些指针问题作斗争——画一张图，显示指针作为任何其他变量，你会找到答案。

请记住，这里没有魔法- **指针只是一个普通的变量，但是你以一种不寻常的方式使用它**。