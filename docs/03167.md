# 自动化 Unity3D 资产导入

> 原文：<https://dev.to/homerdalords/automating-unity3d-assets-import-908>

游戏并不总是由一个人的团队制作，而是涉及几个开发人员，他们类似于犯错误的人，这反过来增加了开发成本时间。导入资产可不是闹着玩的，默认设置和项目不同部分的不同规则，让我们从这个前提开始，问问我们自己:“我们能编写工具来防止常见的、代价高昂的错误吗？”

答案是肯定的，让我们发现一个很酷的 Unity3D 类。

**AssetPostprocessor:**

AssetPostProcessor 类在导入内容时接收回调。它实现 OnPreProcess*和 OnPostProcess*方法，并将您的规则应用于 assetImporter 实例。因此，我们可以编写规则来防止一些常见错误，以纹理为例，我们可以确保:

*   设置纹理类型(默认、精灵等..)
*   确保读/写被禁用。
*   禁用任何 2D 或 UI 的 mipmaps(默认情况下启用)。
*   强制最大纹理大小

```
public class AssetImportSample : AssetPostprocessor {

    void OnPreprocessTexture()
    {
        if (assetPath.Contains("_UI") || assetPath.Contains("_Sprite"))
        {
            TextureImporter textureImporter = (TextureImporter)assetImporter;
            textureImporter.textureType = TextureImporterType.Sprite;
            textureImporter.spriteImportMode = SpriteImportMode.Multiple;
            textureImporter.mipmapEnabled = false; // we don't need mipmaps for 2D/UI Atlases

            if (textureImporter.isReadable)
            {
                textureImporter.isReadable = false; // make sure Read/Write is disabled
            }
            textureImporter.maxTextureSize = 1024; // force a max texture size
            Debug.Log("UI/Sprite Audit Complete");
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在有了上面的脚本，团队是否更容易遵守命名约定，而不是项目不同部分的 cherrypick 设置。

我们还可以为网格实现相同的 onpremodel()方法来审计一些常见错误:

确保读/写被禁用
禁用非角色模型上的装备(它修复自动添加的动画组件)
使用共享装备复制角色的化身
启用网格压缩

当然，这也适用于音频。