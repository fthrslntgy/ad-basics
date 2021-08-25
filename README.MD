Bu yazıda Active Directory Site Yapısını ayrıntılarıyla birlikte açıklamaya çalışacağız. Fakat öncelikle Active Directory ile ilgili temel kavramları ele alalım.

## 1. Temel Kavramlar

Öncelikle sunucu ve istemciden bahsedecek olursak:

**<u>Server (Sunucu)</u>:** Ağ üzerindeki istemcilere veri, kaynak ve dosya paylaşımlarını yapan hizmet sağlayıcılarına server adı verilir. Ağı yönetmek, dosya veya uygulama paylaşımı yapmak, veri tabanı barındırmak, web sayfalarıyla e-posta barındırmak gibi amaçlarla kullanılabilirler.

**<u>Client (İstemci)</u>:** Bir ağ üzerinde, sunucu bilgisayarlardan hizmet alan kullanıcı bilgisayarlarıdır. Bilgiye erişim yetkileri sunucu tarafından belirlenir.

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ggyehi0dto56ic1gvpup.png)



**Active Directory (AD)**; içerisinde sunucu, istemci, kullanıcı ve yazıcı gibi bilgileri tutan bir **dizin servisi ve veri tabanı**'dır. Kaynak kontrolünü ve merkezden yönetimi büyük oranda kolaylaştırdığından çokça tercih edilen bir servistir. Active Directory servisi, Domain Controller olarak adlandırılan sunucu veya sunucular üzerinde tutulur. Bazı Active Directory kavramlarını kısaca tanımlamak gerekirse:

**<u>Domain (Etki Alanı)</u>**: Kullanıcı ve bilgisayar hesapları gibi nesnelerin yönetimi için oluşturulan mantıksal bir alandır. Farklı iki domain arasında bilgi ve kaynak paylaşımı amacıyla kurulan mantıksal ilişkiye **güven ilişkisi (trust/domain trust)** adı verilir. 

- Domainler, diğer domainler ile alt-üst ilişkisi kurularak düzenlenebilirler. Oluşturulan ilk domain, Active Directory yapısındaki ***Kök (Root) Domain***'i ifade eder. Bu aşamadan sonra oluşturulacak ek Domain'ler dizinin Tree ve Forest yapısını oluşturur. 
- **Organizational Unit (OU)** ise domain içerisinde bulunan nesneleri (kullanıcı,grup veya bilgisayar hesapları gibi) organize etmeyi sağlayan birimlerdir. Mevcut kullanıcılarımızı bir grup içerisinde toplayıp tek bir yerden yönetmemizi kolaylaştıran Active Directory'nin olmazsa olmaz birimidir.

**<u>Tree (Ağaç)</u>**: Aynı isim altında toplanmış domainlerin hiyerarşik olarak oluşturduğu yapıdır. Aynı domain ağacı içerisinde bulunan domainler tek bir isim yapısını paylaşmaktadırlar.

**<u>Forest (Orman)</u>**: Birden fazla Tree'nin bir araya gelmiş halidir. Oluşturulan ilk domain Tree'yi oluşturur ve böylece Forest da oluşmuş olur. Bu Forest'a daha sonra eklenecek Tree'ler, Forest içerisindeki Tree ile aynı isim yapısını paylaşmasalar da aynı Global Catalog ve Schema'ya sahip olurlar.

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/stgh2y5rojv0vhmfqbse.jpg)



**<u>Global Catalog (GC)</u>**: Active Directory Forest'larındaki her bir nesnenin aranabilir bir bölümünü içeren dağıtık bir veri deposudur.

**<u>Schema (Şema)</u>**: Kullanıcı, grup, bilgisayar ve yazıcılar gibi bütün nesnelere ait bilgileri içerir. Forest içerisinde sadece bir Schema bulunur ve bütün nesne bilgileri bu Schema üzerine yazılır. Yeni oluşturulan veya içeriği değiştirilen bir nesnenin Schema içerisinde dinamik olarak güncellemesi yapılır.

**<u>Lightweight Directory Access Protocol (LDAP)</u>**: Active Directory içerisinde de kullanılan, temel olarak dizin servislerini sorgulama ve güncellemeye yarayan bir protokoldür. AD objelerine erişimde LDAP isimlendirme yöntemi kullanılır ve iki tanım içerir:

