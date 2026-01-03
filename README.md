# Ares v3.0.0 - Uygulama Planı

## Hedef Tanımı

Bu çalışma, Ares WPF uygulamasını v1.1.2 sürümünden v3.0.0’a yükselten kapsamlı bir yeniden düzenleme (refactor) ve özellik ekleme sürecidir. Proje kapsamı:

1. **Temizlik**: WebView2 bağımlılıklarının, kullanılmayan çalıştırılabilir dosyaların, ViewModel’lerin ve eski font referanslarının kaldırılması  
2. **Sürüm Kontrolü**: Sürüm kontrolünün v3.0.0 olacak şekilde düzeltilmesi ve GitHub URL’sinin güncellenmesi  
3. **Karşılama Akışı**: Animasyonlu, 4 adımlı ilk kullanıcı deneyiminin uygulanması  
4. **Dashboard**: 4 tema seçeneği ve dinamik gezinme içeren modern bir dashboard oluşturulması  
5. **Sayfalar**: HomePage’in yeniden inşası, Steam Plugin Center ve Online Fix işlevlerinin GamesPage ve OnlineFixPage’e taşınması  
6. **Ayarlar**: Tema seçimi, dil değiştirme, ekstralar penceresi ve sosyal medya bağlantılarının uygulanması  
7. **Profil**: Seviye sistemi ve özelleştirme içeren Steam benzeri profil sayfası oluşturulması  
8. **UI/UX**: Nevillia tasarım dilinin cam efekti (glassmorphism), animasyonlar ve premium estetikle tüm uygulamaya uygulanması  

## Kullanıcı İncelemesi Gerekli

> [!IMPORTANT]
> **Kırıcı Değişiklikler**
> - Tüm ViewModel’ler silinecek, veri bağlama (data binding) desenleri yeniden düzenlenecek  
> - MainWindow tamamen kaldırılacak ve yerine DashboardWindow gelecek  
> - WebView2 işlevselliği tamamen kaldırılacak  
> - Yeni profil sistemiyle mevcut kullanıcı verisi yapısı değişebilir  

> [!WARNING]
> **Onay Gerektiren Tasarım Kararları**
> - System_Userdata_Pack dosya formatı ve konumu projede bulunamadı – AppData altında JSON formatı varsayılacak  
> - Steam avatar tespiti Registry erişimine dayanıyor – Steam yüklü değilse başarısız olabilir  
> - Seviye sistemi mekaniği (oyun başına XP, seviye eşikleri) belirtilmedi – oyun başına basit artış kullanılacak  
> - Üyelik sistemi entegrasyonu tam belirtilmedi – placeholder oluşturulacak  

> [!CAUTION]
> **Teknik Kısıtlar**
> - Sadece saf XAML (harici UI kütüphanesi yok)  
> - .NET 8.0 WPF, Windows 10.0.19041.0 hedefi  
> - Tüm animasyonlar Cubic-Bezier (0.25, 0.1, 0.25, 1.0) easing kullanmalı  
> - Glassmorphism efektleri düzgün blur için Windows 10+ gerektirir  

---

## Önerilen Değişiklikler

### Bileşen 1: Proje Temizliği ve Yapılandırma

#### [MODIFY] [Ares.csproj](file:///c:/Users/®®Emre®®/Desktop/WinProject/Aresia/Ares/Ares%20Versions/Ares%20v3.0.0/Ares/Ares.csproj)
- `Microsoft.Web.WebView2` paket referansını kaldır (satır 171)  
- `Asre.exe` referansını kaldır (satır 176-178)  
- `Re2KG.exe` referansını kaldır (satır 191-193)  
- `Aquire.otf` font kaynağını kaldır (satır 163)  
- Sürümü 3.0.0 olarak güncelle (satır 11, 15-16)  

#### [DELETE] ViewModels/GamesViewModel.cs  
#### [DELETE] ViewModels/MainViewModel.cs  
#### [DELETE] ViewModels/SplashViewModel.cs  
#### [DELETE] Views/MainWindow.xaml  
#### [DELETE] Views/MainWindow.xaml.cs  
#### [DELETE] Views/WebViewWindow.xaml  
#### [DELETE] Views/WebViewWindow.xaml.cs  

---

### Bileşen 2: Servis Katmanı

#### [MODIFY] [Services/VersionService.cs](file:///c:/Users/®®Emre®®/Desktop/WinProject/Aresia/Ares/Ares%20Versions/Ares%20v3.0.0/Ares/Services/VersionService.cs)
- `CurrentVersion` değerini "3.0.0" yap  
- `VersionUrl`’yi "https://raw.githubusercontent.com/emrewyt/Ares/refs/heads/main/version" olarak düzelt  
- Sürüm kontrolünden önce internet bağlantısı kontrolü ekle  
- Hata mesajlarıyla birlikte düzgün exception handling ekle  

