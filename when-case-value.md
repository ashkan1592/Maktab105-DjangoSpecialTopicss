 # When, Case, Value in Django
### نویسنده : مرجان جباریانی - آدرس گیتهاب : https://github.com/MarjanJab







## When تعریف
What is a when?
- برای کپسوله کردن یک شرط و نتیجه آن برای استفاده در عبارت .شرطی استفاده می شود. when شی 
-   است. filter مشابه استفاد از روش  when شی 
- دارد boolean-feild دارد که یک   output_feild که یک Expression یا شی  "Q" گزاره شرطی می تواند با استفاده از جستجوی فیلد شی  
- - مشخص می شود then نتیجه با استفاده از کلمه کلیدی 



## Why Use When?

- ها استفاده نمود caseمی توان در  output_feild  از نتیجه بازگردانده شده توسط  



## Example - when usage



```python
>>> from django.db.models import F, Q, When
>>> # String arguments refer to fields; the following two examples are equivalent:
>>> When(account_type=Client.GOLD, then="name")
>>> When(account_type=Client.GOLD, then=F("name"))
>>> # You can use field lookups in the condition
>>> from datetime import date
>>> When(
...     registered_on__gt=date(2014, 1, 1),
...     registered_on__lt=date(2015, 1, 1),
...     then="account_type",
... )
>>> # Complex conditions can be created using Q objects
>>> When(Q(name__startswith="John") | Q(name__startswith="Paul"), then="name")
>>> # Condition can be created using boolean expressions.
>>> from django.db.models import Exists, OuterRef
>>> non_unique_account_type = (
...     Client.objects.filter(
...         account_type=OuterRef("account_type"),
...     )
...     .exclude(pk=OuterRef("pk"))
...     .values("pk")
... )
>>> When(Exists(non_unique_account_type), then=Value("non unique"))
>>> # Condition can be created using lookup expressions.
>>> from django.db.models.lookups import GreaterThan, LessThan
>>> When(
...     GreaterThan(F("registered_on"), date(2014, 1, 1))
...     & LessThan(F("registered_on"), date(2015, 1, 1)),
...     then="account_type",
... )
```

در این مثال
برای دسته بندی کاربران در گروه های مختلف و ارائه تخفیف های منتاظر استفاده شده است.


## Case تعریف

- در پایتون است if ... elif... else شبیه به  گزاره های  case یک عبارت 
- ارائه شده به تریتیب ارزیابی می شود،  تا زمانی که یکی از آن ها به یک مقدار صحیح برسد when هر شرط 
- مطابق با شرط مطابقت دار برگردانده می شود  when عبارت نتیجه از شی 


## Example - case usage
```python
>>>
>>> from datetime import date, timedelta
>>> from django.db.models import Case, Value, When
>>> Client.objects.create(
...     name="Jane Doe",
...     account_type=Client.REGULAR,
...     registered_on=date.today() - timedelta(days=36),
... )
>>> Client.objects.create(
...     name="James Smith",
...     account_type=Client.GOLD,
...     registered_on=date.today() - timedelta(days=5),
... )
>>> Client.objects.create(
...     name="Jack Black",
...     account_type=Client.PLATINUM,
...     registered_on=date.today() - timedelta(days=10 * 365),
... )
>>> # Get the discount for each Client based on the account type
>>> Client.objects.annotate(
...     discount=Case(
...         When(account_type=Client.GOLD, then=Value("5%")),
...         When(account_type=Client.PLATINUM, then=Value("10%")),
...         default=Value("0%"),
...     ),
... ).values_list("name", "discount")
<QuerySet [('Jane Doe', '0%'), ('James Smith', '5%'), ('Jack Black', '10%')]>
```
در این مثال ابتدا کاربرهای مختلف تعریف شده و سپس با توجه به تاریخ عضویت آن ها مقادیر برگردانده شده است.
## نتیجه

- ها در پایتون می باشد. switch-case ها همانند  case عملکرد 
- بازگرداند valueآن را به عنوان  boolean_feild استفاده نمود و سپس نتیجه  whenها می بایست ابتدا از  case برای استفاده از 
