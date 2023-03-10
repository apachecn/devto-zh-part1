# 多行签名的情况

> 原文：<https://dev.to/greg0ire/the-case-for-multiline-signatures-dl6>

但愿，[我的上一篇博文](https://dev.to/greg0ire/why-i-keep-pestering-everyone-about-long-lines)说服了你不要排长队。php 社区有一个很好的编码风格指南，名为 [PSR-2](http://www.php-fig.org/psr/psr-2/) ，内容如下:

> 对线路长度不能有硬性限制；软限制必须为 120 个字符；行数应等于或少于 80 个字符。

基本上这意味着:做你想做的，但是如果你超过 120 个字符，你的编辑器会警告你。80 个字符的限制可能是为了鼓励人们添加换行符，即使他们的行只有 121 个字符，而不是说“来吧，这只是 1 个字符”。

一些项目利用了软限制，忽略了方法签名的软限制，并写下了:

```
<?php
public function connect(?string $sourceId, $sourceValue, ?string $destId, $destValue = null, string $reference = null, bool $lazy = false, bool $weak = false); 
```

Enter fullscreen mode Exit fullscreen mode

而不是这个:

```
<?php
    public function connect(
        ?string $sourceId,
        $sourceValue,
        ?string $destId,
        $destValue = null,
        string $reference = null,
        bool $lazy = false,
        bool $weak = false
    ); 
```

Enter fullscreen mode Exit fullscreen mode

在这个决定的背后，有许多我想在这里讨论的论点。不过要明确的是:我并不是建议您应该总是使用多行签名。在我看来，只有当行长度超过 120 个字符时，才应该使用它们。

## 方法签名不是真正的代码

主要的论点似乎是，方法签名不是“真正的代码”，因为它们声明事情而不是做事情，因此不应该污染屏幕空间。除了它们描述了你的方法的接口，对它们的修改是非常重要的，应该仔细检查，因为这是大多数 BC 中断发生的地方。此外，自 php 7.0 以来，函数签名得到了比以前更多的类型提示，这使它们更长，但也更有用，因为它们描述了您正在处理的变量的类型。这并不总是显而易见的，除非你使用[匈牙利符号](https://en.wikipedia.org/wiki/Hungarian_notation)，这是绝对不应该的。
拥有多行签名不仅对审查者有用，对编辑那部分代码的人也有用。如果一个参数映射到一行，那么从一个参数到另一个参数的导航会突然变得比其他情况下使用的 IDE 快捷键容易得多。
还不服气？考虑使用[代码折叠](https://en.wikipedia.org/wiki/Code_folding)，而不是让代码对其他人来说更难看，包括尤其是评审员。如果这个建议听起来很愚蠢，也许确实如此，毕竟签名很重要？

## 参数类型已经在 phpdoc 中描述过

然后把 phpdoc 去掉，除非你在里面写了非常有趣的东西。根据我的经验，强迫人们描述 phpdoc 中的每一个参数总是导致相同的转述评论，这有一个非常糟糕的后果:人们不再阅读评论。你当然可以把你给 phpdoc 的不动产给一段代码？现在大多数 ide 用浅灰色显示注释，可能就是因为这个，但是我感觉注释不应该主要针对文档提取工具或者注释库。当你的代码不够清晰时，它们应该是对你的团队有帮助的信息。

## 长方法签名是参数过多的警告信号

这一点实际上是没有意义的，因为如果你有一个 100000 字符的长行，它仍然是一行，而如果你的屏幕上充满了一个签名，警报信号只会更大，像 [phpmd](https://phpmd.org/) 这样的工具会警告你的方法有太多的参数。
意识到事情有多糟糕的第一步是[这个方法](https://github.com/magento/magento2/blob/a5be15b42bee194f5030cb516002aab5244fe057/app/code/Magento/Catalog/Model/Product.php#L392)肯定是使用多行签名:

```
<?php
    /**
     * Product constructor.
     * @param \Magento\Framework\Model\Context $context
     * @param \Magento\Framework\Registry $registry
     * @param \Magento\Framework\Api\ExtensionAttributesFactory $extensionFactory
     * @param AttributeValueFactory $customAttributeFactory
     * @param \Magento\Store\Model\StoreManagerInterface $storeManager
     * @param \Magento\Catalog\Api\ProductAttributeRepositoryInterface $metadataService
     * @param Product\Url $url
     * @param Product\Link $productLink
     * @param Product\Configuration\Item\OptionFactory $itemOptionFactory
     * @param \Magento\CatalogInventory\Api\Data\StockItemInterfaceFactory $stockItemFactory
     * @param Product\OptionFactory $catalogProductOptionFactory
     * @param Product\Visibility $catalogProductVisibility
     * @param Product\Attribute\Source\Status $catalogProductStatus
     * @param Product\Media\Config $catalogProductMediaConfig
     * @param Product\Type $catalogProductType
     * @param \Magento\Framework\Module\Manager $moduleManager
     * @param \Magento\Catalog\Helper\Product $catalogProduct
     * @param ResourceModel\Product $resource
     * @param ResourceModel\Product\Collection $resourceCollection
     * @param \Magento\Framework\Data\CollectionFactory $collectionFactory
     * @param \Magento\Framework\Filesystem $filesystem
     * @param \Magento\Framework\Indexer\IndexerRegistry $indexerRegistry
     * @param Indexer\Product\Flat\Processor $productFlatIndexerProcessor
     * @param Indexer\Product\Price\Processor $productPriceIndexerProcessor
     * @param Indexer\Product\Eav\Processor $productEavIndexerProcessor
     * @param CategoryRepositoryInterface $categoryRepository
     * @param Product\Image\CacheFactory $imageCacheFactory
     * @param ProductLink\CollectionProvider $entityCollectionProvider
     * @param Product\LinkTypeProvider $linkTypeProvider
     * @param \Magento\Catalog\Api\Data\ProductLinkInterfaceFactory $productLinkFactory
     * @param \Magento\Catalog\Api\Data\ProductLinkExtensionFactory $productLinkExtensionFactory
     * @param EntryConverterPool $mediaGalleryEntryConverterPool
     * @param \Magento\Framework\Api\DataObjectHelper $dataObjectHelper
     * @param \Magento\Framework\Api\ExtensionAttribute\JoinProcessorInterface $joinProcessor
     * @param array $data
     *
     * @SuppressWarnings(PHPMD.ExcessiveParameterList)
     * @SuppressWarnings(PHPMD.UnusedFormalParameter)
     */
    public function __construct(
        \Magento\Framework\Model\Context $context,
        \Magento\Framework\Registry $registry,
        \Magento\Framework\Api\ExtensionAttributesFactory $extensionFactory,
        AttributeValueFactory $customAttributeFactory,
        \Magento\Store\Model\StoreManagerInterface $storeManager,
        \Magento\Catalog\Api\ProductAttributeRepositoryInterface $metadataService,
        Product\Url $url,
        Product\Link $productLink,
        \Magento\Catalog\Model\Product\Configuration\Item\OptionFactory $itemOptionFactory,
        \Magento\CatalogInventory\Api\Data\StockItemInterfaceFactory $stockItemFactory,
        \Magento\Catalog\Model\Product\OptionFactory $catalogProductOptionFactory,
        \Magento\Catalog\Model\Product\Visibility $catalogProductVisibility,
        \Magento\Catalog\Model\Product\Attribute\Source\Status $catalogProductStatus,
        \Magento\Catalog\Model\Product\Media\Config $catalogProductMediaConfig,
        Product\Type $catalogProductType,
        \Magento\Framework\Module\Manager $moduleManager,
        \Magento\Catalog\Helper\Product $catalogProduct,
        \Magento\Catalog\Model\ResourceModel\Product $resource,
        \Magento\Catalog\Model\ResourceModel\Product\Collection $resourceCollection,
        \Magento\Framework\Data\CollectionFactory $collectionFactory,
        \Magento\Framework\Filesystem $filesystem,
        \Magento\Framework\Indexer\IndexerRegistry $indexerRegistry,
        \Magento\Catalog\Model\Indexer\Product\Flat\Processor $productFlatIndexerProcessor,
        \Magento\Catalog\Model\Indexer\Product\Price\Processor $productPriceIndexerProcessor,
        \Magento\Catalog\Model\Indexer\Product\Eav\Processor $productEavIndexerProcessor,
        CategoryRepositoryInterface $categoryRepository,
        Product\Image\CacheFactory $imageCacheFactory,
        \Magento\Catalog\Model\ProductLink\CollectionProvider $entityCollectionProvider,
        \Magento\Catalog\Model\Product\LinkTypeProvider $linkTypeProvider,
        \Magento\Catalog\Api\Data\ProductLinkInterfaceFactory $productLinkFactory,
        \Magento\Catalog\Api\Data\ProductLinkExtensionFactory $productLinkExtensionFactory,
        EntryConverterPool $mediaGalleryEntryConverterPool,
        \Magento\Framework\Api\DataObjectHelper $dataObjectHelper,
        \Magento\Framework\Api\ExtensionAttribute\JoinProcessorInterface $joinProcessor,
        array $data = []
    ) { 
```

Enter fullscreen mode Exit fullscreen mode

此外，有了 php 7.0 类型提示，长签名会更频繁地出现，您可能想知道原因是参数的数量、类型提示的长度、变量名的长度还是所有这些的组合。如果你解决了背后的真正问题，你可能会得到更多更少参数的小方法，这通常更容易推理。不要掩盖事实，注意警告，设计更好的代码。如果你的参数实际上是可互换的配置选项，Symfony 的选项解析器组件可能会有所帮助。

## 我的库使用的框架 X 禁止多行签名

您应该努力使您的库与框架无关，并将其与框架集成层隔离开来。风格是其中的一部分，使用你自己的风格，不要让糟糕的决定像疾病一样传播，尤其是如果你因为热爱 Java 而忍不住有许多带有长名称和长类型提示的参数。

## 切换到多行签名将是许多冲突的根源

我是一些不使用多行签名的库的维护者。猜猜大部分冲突发生在哪里？没错，它们发生在很长的线上，现在实际上只有长签名才会发生。所以从长远来看，这些冲突也许是值得的。

[![long lines are a recipe for git conflicts](img/feee9f7c3c0bd79bb1f18b8fe6d34afb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c-6HLk0G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgflip.com/1xv95r.jpg)

此外，考虑到切换到多行签名不必一蹴而就:只要允许切换到它们，并在它们太长时在新创建的方法上强制它们。

## 多行签名看起来很奇怪

记得切换到 PSR-2 吗？我记得，一开始不得不写
感觉很恐怖

```
<?php
if (defractulatorIsReady()) {
    defraculate();
} 
```

Enter fullscreen mode Exit fullscreen mode

而不是

```
<?php
if (defractulatorIsReady())
{
    defraculate();
} 
```

Enter fullscreen mode Exit fullscreen mode

就像我们中的一些人多年来所做的那样。但也很高兴终于有了一个人人都能认同的标准，所以我还是照着做了，感觉很恐怖…大概半天，就这样。因此，如果多行签名感觉很糟糕，请多尝试一下，不要固守旧习惯，语言在发展，你也应该这样！