#### [NEW] Services/UserDataService.cs
- System_Userdata_Pack’i yükle/kaydet (takma ad, tercihler)  
- AppData/Local/Ares içine JSON serileştirme  

#### [NEW] Services/ThemeService.cs
- 4 yerleşik temayı yönet (Sakura, Winter, Safari, Night)  
- Özel tema yüklemelerini yönet  
- Tema renklerini dinamik uygula  

#### [NEW] Services/ProfileService.cs
- user_stats yükle/kaydet (görünen ad, açıklama, seviye, avatar, arka plan)  
- Seviye hesaplama ve ilerleme  
- 28 günlük görünen ad değiştirme bekleme süresi  

#### [NEW] Services/WelcomeService.cs
- İlk çalıştırmayı tespit et  
- coming_stats anket sonuçlarını kaydet  

#### [NEW] Services/SteamAvatarService.cs
- Registry tabanlı Steam kurulum tespiti  
- Avatar önbellek dizini taraması  
- Avatar dosyası seçimi  

#### [NEW] Services/NotificationService.cs
- Seviye atlama olayları için Windows Toast Bildirimleri  

---

### Bileşen 3: Karşılama Penceresi

#### [NEW] [Views/WelcomeWindow.xaml](file:///c:/Users/®®Emre®®/Desktop/WinProject/Aresia/Ares/Ares%20Versions/Ares%20v3.0.0/Ares/Views/WelcomeWindow.xaml)
- Frame navigasyonlu 4 sayfalı akış  
- Her yerde Nevillia animasyonları  

#### [NEW] Views/WelcomeWindow.xaml.cs
- Sayfa geçiş mantığı  
- Anket veri toplama  
- Dashboard’a geçiş  

