# “如何在 Django 1.11 中使用电子邮件作为用户标识符”

> 原文：<https://dev.to/ariera/how-to-use-email-as-user-identifier-in-django-111>

 <small>*[(本帖原载于 ariera . github . io)](https://ariera.github.io/2017/09/20/how-to-use-email-as-user-identifier-in-django-1-11.html)*</small> 

我找到了几个关于如何做到这一点的指南和提示。它们都非常鼓舞人心和有帮助，但我不得不自己想一些事情，因为它们都没有提供我需要的一切:一个完全工作的管理面板，复制原始面板的所有功能。

我不会停下来解释每一行都在做什么，最后我只是想在将来给自己省点麻烦，但我分享它是因为它可能对其他人也有用。在写这篇文章的时候，我正在使用`Django==1.11.5`。

#### 出发前

请记住，您需要在项目生命周期的开始，在运行任何迁移之前完成这项工作，所以如果您已经完成了那么多，那么就删除并创建您的 DB。我会在我的本地机器上用 postgres:
来做这件事

```
pg_ctl -D /usr/local/var/postgres restart
dropdb my_app
createdb my_app 
```

Enter fullscreen mode Exit fullscreen mode

#### 首先创建你的新应用

因为这将是一个非常通用的、项目范围的应用程序，我喜欢称它为`base`。我可能会把其他的基本模型/视图放在这里。

```
python manage.py startapp base 
```

Enter fullscreen mode Exit fullscreen mode

#### 配置您的设置

。/my_app/settings.py

```
# ./my_app/settings.py
AUTH_USER_MODEL = 'base.User'
INSTALLED_APPS = [
    'base.apps.BaseConfig',
    # ...
] 
```

Enter fullscreen mode Exit fullscreen mode

#### [T3 配置你的`models`和`admin`](#configure-your-raw-models-endraw-and-raw-admin-endraw-)

这将是你的模型，从最初的 Django 源代码得到高度启发。

。/base/models.py

```
from django.db import models
from django.contrib.auth.models import AbstractBaseUser
from django.contrib.auth.models import BaseUserManager
from django.contrib.auth.models import PermissionsMixin
from django.utils.translation import ugettext_lazy as _

class MyUserManager(BaseUserManager):
    """
    A custom user manager to deal with emails as unique identifiers for auth
    instead of usernames. The default that's used is "UserManager"
    """
    def _create_user(self, email, password, **extra_fields):
        """
        Creates and saves a User with the given email and password.
        """
        if not email:
            raise ValueError('The Email must be set')
        email = self.normalize_email(email)
        user = self.model(email=email, **extra_fields)
        user.set_password(password)
        user.save()
        return user

    def create_superuser(self, email, password, **extra_fields):
        extra_fields.setdefault('is_staff', True)
        extra_fields.setdefault('is_superuser', True)
        extra_fields.setdefault('is_active', True)

        if extra_fields.get('is_staff') is not True:
            raise ValueError('Superuser must have is_staff=True.')
        if extra_fields.get('is_superuser') is not True:
            raise ValueError('Superuser must have is_superuser=True.')
        return self._create_user(email, password, **extra_fields)

class User(AbstractBaseUser, PermissionsMixin):
    email = models.EmailField(unique=True, null=True)
    is_staff = models.BooleanField(
        _('staff status'),
        default=False,
        help_text=_('Designates whether the user can log into this site.'),
    )
    is_active = models.BooleanField(
        _('active'),
        default=True,
        help_text=_(
            'Designates whether this user should be treated as active. '
            'Unselect this instead of deleting accounts.'
        ),
    )
    is_superuser = models.BooleanField(default=False, help_text='Designates that this user has all permissions without explicitly assigning them.', verbose_name='superuser status')
    created_at   = models.DateTimeField(auto_now_add=True)
    updated_at   = models.DateTimeField(auto_now=True)
    first_name   = models.CharField(blank=True, max_length=30, verbose_name='first name')
    last_name    = models.CharField(blank=True, max_length=30, verbose_name='last name')

    USERNAME_FIELD = 'email'
    objects = MyUserManager()

    def __str__(self):
        return self.email

    def get_full_name(self):
        return self.email

    def get_short_name(self):
        return self.email 
```

Enter fullscreen mode Exit fullscreen mode

这是管理文件，也是从最初的 Django 源代码中得到很大启发

。/base/admin.py

```
from django import forms
from django.contrib import admin
from django.contrib.auth import password_validation
from django.contrib.auth.admin import UserAdmin as BaseUserAdmin
from django.contrib.auth.forms import ReadOnlyPasswordHashField
from django.contrib.auth.forms import UserChangeForm as BaseUserChangeForm
from django.utils.translation import gettext, gettext_lazy as _
from .models import User

class UserCreationForm(forms.ModelForm):
    """A form for creating new users. Includes all the required
    fields, plus a repeated password."""
    password1 = forms.CharField(
        label=_("Password"),
        strip=False,
        widget=forms.PasswordInput,
        help_text=password_validation.password_validators_help_text_html(),
    )
    password2 = forms.CharField(
        label=_("Password confirmation"),
        widget=forms.PasswordInput,
        strip=False,
        help_text=_("Enter the same password as before, for verification."),
    )

    class Meta:
        model = User
        fields = ('email', 'is_staff', 'is_active', 'first_name', 'last_name', )

    def clean_password2(self):
        # Check that the two password entries match
        password1 = self.cleaned_data.get("password1")
        password2 = self.cleaned_data.get("password2")
        if password1 and password2 and password1 != password2:
            raise forms.ValidationError("Passwords don't match")
        return password2

    def save(self, commit=True):
        # Save the provided password in hashed format
        user = super().save(commit=False)
        user.set_password(self.cleaned_data["password1"])
        if commit:
            user.save()
        return user

class UserChangeForm(BaseUserChangeForm):
    """
    Override as needed
    """

class UserAdmin(BaseUserAdmin):
    # The forms to add and change user instances
    form = UserChangeForm
    add_form = UserCreationForm

    # The fields to be used in displaying the User model.
    # These override the definitions on the base UserAdmin
    # that reference specific fields on auth.User.
    list_display = ('email', 'is_superuser', 'is_staff', 'is_active', 'first_name', 'last_name', 'created_at', 'updated_at', 'last_login')
    # list_filter = ('email',)

    readonly_fields=('created_at', 'updated_at',)
    fieldsets = (
        (None                , {'fields': ('email', 'password')}),
        (_('Personal info')  , {'fields': ('first_name', 'last_name',)}),
        (_('Permissions')    , {'fields': ('is_active', 'is_staff', 'is_superuser', 'groups', 'user_permissions')}),
        (_('Important dates'), {'fields': ('last_login', 'created_at', 'updated_at')}),
    )
    # add_fieldsets is not a standard ModelAdmin attribute. UserAdmin
    # overrides get_fieldsets to use this attribute when creating a user.
    add_fieldsets = (
        (None, {
            'classes': ('wide',),
            'fields': ('email', 'password1', 'password2')}
        ),
    )
    search_fields = ('email',)
    ordering = ('email',)
    # filter_horizontal = ()

admin.site.register(User, UserAdmin) 
```

Enter fullscreen mode Exit fullscreen mode

#### 应用修改

最后，您需要生成您的迁移并进行迁移

```
python manage.py makemigrations base
python manage.py migrate
python manage.py createsuperuser 
```

Enter fullscreen mode Exit fullscreen mode

#### 参考文献

*   [https://docs . django project . com/en/1.11/topics/auth/customizing/# substituting-a-custom-user-model](https://docs.djangoproject.com/en/1.11/topics/auth/customizing/#substituting-a-custom-user-model)
*   [https://docs . django project . com/en/1.11/topics/auth/customizing/# a-full-example](https://docs.djangoproject.com/en/1.11/topics/auth/customizing/#a-full-example)
*   [https://medium . com/@ ramykhufash/django-authentic ation-with-just-an-email-and-password-no-username-required-33e 47976 b517](https://medium.com/@ramykhuffash/django-authentication-with-just-an-email-and-password-no-username-required-33e47976b517)
*   [https://stack overflow . com/questions/3967644/django-admin-how-to-display-a-field-that-is-marked-to-editable-false-in-the-mo/3967891 # 3967891](https://stackoverflow.com/questions/3967644/django-admin-how-to-display-a-field-that-is-marked-as-editable-false-in-the-mo/3967891#3967891)