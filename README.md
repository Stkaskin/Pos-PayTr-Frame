
C# MVC NET .FreamWork ile Pay TR Entegrasyonu :

Test amaçlı proje açılmıştır.
Projedeki Controllers altındaki PostController.cs içinden kodlara erişebilirsiniz.

![image](https://user-images.githubusercontent.com/90522945/175143695-d10e851c-50d7-4e18-a38c-5240e9d52218.png)

*****NESNELER (CLASSLAR)
Projedeki Customer(Müşteri bilgi (nesne)),Cart(Sepet içeriği(nesne)),Product(Ürün (Nesne)) bilgileri sahte nesnelerdir, projeyi test etmek için yeterlidir.
Eğer projede farklı şekilde property varsa bunları istenilenlere göre uyarlayıp kullanabilirsiniz.

  public class Customer
        {//sample 
         //örnektir
            public string userid { get; set; }
            public string mail { get; set; }
            public string adress { get; set; }
            public string name { get; set; }
            public string phonenumber { get; internal set; }
        }
       public class Cart
        {
            public int cartId { get; set; }

            public int productid { get; set; }
            public int count { get; set; }
            public Product product { get; set; }
            public object name { get; internal set; }
        }
        public class Product
        {
            public int id { get; set; }

            public int urun { get; set; }
            public object price { get; internal set; }

VİEW SAYFASI İÇİN GEREKLİ CONTROLLER HTTPGET KODLARI 

Frame olarak kullanılması uygun olan Pay Tr entegrasyoununda freame token bilgisi girmemiz gerekmekte . İlk adımımız Mvc için bir View oluşturmak .
Aşşağıdaki bilgiler Mvc için yeterli bilgilerdir. Kodların açıklaması resmin altından devam etmektedir.



        [HttpGet]
        public ActionResult Pay(string orderid)
        {
            //get current cart and customer
            //geçerli muşteriyi ve geçerli kullanıcının sepetini getir
            Customer customer =(Customer) Session["Musteri"];
            List<Cart> cart = (List<Cart>)Session["Sepet"];
            //Frame için token gerekiyor bu kod ile bilgileri gönderip token üretiyoruz
            //bu tokkeni frame urlsi olarak göndereceğiz ve frame içinde sanal pos açılacak 
            ViewBag.data = GeneratorStart(customer, cart, orderid);
            return View();
        }




string orderid //sipariş için ödeme bölümü yanı bu bölüme gelindiğinde oluşturulan orderid veritabanına sipariş girilir durumu ödenmedi olarak veritabanınıza işlenir 
daha sonra ödeme başarılı ,başarısız ,yapıldı veya yapılmadı durumları göz önünde bulundurarak işlemler gerçekleştirilir .
orderid farklı şekilde sepet oluşturma yöntemlerine başvurulur benim tercihim ödeme sayfası açıldığında sepeti veritabanına kaydedilmesi daha sonra kontrollerin sağlanması .

  Customer customer =(Customer) Session["Musteri"];// bu satırda yapılan işlem müşterinin bilgilerini almak.  Eğer müşteriniz giriş yaptıysa burayı bilgileri buraya atmanız gerekiyor , eğer giriş yapmadıysa gerekli bilgileri müşteriden talep etmeniz gerekiyor. Her iki durumda da müşteriden bilgi talep edilecektir. Müşterinin bilgilerini customer içine yazmanız gerekiyor. Bu bilgiler ödeme yapılırken pay tr tarafından  müşteriye mail ,telefon veya adres üzerinden gerekli bilgi paylaşımı yapılması için almanız gereken bilgilerdir.
  
***Müşteriden alınması gereken ve gerekli olan bilgiler*** :
*** Müşteri e-posta adresi.
*** Müşteri adresi.
*** Müşteri telefon numarası.

  List<Cart> cart = (List<Cart>)Session["Sepet"]; // bu satırda sipariş edilen ürünlerin bilgileri bulunmakta. Müşterinin seçtiği ürünleri sepete aktardığınımızı varsayarsak burada sepeti çağırıyoruz ve içeriğini cart içine atıyoruz.

   ViewBag.data = GeneratorStart(customer, cart, orderid);// Token şifrelemesi için gerekli bilgileri gönderiyoruz ve bize sönen string türündeki bilgiyi ViewBag.data içine atıyoruz. ViewBag'ın işlevi  bilgileri view sayfası için depolama işlemi görüyor. Bu şekilde view sayfasına controllerdaki veriyi rahatça aktarabiliyoruz.
  
        GeneratorStart(...); Fonksiyonumuza customer(müşteri),cart(sepet) ve orderid bilgilerini gönderiyoruz.
return View();// View sayfasını açmamız için bizi yönlendiriyor.


***************VİEW SAYFASI(Pay.cshtml)*********
Güncel script dosyalarının bilgilerini paytr dokümantasyonundan alıyoruz ve eklemesini yapıyoruz.
ViewBag.data içindeki string token bilgisini src içine yazmamız view sayfasındaki işlemlerimizi bitiryor.
Yapılan işlem  ViewBag.data içindeki tokenli linki iframe yazıp frame özelliği ile görüntüleme işlemidir.

![image](https://user-images.githubusercontent.com/90522945/175163637-b1678d31-593d-4889-9b22-a787425601b4.png)

       Frame İçin Token Oluşturma Kodları
        
  string merchant_id = "merchant_id";//PayTr tarafından verilercek bilgi
        
  string merchant_key = "merchant_key";//PayTr tarafından verilercek bilgi
        
  string merchant_salt = "merchant_salt";//PayTr tarafından verilercek bilgi
        
  string emailstr = user.mail;//Satın alan kişinin fatura veya normal olarak ödeme bilgilendirilmesi yapılacak mail hesabı
        
  int payment_amountstr = toplamtutar;//subtotal(cart);//Sepetteki ürünlerin tutar bilgisi
        
  string merchant_oid = ordernumber;//oluşturulan sipariş numarası eşsiz olmalı 
        
  string user_namestr = user.name;//müşterinin adı soyadı
        
  string user_addressstr = user.adress;//müşterinin adresi
        
  string user_phonestr = user.phonenumber;//müşterinin telefon bilgisi
        
        
  public string GeneratorStart(Customer user, List<Cart> cart, string ordernumber)
        {
            // ####################### DÜZENLEMESİ ZORUNLU ALANLAR #######################
        
            // API Entegrasyon Bilgileri - Mağaza paneline giriş yaparak BİLGİ sayfasından alabilirsiniz.
        
            string merchant_id = "merchant_id";
        
            string merchant_key = "merchant_key";
        
            string merchant_salt = "merchant_salt";
        
            // Müşterinizin sitenizde kayıtlı veya form vasıtasıyla aldığınız eposta adresi
        
            string emailstr = user.mail;
        
            // Tahsil edilecek tutar. 9.99 için 9.99 * 100 = 999 gönderilmelidir.
            int payment_amountstr = 111;//subtotal(cart);//toplamtutarr
            // Sipariş numarası: Her işlemde benzersiz olmalıdır!! Bu bilgi bildirim sayfanıza yapılacak bildirimde geri gönderilir.
            string merchant_oid = ordernumber;
            // Müşterinizin sitenizde kayıtlı veya form aracılığıyla aldığınız ad ve soyad bilgisi
            string user_namestr = user.name;
            // Müşterinizin sitenizde kayıtlı veya form aracılığıyla aldığınız adres bilgisi
            string user_addressstr = user.adress;
            // Müşterinizin sitenizde kayıtlı veya form aracılığıyla aldığınız telefon bilgisi
            string user_phonestr = user.phonenumber;
        
        
        
     string merchant_ok_url = "http://xxx.net/checkout/completed"; //Burada işlem başarılı ise işlemin başarılı bir şekilde bittiğini bildirmeniz gerekiyor . Burası ödemenin alındığı bilgisinin verildiği sayfa değildir. Veritabanı veya diğer ödeme işlemlerini burada yapmamanız gerekmektedir. Sipariş onay sayfası değildir.
      // Başarılı ödeme sonrası müşterinizin yönlendirileceği sayfa
            // !!! Bu sayfa siparişi onaylayacağınız sayfa değildir! Yalnızca müşterinizi bilgilendireceğiniz sayfadır!
            // !!! Siparişi onaylayacağız sayfa "Bildirim URL" sayfasıdır (Bakınız: 2.ADIM Klasörü).
            string merchant_ok_url = "http://xxx.net/checkout/completed";
            //
            // Ödeme sürecinde beklenmedik bir hata oluşması durumunda müşterinizin yönlendirileceği sayfa
            // !!! Bu sayfa siparişi iptal edeceğiniz sayfa değildir! Yalnızca müşterinizi bilgilendireceğiniz sayfadır!
            // !!! Siparişi iptal edeceğiniz sayfa "Bildirim URL" sayfasıdır (Bakınız: 2.ADIM Klasörü).
            string merchant_fail_url = "http://xxx.net/checkout/failedcomplete";
            //        
            // !!! Eğer bu örnek kodu sunucuda değil local makinanızda çalıştırıyorsanız
            // buraya dış ip adresinizi  (https://www.whatismyip.com/)  yazmalısınız. Aksi halde geçersiz paytr_token hatası alırsınız.
            string user_ip = "http://xxx.net/";
            //= Request.ServerVariables["HTTP_X_FORWARDED_FOR"];
            if (user_ip == "" || user_ip == null)
            {
                user_ip = Request.ServerVariables["REMOTE_ADDR"];
            }
        
        
    
    string merchant_fail_url = "http://xxx.net/checkout/failedcomplete";// Ödeme sürecinde beklenmedik bir hata oluşması durumunda müşterinizin yönlendirileceği sayfadır. Bu sayfa siparişi iptal edeceğiniz sayfa değildir! Yalnızca müşterinizi bilgilendireceğiniz sayfadır.

 string user_ip = "http://xxx.net/";//web sitenizin paytr üzerindeki linkidir. Paytr onaylı olmak zorundadır. Eğer local çalışıyorsanız ipadresiniz.com girmeniz gerekmektedir. www.whatismyip.com üzerinden öğrenebilirsiniz 
        

     
Sepetteki ürünleri istenilen 2 boyutlu object'e cevirme işlemi her foreach içinde diziye ürün adı , ürün fiyatı ve ürün adeti bilgilerini yazmanız gerekmektedir.
    oluşan listeyi user_basket'e aktarmanız gerekmektedir.
    
           
          object[][] user_basket = new object[cart.Count][];
            int i = 0;
            foreach (var item in cart)
            {
                user_basket[i] = new object[] { item.name, item.product.price, item.count };
                i++;
            }
        
        
    string test_mode = "1"; test modunda iseniz 1 girmeniz gerekiyor diğer durumlarda 0 bu aşamada 0 moduna geçerken müşteri temsilcisi ile iletişime geçebilirsiniz.
  
    string no_installment = "1";    // Taksit yapılmasını istemiyorsanız, sadece tek çekim sunacaksanız 1 yapın
        
    string currency = "TL"; para birimi
        
    string lang = ""; varsayılan tr dir boş bırakabilirsiniz
    
          /* ############################################################################################ */
            // İşlem zaman aşımı süresi - dakika cinsinden
            string timeout_limit = "30";           
            // Hata mesajlarının ekrana basılması için entegrasyon ve test sürecinde 1 olarak bırakın. Daha sonra 0 yapabilirsiniz.
            string debug_on = "1";        
            // Mağaza canlı modda iken test işlem yapmak için 1 olarak gönderilebilir.
            string test_mode = "1";
            // Taksit yapılmasını istemiyorsanız, sadece tek çekim sunacaksanız 1 yapın
            string no_installment = "1";
            // Sayfada görüntülenecek taksit adedini sınırlamak istiyorsanız uygun şekilde değiştirin.
            // Sıfır (0) gönderilmesi durumunda yürürlükteki en fazla izin verilen taksit geçerli olur.
            string max_installment = "0";
            // Para birimi olarak TL, EUR, USD gönderilebilir. USD ve EUR kullanmak için kurumsal@paytr.com 
            // üzerinden bilgi almanız gerekmektedir. Boş gönderilirse TL geçerli olur.
            string currency = "TL";
            // Türkçe için tr veya İngilizce için en gönderilebilir. Boş gönderilirse tr geçerli olur.
            string lang = "";
        

Daha sonrasına müdahale etmenize gerek yok bilgileri json haline çeviriyor.


Burada json bilgilerini paytr üzerinden haberleşmesini yapıyor ve paytr üzerinden işlem başarılı ise token adresini alıyor \n
Bizim burda dönen veriyi str içine aktarıyoruz bu veriyi frame için kullanmamız gerekmekte. Yukarda Viewbag.data ile bu bilgiyi view sayfasına taşıyoruz.

        
          using (WebClient client = new WebClient())
            {
                client.Headers.Add("Content-Type", "application/x-www-form-urlencoded");
                byte[] result = client.UploadValues("https://www.paytr.com/odeme/api/get-token", "POST", data);
                string ResultAuthTicket = Encoding.UTF8.GetString(result);
                dynamic json = JValue.Parse(ResultAuthTicket);

                if (json.status == "success")
                {

                    str = "https://www.paytr.com/odeme/guvenli/" + json.token;
                    //   paytriframe.Attributes["src"] = "https://www.paytr.com/odeme/guvenli/" + json.token;
                    //   paytriframe.Visible = true;
                }
                else
                {

                    Response.Write("PAYTR IFRAME failed. reason:" + json.reason + "");

                }
            }
            return str;
        }


***PayTr Ödeme aldığında haberleşeceği sayfa 
Paytr ödeme alırken ödeme başarılı olduğunda ödemeyi onaylayacağınız veritabanı bilgilerinizi ödeme alındı olarak gireceğiniz sayfa burasıdır.
Bu bir sayfadır çünkü burada bilgileri  aldığınızı Response.Write ile sayfaya yazmanız gerekmektedir.
Bu sayfanın method ayarları şunlardır
        
    string merchant_key = "merchant_key";//paytr'den alınan bilgi
        
    string merchant_salt = "merchant_salt";//paytr'den alınan bilgi
        
    string merchant_oid = Request.Form["merchant_oid"];//gönderdiğiniz sipariş id veritabanındaki hangi sipariş olduğunu aramanız için kullanmanız gerekebilir.
    string status = Request.Form["status"];//İşlem statüsü
        
    string total_amount = Request.Form["total_amount"];//toplam tutar
        
    string hash = Request.Form["hash"];//hash yapısı bu yapı verimizin düzgün geldiğini onaylamak için gereklidir.
        
![image](https://user-images.githubusercontent.com/90522945/175170162-1068c95a-741a-429e-ba85-5102d8428393.png)

Burası aynı kalacaktır.Gelen bilgilerden token oluşturulacak. 
    
![image](https://user-images.githubusercontent.com/90522945/175170514-fdabea52-8522-413e-9c09-4443d04e68ee.png)

    Veri Düzgün gelmediyse giden token ve gelen token birbirinden farklı ise tokenimiz bozulmuştur. Paytr tarafından geldiğine emin olmak için hash ve token kontrolü yapılıyor.
    ![image](https://user-images.githubusercontent.com/90522945/175170575-8304a74f-99f2-4c77-b0c4-f51160ced2b9.png)

Token kontrolü bittiyse ve ödeme başarılı veya başarısız (alınamadı örnek bakiye yetersiz) olduysa. iki durumda da Response.Write ile verinin geldiğini okuduğumuzu işlediğimizi bildirmemiz gerekir.
   if (status == "success") ise burada siparişin ödemesi alındı demektir ,bununla ilgili işlemler gerçekleştirmeniz gerekir (örnek veritabanında sipariş ödemesi yapıldı olarak girilir.)
    else durumunda ise siparişin ödemesi başarısızdır bu durumda ödeme başarısız sipariş silinir veya bununla ilgili işlemler gerçekleştirilir(örnek veritabanında sipariş ödemesi yapılamadı olarak girilir.)
    Ve en son olarak sayfa return ile gösterilir     

![image](https://user-images.githubusercontent.com/90522945/175171087-c845cc2f-29d9-4b17-947e-55cac51a0347.png)

    
    
![image](https://user-images.githubusercontent.com/90522945/175172312-87365db1-6fea-4b5b-9ff9-670b4d607200.png)

***paytrpayaccept View sayfası
    
![image](https://user-images.githubusercontent.com/90522945/175173391-9ee305bb-13d6-4d45-b788-2c2696abb10c.png)

***paytrpayaccept'e verinin düzgün geldiğinde yazmanız gereken ve oluşması beklenen sayfa .
        
Bu sayfa müşteri tarafından açılmaz fakat paytr haberleşmesi için gereklidir .Pay tr üzerinden sizin girdiğiniz sayfadır . Domain/belirlediğiniz (örnek :paytrpayaccept )
Sadece OK yazması gerekmektedir.
        
![image](https://user-images.githubusercontent.com/90522945/175174043-c44c5711-0e21-4261-ae1c-4eeaffc493ee.png)


Eşsiz Sipariş numarası üretmek istiyorsanız. 
Bu methodu kullanınız . Kodlarda bulunuyor
        
    private string siparisnumarasiuret(int length)
        {
            string ts = DateTime.Now.ToString("hhmmss");
            string chars = "ST123456789ABCDEFGHJKLMNOPRSTUIVYZWX";
            var random = new Random();
            return new string(Enumerable.Repeat(chars, length).Select(s => s[random.Next(s.Length)]).ToArray()) + ts;
        }

    
 



  
