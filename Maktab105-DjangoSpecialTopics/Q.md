# مبحث : Q object
### نویسنده : آرمین یعقوبی - آدرس گیتهاب : https://github.com/P014r-13


### چرا اصلا باید از کیو آبجکت استفاده کنیم؟
خب شاید بهرین پرسش برای شروع همین باشه
 برای پاسخ به این پرسش باید بگم که وقتی شما می خوای کوئری های پیچیده بزنی میتونی از کیو آبجکت استفاده کنی :)<br>
 ## کیو آبجکت دقیقا چیکار میکنه؟
 یک کیو آبجکت , آبجکتی است برای محصور کردن مجموعه ای از کیورد آرگومان ها<br><br>
برای مثال :

```
from django.db.models import Q

Q(question__startswith="What")
```
<br> <br>
آبجکت های Q را می توان با عملگر های | & ^ ترکیب کرد وقتی که عملگر روی دو تا Q object اعمال میشه یک شی کاملا جدید به وجود میاره <br> <br>

برای مثال :

```
Q(question__startswith="Who") | Q(question__startswith="What")
```
کد بالا برابر با عبارت sql WHERE زیر است :
```
WHERE question LIKE 'Who%' OR question LIKE 'What%'
```

هر فانکشن سرچی که کیورد آرگومان میگیره میتونه چندین Q object به عنوان آرگومان های بدون نام ارسال بشه <br>
اگر چندین Q object رو برای یک فانکشن سرچ ارسال کنید روشون AND اعمال میکنه <br> <br>

برای مثال :


```
Poll.objects.get(
    Q(question__startswith="Who"),
    Q(pub_date=date(2005, 5, 2)) | Q(pub_date=date(2005, 5, 6)),
)
```

همچنین شما میتوانید با استفاده از ~ اون Q object رو منفی کنید و کار NOT رو براتون انجام میده
<br> <br>
مثال:

```
Q(question__startswith="Who") | ~Q(pub_date__year=2005)
```

مطالعه بیشتر : <br>
https://micropyramid.medium.com/querying-with-django-q-objects-56ccca6c0563 <br>
https://www.scaler.com/topics/django/q-objects-and-f-objects/ <br>
