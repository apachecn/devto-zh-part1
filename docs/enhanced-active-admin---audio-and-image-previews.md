# 增强的活动管理-音频和图像预览

> 原文：<https://dev.to/rob117/enhanced-active-admin---audio-and-image-previews>

# 在 Active Admin 中预览图像和音频

这是一小段代码，它扩展了 active admin，在索引、编辑和显示页面上显示图像和音频的预览。它非常简单，非常非常容易实现。

回购:[https://github.com/Rob117/active-admin-uploads](https://github.com/Rob117/active-admin-uploads)

## 为什么

我最近在做一个项目，我需要一个便携的解决方案来在 active admin 中启用图像和音频预览。管理员需要能够点击任何给定的音频片段来听它，或者预览图像来看它的完整尺寸。他们还需要在显示、索引和编辑视图中提供这些信息。这是我提取的执行该功能的代码。完整的演练在底部。

**注意**:这段代码对 Safari 不太友好，所以我强烈推荐 Chrome 或 Firefox 与 active admin 配合使用(一般来说也是)。

## 代码

```
Module EnhancedUploader
# We grab the form, then the object from the form. We try to then show the
# content of that object
  def input_audio(f, property)
    f.input property, hint: audio_content(f.object, property)
  end

  def input_image(f, property)
    f.input property, hint: image_content(f.object, property)
  end

# We simply grab an object and check to see if our property exists on it.
# If it does, display the object
  def audio_content(object, property)
    if object.persisted? && object.respond_to?(property)
      audio_tag object.send(property), controls: true
    else
      content_tag(:span, 'No Current Audio')
    end
  end

  def image_content(object, property)
    if object.persisted? && object.respond_to?(property)
      image_tag object.send(property), height: 150
    else
      content_tag(:span, 'No Current Image')
    end
  end
end 
```

### 输入解释

首先，我们将从输入表单选项开始

```
 def input_audio(f, property)
    f.input property, hint: audio_content(f.object, property)
  end

  def input_image(f, property)
    f.input property, hint: image_content(f.object, property)
  end 
```

因此，为了理解这段代码做什么，你必须理解什么是活动管理中的提示。当您在 active admin 中编辑某个对象时，它允许您给出该对象可能是什么的一些上下文。通常用**提示来指定。例如，你可以在输入表单上一个名为“信用卡”的字段旁边写一些文字，上面写着“申请只接受签证”，它会显示在输入字段的旁边。**

假设我们有一个用户资料图片，当我们在表单上编辑它时，我们希望显示它的预览。我们也有一个用户说“你好”的音频样本，我们希望可以在表单上播放。输入图像和音频的代码如下所示:

```
 # f is a form
  form do |f|
    extend EnhancedUploader
    input_image f, :profile_picture
    input_audio f, :hello_audio
  end 
```

请查看“使用”一节，以获得一个最小的、指导性的工作示例。

## 音频和图像内容方法

接下来，我们将解释以下音频和图像渲染代码的作用:

```
 def audio_content(object, property)
    if object.persisted? && object.respond_to?(property)
      audio_tag object.send(property), controls: true
    else
      content_tag(:span, 'No Current Audio')
    end
  end

  def image_content(object, property)
    if object.persisted? && object.respond_to?(property)
      image_tag object.send(property), height: 150
    else
      content_tag(:span, 'No Current Image')
    end
  end 
```

这段代码更容易解释。在每个方法中，我们接受一个对象。首先，我们检查它是否被持久化(保存到数据库中)，因为如果它没有被持久化，就没有什么可以显示或呈现——我们不能呈现我们没有的数据。

接下来，我们检查以确保对象能够真正响应我们想要的方法。试图渲染对象没有的东西并不是一个好主意！例如，在我们尝试显示 profile_photo 之前，我们应该确保模型确实有一个可以显示的 profile_photo 字段。

最后，我们简单地呈现一个 html5 兼容的图像链接或带有控件的音频播放器。show 部分的代码如下所示:

```
 show do
    extend EnhancedUploader
    attributes_table do
      row :profile_photo do |row_object|
        image_content row_object, :profile_photo
      end
      row :hello_audio do |row_object|
        audio_content row_object, :hello_audio
      end
    end
  end 
```

## 演练

### 打基础

`rails new demo`
`rails g model User name:string location:string`
T2】

现在，我们添加 active admin 并在您的 gem 文件中设计
,添加行

```
gem 'activeadmin'
gem 'devise' 
```

`bundle`
`rails generate devise:install`
`rails g active_admin:install`
`rails db:migrate db:seed`
T4】

进入`localhost:3000/admin`，用
用户名:【admin@example.com】T2T4】密码登录:密码

在 app/admin/user.rb 创建文件，并粘贴以下内容:

```
ActiveAdmin.register User do
  permit_params :name, :location
  # Show the essential data in the index
  index do
    selectable_column # we can select columns for batch actions
    column :id
    column :name
    column :location
    column :created_at
    column :updated_at
    actions
  end

  # When you click on the show for any individual item, this data is rendered
  show do
    attributes_table do # display the following attributes
      row :id
      row :name
      row :location
      row :created_at
      row :updated_at
    end
  end
  # When you click on edit, this form is rendered
  form do |f|
    f.semantic_errors
    f.inputs do
      f.input :name
      f.input :location
    end
    f.actions
  end
end 
```

转到`http://localhost:3000/admin/users`并确认我们可以创建、删除和编辑我们的基本用户。

### 添加载波波

是时候添加图像上传程序和音频上传程序了。首先，让我们包括将为我们管理数据显示的库。将以下文件移至`lib/enhanced_uploader`

+git file+

将以下内容添加到您的 gem 文件中:

```
gem 'carrierwave', '~> 1.0' 
```

`bundle`

制作两个上传器，一个用于 profile_photo，一个用于 hello_audio
`rails g uploader profile_photo`
`rails g uploader hello_audio`

将两个上传器添加到用户模型中

`rails g migration add_profile_photo_to_users profile_photo:string`
`rails g migration add_hello_audio_to_users hello_audio:string`
T2】

打开 app/models/user.rb 文件并安装上传程序

```
class User < ApplicationRecord
  mount_uploader :profile_photo, ProfilePhotoUploader
  mount_uploader :hello_audio,   HelloAudioUploader
end 
```

用以下内容替换 app/admin/user.rb(我们还没有展示增强的上传程序):

```
ActiveAdmin.register User do
  permit_params :name, :location, :profile_photo, :hello_audio
  # Show the essential data in the index
  index do
    selectable_column
    column :id
    column :name
    column :location
    column :profile_photo
    column :hello_audio
    column :created_at
    column :updated_at
    actions
  end

  # When you click on the show for any individual item, this data is rendered
  show do
    attributes_table do
      row :id
      row :name
      row :location
      row :profile_photo
      row :hello_audio
      row :created_at
      row :updated_at
    end
  end
  # When you click on edit, this form is rendered
  form do |f|
    f.semantic_errors
    f.inputs do
      f.input :name
      f.input :location
      f.inputs :profile_photo
      f.inputs :hello_audio
    end
    f.actions
  end
end 
```

我们刚刚添加了字段。**重启您的服务器**并访问您的用户页面。
如果需要的话创建一个用户。
请注意，在索引、显示和编辑时，profile_photo 和 hello_audio 字段为空。这是意料之中的——我们还没有上传任何东西！

上传一个小图片和一个小音频剪辑。

请注意，在我们上传之后，您只能查看路径名，而不能查看文件中的任何内容。

为了包含我们的“库”,我们只需要在 config/application.rb 中做一个小小的修改:

```
require_relative 'boot'
require 'rails/all'
Bundler.require(*Rails.groups)

module Blogly
  class Application < Rails::Application
    config.autoload_paths += %W(#{config.root}/lib) # Add this line!
  end
end 
```

现在，只需将 admin/user.rb 文件更改为以下代码:

```
ActiveAdmin.register User do
  permit_params :name, :location, :profile_photo, :hello_audio
  # Show the essential data in the index
  index do
    extend EnhancedUploader # include uploader
    selectable_column
    column :id
    column :name
    column :location
    column :profile_photo do |row_object|
      image_content row_object, :profile_photo
    end
    column :hello_audio do |row_object|
      audio_content row_object, :hello_audio
    end
    column :created_at
    column :updated_at
    actions
  end

  # When you click on the show for any individual item, this data is rendered
  show do
    extend EnhancedUploader # include uploader
    attributes_table do
      row :id
      row :name
      row :location
      row :profile_photo do |item|
        image_content item, :profile_photo
      end
      row :hello_audio do |item|
        audio_content item, :hello_audio
      end
      row :created_at
      row :updated_at
    end
  end
  # When you click on edit, this form is rendered
  form do |f|
    extend EnhancedUploader # include uploader
    f.semantic_errors
    f.inputs do
      f.input :name
      f.input :location
      input_image f, :profile_photo
      input_audio f, :hello_audio
    end
    f.actions
  end
end 
```

刷新您的服务器，并前往用户页面。现在，您应该可以在每个索引、显示和编辑页面上看到您上传的音频和图像的预览！成功！