- **Distinguished Names (DN)**: AD nesnelerinin, ağ ortamında kendilerine ulaşılmasını sağlayan ve komple bir path içeren isimleridir. Örneğin: `CN=fatihpc,OU=Sistem,DC=fatih,DC=lab`

Burada kullanılan "CN" Common Name, "OU" Organizational Unit ve "DC" Domain Controller anlamına gelmektedir. Dikkat ettiysek "fatih.lab" olan domain adımızı `DC=fatih,DC=lab` şeklinde ifade ettik.

- **Relative Distinguished Name (RDN)**: DN içerisinde asıl belirtilmek istenen nesne adıdır ve nesneye ait benzersiz tanımı içerir. Yani domain içerisinde tektir. Örneğin yukarıdaki örnekte kullandığımız DN'de fatih.lab domaini içerisinde tek olan RDN  "CN=fatihpc"dir.

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/wc7dmda7yq5fhmhfdaqq.png)



Şimdiye kadar bahsettiğimiz kavramların tamamı, Active Directory'nin mantıksal yapısını oluşturmaktaydı. Bunun haricinde Active Directory'nin fiziksel yapısı da bulunmaktadır. Mantıksal yapı ile ağ kaynakları organize edilirken, fiziksel yapı ile de ağ trafiğinin kontrolü ve konfigürasyonu gerçekleştirilir. Fiziksel yapı Domain Controller ve Site'lardan oluşmaktadır.



## 2. Domain Controller (DC)

Domain Controller (Etki Alanı Denetleyicisi), üzerinde Active Directory veri tabanının bir replikasını bulunduran sunucudur. Şirket yapısındaki ağların ve sistemlerin çatısını oluşturmaktadır. Domain üzerinde yapılan herhangi bir değişiklik doğrudan Domain Controller üzerinde de gerçekleştirilir, ardından domain üzerindeki tüm Domain Controller'lar bu değişiklikleri replikasyon yolu ile kopyalarlar.

- Domain Controller'lar dizin bilgisini bulundururlar ve logon, kimlik doğrulama ve dizin arama işlemlerini yürütürler. 
- İstemcilerden gelen tüm isteklere yanıt vererek gerekli yönlendirmeleri yaparlar. 
- **Group Policy (Grup İlkesi)** sayesinde domain içerisinde güvenlik ilkeleri oluşturularak güvenlik sağlarlar.
  - Group Policy, Domain Controller üzerinden güvenlik ayarları, kısıtlamalar, standart konfigürasyonlar, yazılım dağıtımı gibi işlemleri yapmamızı sağlayan bir merkezi yönetim protokolüdür.
- Global Catalog sayesinde Domain yapısının bilgisini kendi bünyesinde barındırır.

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/k9cne22s9pcgmtpf1q2l.png)



İki farklı Domain Controller yapısından bahsedebiliriz.

- **Primary Domain Controller (Birincil Etki Alanı Denetleyicisi)**: Active Directory kurulumu yapılan ilk server bilgisayar Primary Domain Controller olarak görev alır. Bir domain ortamında yalnızca bir tane Primary DC olabilir.
- **Additional Domain Controller (Ek Etki Alanı Denetleyicisi):** Primary DC kurulumu yapıldıktan sonra Active Directory kurulumu yapılan her bir server bilgisayar Additional DC görevi yürütür. Aynı domain ortamında birden fazla Additional DC olabilir. 

Yedeklilik ve yüksek erişilirlik gibi ilkelerden dolayı bir domain üzerinde en az 3 tane DC kurulması tavsiye edilmektedir.



### 2.1 FSMO Rolleri

Domain ve Forest ortamında çeşitli görevler alan 5 adet rol bulunmaktadır. Bunlara **Flexible Single Master Operations** veya kısaca **FSMO** adı verilir. Bu 5 rol standart olarak Forest içerisindeki ilk kurulan Server'ın Domain yapısını oluşturan Primary DC üzerinde bulunmaktadır.

