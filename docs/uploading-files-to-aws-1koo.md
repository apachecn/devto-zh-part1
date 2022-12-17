# 将文件上传到 AWS

> 原文：<https://dev.to/funkysi1701/uploading-files-to-aws-1koo>

我是 Azure 的粉丝，但今天我一直在看 AWS。特别是如何上传和下载文件。

AWS S3 将文件存储在桶中。我已经有了一个带桶的 AWS S3 帐户设置。我将假设您已经设置了一个存储桶，并专注于获取文件的代码。

第一步是使用 nuget 安装 AWS 包。在 nuget 中你想要的包叫做 AWSSDK。Core 和 AWSSDK.S3

您想要使用的 using 语句称为 Amazon。S3 和亚马逊。S3。转让，不知道为什么这不匹配 nuget，这种差异抓住了我几次。

现在来看看上传文件的代码

> Amazon S3 client AWS client = new Amazon S3 client(access keyid，secretAccessKeyID，Amazon。region endpoint . euwest 1)；
> 
> transfer utility file transfer utility = new transfer utility(AWS client)；
> 
> 使用(FileStream streamWriter =新建 FileStream(path，FileMode。open))
> 
> {
> 
> transfer utility yuploadrequest fileTransferUtilityRequest = new transfer utility yuploadrequest
> 
> {
> 
> bucket name = " flawlessimages "，
> 
> InputStream = streamWriter，
> 
> Key = fileName
> 
> }；
> T19】fileTransferUtility。上传(fileTransferUtilityRequest)；
> 
> }

让我们把它分解一下，看看它能做什么。

> Amazon S3 client AWS client = new Amazon S3 client(access keyid，secretAccessKeyID，Amazon。region endpoint . euwest 1)；

这创建了 AmazonS3Client 的一个实例，我们传递访问密钥和秘密访问密钥，这两个密钥都可以在您的 Amazon S3 帐户我的安全凭证部分找到。亚马逊。RegionEndpoint.EUWest1 指定您的 bucket 所在的 amazon 数据中心。

> transfer utility file transfer utility = new transfer utility(AWS client)；

这将使用我们在上一步中创建的 AmazonS3Client 实例创建 TransfterUtility 的一个实例。

> using(FileStream streamWriter = new FileStream(path，FileMode。打开)
> 
> {

这将从文件路径打开文件流，并指定应该打开该文件。

> TransferUtilityUploadRequest fileTransferUtilityRequest = new TransferUtilityUploadRequest
> 
> {
> 
> bucket name = " flawlessimages "、
> 
> InputStream = streamWriter、
> 
> Key = fileName
> 
> }；
> 
> fileTransferUtility。上传(fileTransferUtilityRequest)；
> 
> }

最后一步指定上传到哪个存储桶、上传什么输入流以及使用哪个键。Key 只是 AWS 引用文件的一种方式，通常被称为文件名。

这是您将文件上传到您的存储桶所需要做的全部工作。该文件将位于[https://S3-eu-west-1 . amazonaws . com/[bucket name]/[filename]](https://s3-eu-west-1.amazonaws.com/%5Bbucketname%5D/%5Bfilename%5D)中，但是默认情况下，除非您将读取权限设置为“所有人”，否则无法下载该文件，一旦您设置了读取权限，任何拥有该链接的人都可以下载您的文件。

这与您在 web 服务器上拥有的任何文件的权限级别相同，但是 AWS 有更好的方法。

> 使用(S3 client = new Amazon S3 client(access keyid，secretAccessKeyID，Amazon。region endpoint . usest 1))
> 
> {
> 
> GetPreSignedUrlRequest 1 = new GetPreSignedUrlRequest
> 
> {
> 
> bucket name = bucket name，
> 
> Key = filename，
> 
> Expires = DateTime。now . add minutes(5)
> 
> }；
> 
> urlString = s3Client。GetPreSignedURL(request 1)；
> T17】}

这里我们生成一个 url 来下载文件，但是我们指定它只在 5 分钟内有效。这意味着，如果你分享网址，它只会工作 5 分钟，之后，AWS 将给出一个拒绝访问的消息。

这比你在一个典型的 web 服务器上有更好的安全性，并且易于实现，每次用户点击一个下载链接时，你生成一个新的预先设计的 url，并将下载发送到浏览器，只要这个过程不超过 5 分钟，用户将永远不会知道。

上传文件到 AWS 的帖子[最早出现在](https://www.funkysi1701.com/2017/07/03/uploading-files-aws/) [Funky Si 的科技谈话](https://www.funkysi1701.com)上。