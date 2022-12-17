# 如何用 Golang 和 Ajax 上传文件？

> 原文：<https://dev.to/gcdcoder/how-to-upload-files-with-golang-and-ajax>

上传文件是我们希望在我们的网站中使用的一个常见功能，它可以用于用户的个人资料头像、图片库、封面文章等等，但是如果你想通过使用 Ajax 来完成呢？来看看我是怎么解决这个问题的。

首先让我给你项目结构:

```
public\
    api.js
    app.js
    index.html
    style.css
controllers\
    file.go
files\ 
```

然后，让我们在`public`文件夹中创建`index.html`文件，并编写以下代码:

```
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, 
    user-scalable=no, initial-scale=1.0, maximum-scale=1.0,
minimum-scale=1.0">
    <link rel="stylesheet" 
href="https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css">
    <link rel="stylesheet" href="styles.css">
    File upload using Ajax
</head>
<body>
    <form action="/upload" method="post" enctype="multipart/form-data" class="uploadForm">
        <input class="uploadForm__input" type="file" name="file" id="inputFile" accept="image/*">
        <label class="uploadForm__label" for="inputFile">
            <i class="fa fa-upload uploadForm__icon"></i> Select a file
        </label>
    </form>
    <div class="notification" id="alert"></div>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <script src="api.js"></script>
    <script src="app.js"></script>
</body>
</html> 
```

让我们添加一些基本的 CSS 样式，并在`public`文件夹:
中创建`styles.css`文件

```
 body {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100vh;    
    font-family: Verdana, Geneva, Tahoma, sans-serif;
}

body,
.uploadForm,
.uploadForm__label {
    display: flex;
    align-items: center;
    justify-content: center;
}

.uploadForm {
    width: 600px;
    max-width: 600px;
    height: 300px;
    flex-direction: column;    
    border: 2px dashed gray;
    font-family: inherit;
}

.uploadForm__input {
    display: none;
}

.uploadForm__label {
    border: 1px solid gray;
    padding: .5em 2em;
    color: deepskyblue;
    transition: transform .4s;
    flex-direction: column;
}

.uploadForm__label:hover {
    cursor: pointer;
    transform: scale(1.01);
    box-shadow: grey 2px 2px 10px;
}

.uploadForm__icon {
    font-size: 1.8em;
}

.notification {    
    display: none;    
}

.success,
.error {
    right: 30px;
    z-index: 10;
    width: 300px;
    bottom: 40px;
    padding: 1em;
    height: auto;
    text-align: center;
    display: block;
    position: fixed;
    font-family: inherit;
    animation: alert .8s forwards;
}

.notification.success {
    background: #D9EDF7;
    color: #31709C;
}

.notification.error {
    background: #F2DEDE;
    color: #B24842;
}

@keyframes alert {
    0% {
        opacity: 0;
        bottom: -40px;
    }

    100% {
        opacity: 1;
        bottom: 40px;
    }
} 
```

接下来让我们也在`public`文件夹中创建`app.js`文件，并编写以下代码:

```
(function (d, axios) {
    "use strict";
    var inputFile = d.querySelector("#inputFile");
    var divNotification = d.querySelector("#alert");

    inputFile.addEventListener("change", addFile);

    function addFile(e) {
        var file = e.target.files[0]
        if(!file){
            return
        }
        upload(file);
    }

    function upload(file) {
        var formData = new FormData()
        formData.append("file", file)
        post("/upload", formData)
            .then(onResponse)
            .catch(onResponse);        
    }

    function onResponse(response) {
        var className = (response.status !== 400) ? "success" : "error";
        divNotification.innerHTML = response.data;
        divNotification.classList.add(className);
        setTimeout(function() {
            divNotification.classList.remove(className);
        }, 3000);
    }
})(document, axios) 
```

最后，我们需要在`public`文件夹中再次创建`api.js`文件，并编写代码:

```
"use strict";

function post(url, data) {
    return axios.post(url, data)
        .then(function (response) {
            return response;
        }).catch(function (error) {
            return error.response;
        });
} 
```

好了，做完这些之后，让我们最后写一些`Go`代码。我将从在`controllers`文件夹中创建`file.go`文件开始，并编写以下内容:

```
package controllers

import (
    "fmt"
    "io/ioutil"
    "mime/multipart"
    "net/http"
)

// UploadFile uploads a file to the server
func UploadFile(w http.ResponseWriter, r *http.Request) {
    if r.Method != http.MethodPost {
        http.Redirect(w, r, "/", http.StatusSeeOther)
        return
    }

    file, handle, err := r.FormFile("file")
    if err != nil {
        fmt.Fprintf(w, "%v", err)
        return
    }
    defer file.Close()

    mimeType := handle.Header.Get("Content-Type")
    switch mimeType {
    case "image/jpeg":
        saveFile(w, file, handle)
    case "image/png":
        saveFile(w, file, handle)
    default:
        jsonResponse(w, http.StatusBadRequest, "The format file is not valid.")
    }
}

func saveFile(w http.ResponseWriter, file multipart.File, handle *multipart.FileHeader) {
    data, err := ioutil.ReadAll(file)
    if err != nil {
        fmt.Fprintf(w, "%v", err)
        return
    }

    err = ioutil.WriteFile("./files/"+handle.Filename, data, 0666)
    if err != nil {
        fmt.Fprintf(w, "%v", err)
        return
    }
    jsonResponse(w, http.StatusCreated, "File uploaded successfully!.")
}

func jsonResponse(w http.ResponseWriter, code int, message string) {
    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(code)
    fmt.Fprint(w, message)
} 
```

作为我们的最后一步，让我们在你的根目录下创建`main.go`文件，并像这样使用`file package`:

```
package main

import (
    "log"
    "net/http"

        // Note this is my path according to my GOPATH, chage it according to yours.
    "bitbucket.org/gustavocd/upload-img/controllers"
)

func main() {
    http.Handle("/", http.FileServer(http.Dir("./public")))
    http.HandleFunc("/upload", controllers.UploadFile)
    log.Println("Running")
    http.ListenAndServe(":8080", nil)
} 
```

## 备注

1.  我不处理验证(你应该做)，但它应该像魔咒一样工作。
2.  我用的是我的`GOPATH`根，请按你的换。
3.  坚持学习快乐代码:)。