- **Schema Master**: Schema yapısının Active Directory üzerindeki bütün nesnelerin bilgilerini tutan yapı olduğunu yukarıda belirtmiştik. Bu yapı, sınıflardan (class) oluşur ve her bir sınıf içerisinde o sınıfa ait öznitelikler (Attribute) bulunur. Bir nesneye ait hangi bilgilerin saklanacağı bu öznitelikler ile tanımlanır. Tüm AD nesne sınıfı ve öznitelik güncellemeleri ve bunlara ait bilgiler Schema Master üzerinde tutulur. Schema Master rolü Schema'nın yönetimini yapmakla birlikte Domain Controller'lar arası tüm AD replikasyonlarını yapmakla sorumludur. Forest içerisinde tektir.
- **Domain Naming Master**: Domain içerisine giriş-çıkış yapan nesnelerin bilgilerini tutar ve yönetir. Bu rolün Global Catalog rolüne sahip bir DC'da olması tavsiye edilir. Forest içerisinde tektir.
- **PDC Emulator**: FSMO rolleri içerisinde kaynakları en yoğun kullanan roldür. Şifre değişiklikleri ve resetlemeleri, **Group Policy** ve **SYSVOL** paylaşım erişimlerini yönetir.
- **RID Master**: AD Domain yapısı içerisindeki her bir nesnenin benzersiz kimlik numarası vardır ve bu numaralara **RID (Relative Identifier)** adı verilmektedir. Ayrıca RID atanırken, AD nesnesinin oluşturduğu Domain altında ilgili Domain'in kimlik numarasıyla beraber atanır. Bu numaraya da **SID (Security Identifier)** denir. Her Domain'in kendine ait sabit bir değerde SID numarası vardır ve her atanan RID numarası bu SID numarasının altında atanır. RID Master rolü, AD ortamında oluşturulan nesnelere RID numarası atama işlemini gerçekleştirir. 
  - RID Master rolünün sahibi olan sunucuda 1 milyarın üzerinde RID numarası bulunur. RID Master olmayan DC'lerde ise bu sayı yalnızca 400 (Windows Server 16 ve sonrasında 500) ile kısıtlıdır. Bu DC'ler, bu rakam tükenmeden hemen önce RID Master rolüne sahip DC ile iletişime geçerek tekrar bir 400 tane daha RID numarası talep ederler. İşte bu işlemleri yerine getiren rol RID Master rolüdür.

- **Infrastructure Master**: Domain'ler arası bilgi transferini yapar ve güncel tutulmasını sağlar. OU'dan OU'ya taşıma (nesnenin yerinin değiştirilmesi) durumundaki Update değişikliklerinden sorumludur. Üzerindeki bilgi her daim günceldir.



## 3. Site Yapısı

Büyük bir ağ yapımız olduğunda *-örneğin kurumsal bir firmanın farklı şehirlerde veya ülkelerde hizmet verdiğini düşünelim-* farklı lokasyonlarda konumlanan Domain Controller'ların ayrıştırılmaları gerekmektedir. Bunun sebebini açıklayacak olursak, her istemcinin bulunduğu lokasyondaki Domain Controller'dan kimlik doğrulama işlemini yapması zorunludur. Uzak bir şubeden kimlik doğrulama işlemi yapacağımızı düşünürsek; bu işlemi yapacağımız DC, tek bir site yapısı (varsayılan olarak **Default-First-Site-Name**) içerisinde bulunursa kimlik doğrulama işlemi çok uzun zamanlar sürebilir. Bu sebeple kişi hangi şubeye giderse gitsin o coğrafyadaki site (lokasyon) içerisinde bulunan DC ile iletişim kurmalıdır.

Bir site, yüksek bant genişliğine sahip hatlarla bağlanmış bir veya birden çok IP alt ağlarını ifade eder. Eğer Site'ları doğru bir şekilde yapılandırırsak gerek logon gerekse replikasyon işlemlerindeki ağ trafiğini en aza indirgemiş oluruz. Site yapısının en önemli amaçlarından birisi kullanıcının logon sırasında doğru Domain Controller'ı en hızlı ve en güvenli bağlantı ile bulabilmesidir.

Elbette farklı coğrafyalardaki Domain Controller'ların veri tabanları tutarlı olmalıdır. Bunu kendi aralarındaki replikasyonlar ile sağlarlar. Bu replikasyonlar ikiye ayrılır:

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/byqztkb8skcjlnke1nea.gif)

