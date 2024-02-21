# مبحث : Annotate

### نویسنده : پدرام کریمی - آدرس گیتهاب : `https://github.com/pedramkarimii`

# Annotate() در جنگو

## بررسی اجمالی

در جنگو، "annotate()" روشی است که برای افزودن اطلاعات اضافی به هر شیء در گروهی از اشیاء (یک مجموعه کوئری) استفاده می
شود. زمانی که نیاز به محاسبه مواردی مانند شمارش، میانگین‌ها یا اعمال منطق شرطی روی داده‌های خود دارید، مفید است.

## استفاده اولیه

فرض کنید یک مدل «محصول» با فیلدهایی مانند «نام»، «قیمت» و «رده» داریم. ما می خواهیم بدانیم در هر دسته چند محصول وجود
دارد.
### هر محصول را با تعداد محصولات در هر دسته

`
from django.db.models import Count
from myapp.models import Product
`


`
annotated_products = Product.objects.annotate(category_count=Count('category'))
`

### اکنون هر محصول اطلاعات جدیدی به نام category_count خواهد داشت که تعداد محصولات در دسته خود را نشان می دهد.

## برای مثال، اگر می‌خواهید میانگین تعداد نویسندگان در هر کتاب را محاسبه کنید، ابتدا مجموعه کتاب‌ها را با تعداد نویسنده Annotate  می‌کنید، سپس تعداد آن نویسنده را با ارجاع به قسمت aggregate()  جمع‌آوری می‌کنید:

`
from django.db.models import Avg, Count
Book.objects.annotate(num_authors=Count("authors")).aggregate(Avg("num_authors"))
{'num_authors__avg': 1.66}
`

# نکته

# ابتدا میتوانیم از  annotate()  و سپس Aggregate استفاده کنید
## برای annotate ()  به لیست[ ] و Aggregate () در { } برمی گردد

# نتیجه

## annotate () یک ابزار مفید در جنگو برای اضافه کردن اطلاعات اضافی به داده های شما است. برای کارهایی مانند شمارش یا اعمال شرایط مفید است. درک نحوه استفاده از آن، وظایف مدیریت داده های شما را بسیار آسان تر می کند!

### برای مثال های بیشتر و اطلاعات دقیق، به مستندات جنگو در annotate() مراجعه کنید.

[Django documentation](https://docs.djangoproject.com/en/5.0/topics/db/aggregation/#generating-aggregates-for-each-item-in-a-queryset).
