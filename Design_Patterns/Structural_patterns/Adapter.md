آداپتر پترن در اصل یک دیزاین پترن از نوع Structural است که به شما این امکان را می‌دهد تا یک کلاس را با اینترفیس متفاوتی نیز سازگار کنید؛ این کار امکان استفاده از کلاس توسط سیستمی که از متدهای فراخوانی مختلفی استفاده می‌کند را امکان‌پذیر می‌نماید.

یک آداپتور به دو رابط ناسازگار اجازه می‌دهد تا بتوانند با هم کار کنند. این یک تعریف کلی از مفهوم آداپتور است. ممکن است رابط ها ناسازگار باشند ولی قابلیت درونی آنها باید سازگار با نیاز باشد. الگوی طراحی آداپتور از طریق تبدیل رابط یک کلاس به رابط مورد انتظار توسط کلاینت، به کلاس‌های ناسازگار اجازه می‌دهد تا بتوانند از قابلیت‌های همدیگر استفاده کنند.

# مثال: سرویس وب قدیمی
class OldWebService:
    def request(self):
        return "request form old service"

# رابط جدید
class NewWebService:
    def request_new(self):
        return "request from new service"


class WebServiceAdapter:
    def __init__(self, new_service):
        self.new_service = new_service

    def request(self):
        return self.new_service.request_new()


def main():
  
    old_service = OldWebService()
    print(old_service.request())
 
    new_service = NewWebService()
    adapter = WebServiceAdapter(new_service)
    print(adapter.request())

if __name__ == "__main__":
    main()
