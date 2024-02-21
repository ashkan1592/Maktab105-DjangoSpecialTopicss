# مبحث : meta.CONSTRAINT
### نویسنده : امیرعلی قبادی - آدرس گیتهاب :
### نویسنده : امیرعلی قبادی - آدرس گیتهاب :کانسترینت نوعی لیمیتیشن است که بر روی ستون ها در حنگو اعمال میشود و بر روی مقادیری که بر روی یک ستون می تواند ارائه شود اعمال میشود و اینجوری میتوان ولیدیشن را به ستون های جنگو اضافه کرد
انواع قید ها :
یونیک: برای اطمینان از اینکه یک مقدار در تمامی رکورد ها با یکدیگر متفاوت است
چک: اجاره میدهد خودمان یک قید بسازیم
فارن کی: بین دو مدل ارتباط برقرار میکند
برای ساختن قید چک ابتدا باید در کلاس متا یک لیست با نام کانسترینت ایجاد کنیم و بعد باید از models.checkconstraint را ایجاد کنیم که چهار مقدار درونش قرار میگیردname,check,unique,violation_error_message 
و قید بعدی models.uniquconstraint ()
است که از فیلد و name تشکیل شده است و درون فیلد باید نام ستون هایی که میخواهیم بر روی ان اعمال شود را بنویسیم


unique constraint:
meta.UniqueConstraint(*expressions, fields=(), name=None, condition=None, deferrable=None, include=None, opclasses=(), nulls_distinct=None, violation_error_code=None, violation_error_message=None)
check constraint:
models.checkconstraint(name=(),check=(),unique=(),violidation_error_message=())
