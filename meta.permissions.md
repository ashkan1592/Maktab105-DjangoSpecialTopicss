# مبحث : meta.permissions
### نویسنده : محمدرضا نژاد فلاح - آدرس گیتهاب : https://github.com/mohammadreza-n-fallah
## پرمیشن ها در جنگو
در ابتدا پیش از شروع باید به این نکته توجه کرد بدون استفاده از پرمیشن ها ما هیچ گونه کنترلی بر روی کد های خود نداریم و عملا آنها را به حال خود رها کرده ایم؛ برای شروع بیاید یک تفاوت مهم را با هم بررسی کنیم.</br>

## مجوز دادن و احراز هویت کردن(Authentication vs Authorization)
همیشه به یاد داشته باشید که این دو مورد با هم تفاوت های بسیاری دارند به طوری که Authentication روند احراز هویت و اعتبار سنجی را برای ما می سنجد  و به ما میگوید که چه کسی با چه مشخصاتی میخواهد اعتبار سنجی بشود اما در مقابل Authorization پروسه صدرو مجوز را بر عهد دارد و در واقع این بخش است که به ما اجازه می دهد که بتوانیم بعضی از کار ها را انجام بدهیم و بعضی دیگر را نه.</br>
اما بعد از اینکه کتوجه شدیم تفاوت بین احراز هویت و مجوز دادن چیه میریم سراغ اصل مطلب یعنی پرمیشن ها یا به اصطلاح خودمونی تر مجوز هایی که جنگو به ما میدهد برای شروع مطلب باید بهتون بگویم که ما چندین نوع از پرمیشن هارو داریم که عبارتند از</br>
 *  مجوز های مبتنی بر کاربر (User-level Permissions) 
 *  مجوز های مبتنی بر گروه (Group-level Permissions) 
 *  مجوز های مبتنی بر ویو (Enforcing Permissions) 
 *  مجوز های مبتنی بر مدل (Model-level Permissions) 
 *  مجوز های مبتنی بر شی (Object-level Permissions) 
  </br></br>
##  مجوز های مبتنی بر کاربر (User-level Permissions)
قبل از شروع مبحث این بخش بهتر است این موضوع را برایتان شفاف کنیم که هنگامی که شم‍ا‍‍‍‍ ``` django.contrib.auth‍‍‍ ``` را به ``` INSTALLED_APPS‍‍‍``` اضافه میکنید به صورت اتووماتیک خود جنگو متد هایی نظیر ‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍``` view ``` و``` delete ```_``` change ```_``` add ``` را به برنامتان اضافه میکنید که فرمت کلی آن هم به صورت زیر است ```{app}.{action}_{model_name} ```
</br></br>
```app```: همان نام اپلیکیشنی هست که ساخته اید</br>
```action```:همان متد های گفته شده در بالاست</br>
```model_name```:مساوی است با نام مدل</br>

بیاید یک مثال از این بخش بزنیم؛فرض کنید مدل زی را داریم:
```
from django.db import models


class Post(models.Model):
    title = models.CharField(max_length=400)
    body = models.TextField()
```
به صورت دیفالت این مدل مجوز های زیر را دارد:
1. blog.add_post
2. blog.change_post
3. blog.delete_post
4. blog.view_post

   همچنین با متدی به نام ``` has_perm``` می توان بعد از ساختن یک کاربر مشخص کرد که آن کاربر دسترسی های بالا را دادرد یا خیر؛ به مثال زیر توجه کنید:
```
from django.contrib.auth import get_user_model
from django.contrib.auth.models import User, Permission
from django.contrib.contenttypes.models import ContentType

from blog.models import Post

content_type = ContentType.objects.get_for_model(Post)
post_permission = Permission.objects.filter(content_type=content_type)
print([perm.codename for perm in post_permission])
# => ['add_post', 'change_post', 'delete_post', 'view_post']

user = User.objects.create_user(username="test", password="test", email="test@user.com")

# Check if the user has permissions already
print(user.has_perm("blog.view_post"))
# => False

# To add permissions
for perm in post_permission:
    user.user_permissions.add(perm)

print(user.has_perm("blog.view_post"))
# => False
# Why? This is because Django's permissions do not take
# effect until you allocate a new instance of the user.

user = get_user_model().objects.get(email="test@user.com")
print(user.has_perm("blog.view_post"))
# => True

```
این نکته را هم مد نظر داشته باشید که یک سوپر یوزر بر خلاف عملیاتی که کاربر های عادی باید انجام دهند همیشه به متد های بالا دسترسی دارد:
```
from django.contrib.auth.models import User

superuser = User.objects.create_superuser(
    username="super", password="test", email="super@test.com"
)

# Output will be true
print(superuser.has_perm("blog.view_post"))

# Output will be true even if the permission does not exists
print(superuser.has_perm("foo.add_bar"))

```
##  مجوز های مبتنی بر گروه (Group-level Permissions)
حال نوبت مجوز های گروهی هست؛ حتما برای شما هم پیش آمده که برای دسته ای از اشیاء ویژگی ها و متد های یکسانی را قرار دهید  و حتما هم خسته تان کرده است ما در پرمیشن ها هم همین موضوع را داریم  و به جای اینکه برای تک تک کاربر ها بیایم مجوز ها را ثبت کنیم میایم گروهی از کاربر ها را درون یک گروه قار میدهیم و برای آن گروه پرمیشن انتخاب میکنیم که در نتیجه همه کاربر ها هم آن را به ارث میبرند.بیاید یک مثال بزنیم:
</br>
فرض کنیم میخواهیم گروه هایی تشکیل بدهیم که شامل افراد زیر است:

