# 按总和在列表中查找子列表

> 原文：<https://dev.to/utmfcim/find-sublists-in-list-by-sum-94n>

请帮我完成任务。

给定一个列表 L = (A0，A1，A2，...，一个)。找到子列表，如果它们存在 L1 = (Ai，Ai + 1，...，Ai + k)，i + k <= N, i > = 1，其中 M = Ai + Ai + 1 +...Ai + k。

例如:

L = (1 3 -16 5 7 8 2 2)，M=12
结果:

L1 =(1 ^ 3-16)//1+3-16 = 12
L1 =(5 ^ 7)//5+7 = 12
L1 =(8 ^ 2 ^ 2)//8+2+2 = 12