# 弹性电子邮件地址验证组件

> 原文：<https://dev.to/kritner/flex-email-address-validation-component-3k3>

我试图为 flex - one 找到一个体面的电子邮件验证组件，内置验证器和所有的附加功能。我不能一眼就找到一个，所以我把这个家伙放在一起，到目前为止它看起来实际上是工作的:O

```
\<?xml version="1.0" encoding="utf-8"?\>
\<!--
 Created By: Russ Hammett
 Create Date: 2012-01-03
 Description: Text input that validates an email address.
 use "required" to control if the email address
 is required.

 access the public variable isValidEmail to determine
 if validation passed on the email address
 Modified By: Russ Hammett
 Modify Date: 2012-01-06
 Modify Desc: Updated validateEmail function to be public and
 update errorString accordingly when validation fails or passes.
--\>
\<mx:TextInput xmlns:mx="http://www.adobe.com/2006/mxml"
 maxChars="50" 
 creationComplete="onCreationCompleted();"
 focusOut="validateEmail();" \>

 \<mx:Script\>
 \<![CDATA[
 import mx.events.ValidationResultEvent;

 private var \_isValidEmail:Boolean = true;

 public function set isValidEmail(value:Boolean):void
 {
 \_isValidEmail = value; 
 }
 [Bindable]
 public function get isValidEmail():Boolean
 {
 return \_isValidEmail;
 } 

 [Bindable]
 public var required:Boolean = false;

 /\*\*
 \* On Creation Completed
 \* \*/
 private function onCreationCompleted():void
 {
 if (required)
 {
 emailValidator.enabled = true;
 }
 else
 {
 emailValidator.enabled = false; 
 }

 }

 /\*\*
 \* Validate Email
 \* \*/
 public function validateEmail():void
 {

 var vldEmail:ValidationResultEvent;

 if (this.length \> 0)
 {
 emailValidator.enabled = true;
 vldEmail = emailValidator.validate(this.text);

 if (vldEmail.type == ValidationResultEvent.VALID)
 {
 isValidEmail = true;
 this.errorString = '';
 }
 else
 {
 isValidEmail = false;
 this.errorString = vldEmail.message;
 }

 }
 else
 {
 emailValidator.enabled = false;

 if (required)
 {
 emailValidator.enabled = true;
 vldEmail = emailValidator.validate(this.text);

 isValidEmail = false;
 this.errorString = vldEmail.message;
 }
 else
 {
 isValidEmail = true;
 this.errorString = '';
 }
 }
 }

 ]]\>
 \</mx:Script\>

 \<mx:EmailValidator 
 id="emailValidator" 
 source="{this}"
 property="text"
 required="{required}"
 requiredFieldError="Email Field is Required."
 invalidCharError="Invalid characters in your email address." 
 invalidDomainError= "The domain in your email address is incorrectly formatted." 
 invalidIPDomainError="The IP domain in your email address is incorrectly formatted." 
 invalidPeriodsInDomainError="The domain in your email address has consecutive periods." 
 missingAtSignError="Missing an at character in your email address." 
 missingPeriodInDomainError="The domain in your email address is missing a period." 
 missingUsernameError="The username in your email address is missing." 
 tooManyAtSignsError="Too many at characters in your email address." /\> 

\</mx:TextInput\> 
```

感谢 http://www . blogger central . com/2009/04/how-to-show-code-in-blog-post . html # free 教我如何在 blogger 中粘贴代码(并使其看起来更漂亮)..