#### [NEW] Views/Welcome/Step1AvatarPage.xaml
- Krem arka plan (#F5F5DC)  
- Fade-in animasyonlu dairesel Steam avatarı  
- Takma adla karşılama metni  
- Devam butonu  

#### [NEW] Views/Welcome/Step2SurveyPage.xaml
- Siyah arka plan  
- "Bizi nasıl buldun?" sorusu (JetBrainsMono-Light, mor)  
- 7 adet Frutiger Aero yüzen buton  
- Seçim takibi  
- coming_stats JSON’a kaydetme  

#### [NEW] Views/Welcome/Step3FarewellPage.xaml
- Sun.svg gösterimi  
- Daktilo animasyonu: "Ares'in keyfini çıkar!"  

#### [NEW] Views/Welcome/Step4MatrixPage.xaml
- Matrix yağmur efekti (01 değil, kod parçaları)  
- Son mesaj: "Did You See Aresia?" (mor, büyük, daktilo)  
- Dashboard’dan önce 3 saniye gecikme  

---

### Bileşen 4: Dashboard Penceresi

#### [NEW] [Views/DashboardWindow.xaml](file:///c:/Users/®®Emre®®/Desktop/WinProject/Aresia/Ares/Ares%20Versions/Ares%20v3.0.0/Ares/Views/DashboardWindow.xaml)
- Seçilen temaya bağlı arka plan görseli  
- Glassmorphism gezinme kenar çubuğu  
- SVG ikonlu, daraltılabilir menü  
- Sayfa navigasyonu için content frame  
- Tema tabanlı renk overlay’leri  

#### [NEW] Views/DashboardWindow.xaml.cs
- Navigasyon mantığı  
- Menü aç/kapa  
- Tema uygulama  
- Avatar tıklama → Profil sayfası  

---

### Bileşen 5: Sayfalar

#### [MODIFY] [Views/HomePage.xaml](file:///c:/Users/®®Emre®®/Desktop/WinProject/Aresia/Ares/Ares%20Versions/Ares%20v3.0.0/Ares/Views/HomePage.xaml)
- Tüm mevcut içeriği temizle  
- Zamana bağlı karşılama (Good Morning/Afternoon/Evening)  
- 8 adet premium placeholder kart (glassmorphism)  

#### [MODIFY] [Views/GamesPage.xaml](file:///c:/Users/®®Emre®®/Desktop/WinProject/Aresia/Ares/Ares%20Versions/Ares%20v3.0.0/Ares/Views/GamesPage.xaml)
- Steam Plugin Center arayüzünü taşı  
- Otomatik tespitli Steam yolu girişi  
- Oyun ID girişi ve arama  
- İndir & Kur butonu  
- Oyunu Kaldır butonu  
- Steam’i Yeniden Başlat butonu  
- Manifest dosyaları için sürükle-bırak alanı  
- Kurulu oyunlar listesi  
- Nevillia tasarımını uygula  

#### [MODIFY] [Views/OnlineFixPage.xaml](file:///c:/Users/®®Emre®®/Desktop/WinProject/Aresia/Ares/Ares%20Versions/Ares%20v3.0.0/Ares/Views/OnlineFixPage.xaml)
- Tüm mevcut içeriği temizle  
- Online Fix Downloader arayüzünü taşı  
- Aramalı oyun listesi  
- İndirme ilerlemesi  
- Manuel Steam ID girişi  
- Nevillia tasarımını uygula  

#### [MODIFY] [Views/SettingsPage.xaml](file:///c:/Users/®®Emre®®/Desktop/WinProject/Aresia/Ares/Ares%20Versions/Ares%20v3.0.0/Ares/Views/SettingsPage.xaml)
- Version.svg ile sürüm gösterimi  
- Tema önizleme grid’i (4 tema)  
- Dil açılır menüsü (TR, EN, ES, RU)  
- Ekstralar butonu → glassmorphism pencere  
  - Üyelik kalan gün sayısı  
  - Özel tema yükleme  
  - İndirme konumu seçici  
- Sosyal medya butonları (YouTube, TikTok, Instagram, Discord)  
- Icons8 atıf bağlantısı  
- Değişiklikleri Kaydet butonu → Ares_Change.mp4 oynatma  

#### [NEW] [Views/ProfilePage.xaml](file:///c:/Users/®®Emre®®/Desktop/WinProject/Aresia/Ares/Ares%20Versions/Ares%20v3.0.0/Ares/Views/ProfilePage.xaml)
- Arka plan görseli  
- Derin mor-mavi gradyan kenarlık (tam blur)  
- Steam avatarı  
- Takma ad (salt okunur)  
- Görünen ad (bekleme süresiyle düzenlenebilir)  
- Açıklama (maks. 256 karakter)  
- Seviye göstergesi (maks. 50)  
- Düzenle butonu → glassmorphism dialog  

---

### Bileşen 6: Oyun İşlevselliği (Python’dan C#’a Port)

#### [NEW] Services/ManifestService.cs
- GitHub ManifestHub’dan manifest indirme  
- ZIP çıkarma  
- Steam/config/stplug-in içine LUA dosyası kurulumu  
- Steam/config/depotcache içine manifest kurulumu  
- DLC yönetimi (marcellus.lua güncellemeleri)  

#### [NEW] Services/OnlineFixService.cs
- GitHub’dan online fix listesi çekme  
- RAR dosyalarını indirme  
- İlerleme takibi  
- Oyun adı çözümleme (Steam API + cache)  

---

### Bileşen 7: Stiller ve Kaynaklar

#### [NEW] [Styles/NevelliaStyles.xaml](file:///c:/Users/®®Emre®®/Desktop/WinProject/Aresia/Ares/Ares%20Versions/Ares%20v3.0.0/Ares/Styles/NevelliaStyles.xaml)
- Glassmorphism buton stili  
- Glassmorphism kart stili  
- Yuvarlatılmış TextBox stili  
- Özel scrollbar stili  
- Frutiger Aero balon buton stili  
- Hepsi Cubic-Bezier animasyonlu  

#### [NEW] Animations/PageTransitions.xaml
- Fade in/out  
- Slide geçişleri  
- Daktilo metin animasyonu  
- Matrix yağmur animasyonu  

---

### Bileşen 8: Uygulama Başlangıç Akışı

#### [MODIFY] [App.xaml.cs](file:///c:/Users/®®Emre®®/Desktop/WinProject/Aresia/Ares/Ares%20Versions/Ares%20v3.0.0/Ares/App.xaml.cs)
- StartupUri’yi SplashWindow olarak ayarla  

#### [MODIFY] [Views/SplashWindow.xaml.cs](file:///c:/Users/®®Emre®®/Desktop/WinProject/Aresia/Ares/Ares%20Versions/Ares%20v3.0.0/Ares/Views/SplashWindow.xaml.cs)
- Videodan önce sürüm kontrolü yap  
- Videodan önce üyelik kontrolü yap  
- Kontroller başarısız olursa hata göster  
- Video sonrası: ilk çalıştırma kontrolü  
  - İlk çalıştırma ise → WelcomeWindow  
  - Değilse → DashboardWindow  

---

## Doğrulama Planı

### Otomatik Testler

Projede şu anda mevcut birim testleri yok. UI ağırlıklı bu yeniden düzenleme nedeniyle otomatik testler sınırlı olacak.

**Eklenecek Yeni Testler:**
1. **Services.Tests/VersionServiceTests.cs**
   - Sürüm karşılaştırma mantığı testi  
   - İnternet bağlantısı yönetimi testi  
   - Çalıştır: `dotnet test`  

2. **Services.Tests/ThemeServiceTests.cs**
   - Tema yükleme testi  
   - Özel tema doğrulama testi  
   - Çalıştır: `dotnet test`  

3. **Services.Tests/ProfileServiceTests.cs**
   - Seviye hesaplama testi  
   - Görünen ad bekleme süresi testi  
   - Çalıştır: `dotnet test`  

### Manuel Doğrulama

**Build Doğrulaması:**
```powershell
cd "c:\Users\®®Emre®®\Desktop\WinProject\Aresia\Ares\Ares Versions\Ares v3.0.0\Ares"
dotnet build