</br>

* ``` Author```:که هم میتواند پست ها را ببیند و هم اضافه کند
* ``` Editor```:هم میتواند پست ها را ببیند هم اضافه و هم ادیت کند
* ``` Publisher```:هم میتواند پست ها را ببیند هم اضافه و هم ادیت و هم حذف کند

```
from django.contrib.auth.models import Group, User, Permission
from django.contrib.contenttypes.models import ContentType
from django.shortcuts import get_object_or_404

from blog.models import Post

author_group, created = Group.objects.get_or_create(name="Author")
editor_group, created = Group.objects.get_or_create(name="Editor")
publisher_group, created = Group.objects.get_or_create(name="Publisher")

content_type = ContentType.objects.get_for_model(Post)
post_permission = Permission.objects.filter(content_type=content_type)
print([perm.codename for perm in post_permission])
# => ['add_post', 'change_post', 'delete_post', 'view_post']

for perm in post_permission:
    if perm.codename == "delete_post":
        publisher_group.permissions.add(perm)

    elif perm.codename == "change_post":
        editor_group.permissions.add(perm)
        publisher_group.permissions.add(perm)
    else:
        author_group.permissions.add(perm)
        editor_group.permissions.add(perm)
        publisher_group.permissions.add(perm)

user = User.objects.get(username="test")
user.groups.add(author_group)  # Add the user to the Author group

user = get_object_or_404(User, pk=user.id)

print(user.has_perm("blog.delete_post")) # => False
print(user.has_perm("blog.change_post")) # => False
print(user.has_perm("blog.view_post")) # => True
print(user.has_perm("blog.add_post")) # => True


```

## مجوز های مبتنی بر ویو (Enforcing Permissions)
این مجوز ها مبتی بر کلاس ها هستند و ساختار نسبتا متفاوتی نسبت به موارد بالا هم دارند؛ ما در این گونه مجوز های class base می توانیم از PermissionRequiredMixin استفاده کنیم؛ همچنین ما می توانیم در این بخش هم از تک پرمیشن ها و هم از چندین پرمیشن به طور همزمان استفاده کنیم بیاید نمونه کد ها را با هم ببینیم:
```
from django.contrib.auth.mixins import PermissionRequiredMixin
from django.views.generic import ListView

from blog.models import Post

class PostListView(PermissionRequiredMixin, ListView):
    permission_required = "blog.view_post"
    template_name = "post.html"
    model = Post

```

```
from django.contrib.auth.mixins import PermissionRequiredMixin
from django.views.generic import ListView

from blog.models import Post

class PostListView(PermissionRequiredMixin, ListView):
    permission_required = ("blog.view_post", "blog.add_post")
    template_name = "post.html"
    model = Post

```

برای function base ها هم میتوان از دکوریتور ``` permission_required ``` استفاده کرد.
```

from django.contrib.auth.decorators import permission_required

@permission_required("blog.view_post")
def post_list_view(request):
    return HttpResponse()

```

## مجوز های مبتنی بر مدل (Model-level Permissions)
مجوز های مبتنی بر مدل هم خیلی ساده بر روی مدل ها پیاده سازی میشوند و ساختار ساده و قابل فهمی مثل کد زیر دارند:
```
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=400)
    body = models.TextField()
    is_published = models.Boolean(default=False)

```
```
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=400)
    body = models.TextField()
    is_published = models.Boolean(default=False)

    class Meta:
        permissions = [
            (
                "set_published_status",
                "Can set the status of the post to either publish or not"
            )
        ]

```

## مجوز های مبتنی بر شی (Object-level Permissions)
مجوز های مبتنی بر شی ها هم در قسمت advance  پرمیشن ها قرار دارد و اگر قرار است از REST framework در پروژه خود استفاده کنید این مجوز ها به کارتان میاید که متد هایی نظیر ``` BasePermission ``` ,``` has_permission ```, ``` has_object_permission ``` را هم دارد که در قسمت  های پیشرفته  permissions  ها باهاشان آشنا میشوید.
