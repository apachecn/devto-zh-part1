# ZFS 和内存问题

> 原文：<https://dev.to/yorodm/zfs-y-el-problema-de-la-memoria-n55>

# ZFS 和内存问题

**ZFS** 已经在 [Linux](http://www.open-zfs.org/) 的土地上呆了几年了，每次有机会我都会向我的一些朋友*【sys admin】*推荐，以及我所做的一系列关于如何使用 _filesystem_，优化一些比其他

注意到在大多数情况下，被劝告者都回到了对 **LVM** 的惩罚中，我致力于调查拒绝的原因以及如何加以纠正。

# 但问题是？

排除因素□(移植物只怕改变分布)与□**ZFS□**有关的大部分问题(在我询问的人中)与使用和/或配置不当有关(也称为:□bar las 最常见的四个是:

1.  一卷 **ZFS** 中的**交换**。
2.  不了解**克隆**和**快照**之间的区别。
3.  内存消耗。
4.  **raid**(硬件和**raid**出现问题。

将我的局部问题与全球问题进行比较(多亏了[【谷歌趋势】](http://trends.google.com)，我看到第 3 点和第 4 点排名很好，我说:∞去

# 因为 ZFS 是个好地方。

我向大家推荐的第一件事是读好教程。如果你来自世界**ext 4**+**【LVM】**，那么**【ZFS】**的哲学可能听起来很奇怪，你应该对各自操作方式的异同有所了解。视其各自的英语水平而定[在这里](http://www.open-zfs.org/wiki/Performance_tuning)对所有的优化参数，甚至案例研究都有很好的参考。

内存使用量只是 ZFS 中使用的缓存方法。大多数的*档案系统*使用【LRU】的某种变体，在此例中被*或**【arc】取代。 **ARC** 大大提高了性能，但消耗*大量 **RAM** 。尽管如此，很少需要**在此设定一些东西，虽然记忆体显示为忙碌(例如使用**时)是快取记忆体，而且如果有需要的话会由核心释放出来。假如你不知道你的 **ARC** 是怎么设置的，你可以简单地:******** 

```
cat /sys/module/zfs/parameters/zfs_arc_max 
```

Enter fullscreen mode Exit fullscreen mode

值 0 表示可用 ram 的一半将用于缓存。要更改此值，您可以执行以下操作:

```
# Configura la cantidad de memoria máxima disponible para ARC
echo <numero en bytes> >> /sys/module/zfs/parameters/zfs_arc_max
# fuerza al kernel a vaciar la información de caché
echo 3 > /proc/sys/vm/drop_caches 
```

Enter fullscreen mode Exit fullscreen mode

最后但并非最不重要的是，**raid**。 **RAIDZ** 是基于软件的 **RAID** 的实现。如果您使用的是**ZFS**，建议您使用**raidz**，而不是硬体解决方案。如果您的服务器要求您有 **RAID** ，则在硬件上创建 x 个驱动器 **RAID0** ，并将其添加到配置有**raidz**的【zpool 中。记住:**【决不】**将**【raidz】**放在具有**大于 0 的磁盘上。重复:**【决不】**。**

 **# 从 ZFS 开始。

每当我需要刷新话题时，这三个链接对我都很有用。

1.  [ZFS 12 人赛](http://jjmora.es/zfs_aprendiendo_zfs_en_12_pasos/)
2.  [打开-ZFS 维基](http://www.open-zfs.org/wiki/Main_Page)
3.  ZFS 维基百科*****