- **Intrasite (Site İçi) Replikasyon**: Aynı Site içerisindeki Domain Controller'ların gerçekleştirdiği replikasyondur. Varsayılan olarak 15 dakikada bir gerçekleşir. **RPC (Remote Procedure Call) (Uzak Çağrı Yordamı) Over IP Protokolü** ile gerçekleştirilir. Replikasyondaki veriler sıkıştırılmadan iletilir.
- **Intersite (Site'ler Arası) Replikasyon**: Farklı Site'lar arası replikasyondur, **Bridgehead Server** adı verilen sunucular arasında gerçekleştirilir ve varsayılan olarak 3 saatte bir gerçekleşir. **RPC Over IP** (tavsiye edilir) veya **SMTP (Simple Mail Transfer Protocol)** üzerinden gerçekleştirilir. Replikasyondaki veriler sıkıştırılarak iletilir. 



### 3.1. Site Link Kavramı

**Site Link**'ler, Site'lar arasındaki fiziksel bağlantıyı temsil eder ve Site'lar arası replikasyonu ve replikasyon yönetimlerini sağlarlar. Varsayılan Site Link **DEFAULTIPSITELINK** olarak isimlendirilmiştir. 

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/m4yqmf1h370s4ig9h9yu.png)

**Site Link Bridge**'ler ise Site Link'leri birbirlerine bağlarlar. Aslında varsayılan olarak tüm Site Link'ler birbirlerine bağlı (Bridged) durumdadırlar. Ancak tüm Site'lar birbirlerine **Routing (yönlendirme)** ile erişebilir durumda değilse gereken yerlere Site Link Bridge'ler oluşturulmalıdır.

### 3.2 KCC Kavramı

Site kavramını açıklarken DC'ler arasında Intersite ve Intrasite olmak üzere iki çeşit replikasyon olduğunu belirtmiştik. **KCC (Knowledge Consistency Checker)**, 15 dakikada bir çalışarak Site içerisinde hangi DC'nin hangi DC ile replikasyon yapacağını belirleyen bir **Intrasite (Site İçi) Replikasyon Topolojisi** oluşturup bağlantı nesnelerinin otomatik olarak oluşmasını sağlar. Bu topoloji oluşturulurken DC'ler arasındaki en iyi bağlantı hesaplanır ve en iyi yolun kullanılması sağlanır.

Site içerisindeki her Domain Controller, KCC görevini üstlenir ve aynı Site içerisindeki KCC Domain Controller'lar replikasyon topolojisini oluşturmak amacıyla birbirleri ile RPC üzerinden haberleşirler.

### 3.3. ISTG Kavramı

KCC, Site içi replikasyon topolojisini oluşturduğu gibi Site'lar arası replikasyon topolojisini de oluşturmaktadır. Site'lar arası replikasyon topolojisi ile diğer Site'lardaki DC'lerdeki değişikliklerin, tüm Domain'deki DC'lerde güncel tutulması sağlanmaktadır. Site'lar arası replikasyon trafiğinde sadece **ISTG (Intersite Topology Generator)** rolündeki DC'ler görevlidir ve bunlara **Bridgehead Server** adı verilmektedir.

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/iglobfh4u2yhqk7q7cxn.jpg)

Bir Site içerisindeki en az bir DC otomatik olarak ISTG yani Bridgehead Server olarak atanır. Fakat KCC, bir Site içerisinde birden fazla ISTG atayabilir. Eğer Site içerisindeki Bridgehead Server'a ulaşılamaz ise Site içindeki **GUİD** değeri en yüksek olan DC Bridgehead Server olarak atanır. 

- **GUID (Globally Unique IDentifier)**: Bilgisayar yazılımlarında tanımlayıcı olarak kullanılan benzersiz bir referans numarasıdır. GUID terimi, genel olarak **UUID (Universally Unique IDentifier)** standardının çeşitli uygulamalarından biridir.

Bir Site içerisindeki bilgilerde değişiklik olduğunu düşünelim. Bu değişiklik öncelikle Site içerisindeki Domain Controller'larda replike olur. Ardından Bridgehead Server DC üzerinden diğer Site'lardaki Bridgehead Server DC'lar replikasyon yaparlar ve son olarak bu replikasyon bilgilerini kendi Site'ları içerisindeki diğer DC'lara replike ederler.
