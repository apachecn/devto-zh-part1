# 使用 FuctBase64 转换上传到 Base64 的文件

> 原文：<https://dev.to/sirbootoo/converting-a-file-uploaded-to-base64-using-fuctbase64-57go>

# functbase 64

将文件上传转换为 Base64

# 使用库。

## 安装

使用以下命令安装库

```
npm install fuctbase64 
```

或者

```
npm install fuctbase64 --save 
```

保存到您的 package.json。

## 用法

### 内角 2+

```
 // Imports
import { Component } from '@angular/core';

let fileUpload = require('fuctbase64');

// Component Decorator
@Component({
  selector: 'app-file-upload-to-base64',
  template: `<input type="file" name="fileInput" (change)="onFileChange($event)" #fileInput formControlName="fileUpload" />
  <br>
  <div>
    {{fileResult}}  
  </div>`
})

// Component Class
export class FileUploadToBase64Component {
  title = 'app works!';
  fileResult: any;
  constructor(){

  }

  onFileChange(event){
      let result = fileUpload(event);
      this.fileResult = result;
  }
} 
```

检查一下[扑通](https://plnkr.co/edit/PiAFMiiveFch4tee6CbL?p=preview)

### 在香草 Javascript 中

```
 <input id="the-file" name="file" type="file"> 
```

```
let fileUpload = require('fuctbase64');

var fileInput = document.getElementById('the-file');

let fileResult = fileUpload(fileInput, true);

console.log(fileResult); 
```

#### 多个文件上传

```
 <input id="the-file" name="file" type="file" multiple> 
```

```
let fileUpload = require('fuctbase64');

var fileInput = document.getElementById('the-file');

let fileResult = fileUpload(fileInput, true);

console.log(fileResult); 
```