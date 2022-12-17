# Azure Blob 存储元数据 400 错误请求

> 原文：<https://dev.to/rkosta/azure-blob-storage-metadata-400-bad-request-1892>

在 Azure Blob 存储中插入 Blob 时，我收到了一个 400 错误请求，因为我使用非 ASCII 字符设置元数据。

```
// Retrieve storage account from connection string. 
CloudStorageAccount storageAccount = CloudStorageAccount.Parse( CloudConfigurationManager.GetSetting("StorageConnectionString")); 

// Create the blob client. 
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient(); 

// Retrieve a reference to a container. 
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer"); 

// Create the container if it doesn't already exist.
container.CreateIfNotExists(); 

// Add some metadata to the container. 
container.Metadata.Add("docType", "textDocuments"); 
```

根据 Microsfot 文档:

*"如果任何名称/值对包含非 ASCII 字符，您将收到 400 错误请求。元数据名称/值对是有效的 HTTP 头，因此必须遵守管理 HTTP 头的所有限制。因此，建议对包含非 ASCII 字符的名称和值使用 URL 编码或 Base64 编码。*

[https://docs . Microsoft . com/en-us/azure/storage/blobs/storage-properties-metadata](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-properties-metadata)

帖子 [Azure Blob 存储元数据 400 错误请求](https://blogit.create.pt/ricardocosta/2017/11/10/azure-blob-storage-metadata-400-bad-request/)首先出现在[博客 IT](https://blogit.create.pt) 上。