# Şaplak Market

Hayko Games'in 3D sonsuz koşu oyunu. Market arabalı haylaz bir çocuk, önüne çıkana şaplak atıp güvenlikten kaçar.

Bu depo oyunu **Android uygulaması** olarak derler. Her `main` güncellemesinde GitHub otomatik olarak:

- telefona kurabileceğin bir **test APK'si**,
- imza anahtarı eklediysen Google Play'e yüklenecek **imzalı AAB** dosyası

üretir. Bilgisayarına Android Studio kurman gerekmez.

## Dosyalar

| Yol | Ne |
| --- | --- |
| `www/index.html` | Oyunun kendisi (tek dosya) |
| `www/vendor/` | 3D motoru (three.js r128), uygulama internetsiz de açılsın diye burada |
| `android/` | Capacitor ile üretilmiş Android projesi |
| `capacitor.config.json` | Uygulama adı ve paket kimliği (`com.haykogames.saplakmarket`) |
| `store/` | Play Store simgesi (512×512) ve öne çıkan görsel (1024×500) |
| `.github/workflows/android.yml` | Otomatik derleme |

## 1. Test APK'sini telefona kur

1. GitHub'da depoda **Actions** sekmesine gir, en üstteki yeşil tikli **Android derleme** çalışmasını aç.
2. Sayfanın altındaki **Artifacts** bölümünden `saplak-market-test-apk` dosyasını indir, zip'i aç.
3. APK'yi telefona at ve kur (telefon "bilinmeyen kaynak" izni isteyebilir).

## 2. İmza anahtarı (Google Play için, bir kere yapılır)

Google Play sadece imzalı paket kabul eder. Anahtarı **sen** oluşturursun, depoya **asla** koyma.

1. Anahtar oluştur. JDK ya da Android Studio kuruluysa terminalde:
   ```bash
   keytool -genkeypair -v -keystore upload.jks -alias saplak -keyalg RSA -keysize 2048 -validity 10000
   ```
   Sorulan şifreyi ve bilgileri gir. `upload.jks` dosyasını ve şifreyi güvenli bir yerde yedekle.
2. Dosyayı metne çevir:
   - Windows PowerShell: `[Convert]::ToBase64String([IO.File]::ReadAllBytes("upload.jks")) | Set-Clipboard`
   - Mac/Linux: `base64 -i upload.jks | pbcopy` (Linux: `base64 -w0 upload.jks`)
3. GitHub'da **Settings → Secrets and variables → Actions → New repository secret** ile 4 secret ekle:

   | Secret adı | Değer |
   | --- | --- |
   | `ANDROID_KEYSTORE_BASE64` | 2. adımda kopyaladığın uzun metin |
   | `ANDROID_KEYSTORE_PASSWORD` | Anahtar deposu şifresi |
   | `ANDROID_KEY_ALIAS` | `saplak` |
   | `ANDROID_KEY_PASSWORD` | Anahtar şifresi (genelde aynı şifre) |

4. **Actions → Android derleme → Run workflow** ile yeniden çalıştır. Artifacts'ta `saplak-market-google-play` çıkar; içindeki `.aab` dosyası Play Console'a yüklenir.

## 3. AdMob

Reklam birimleri `www/index.html` içinde `const ADS=` satırında:

- Ödüllü, Geçiş ve Banner kimlikleri yazılı.
- **Uygulama açıkken** kimliği boş; AdMob'da oluşturup `appOpen` alanına yaz.
- `test:false`: gerçek reklamlar gösterilir. Geliştirme sırasında deneme reklamı istersen `test:true` yap.

**Uygulama kimliği** (ortasında `~` olan) `android/app/src/main/res/values/strings.xml` içindeki `admob_app_id` satırına yazılır. Şu an orada Şaplak Market'in kendi kimliği (`ca-app-pub-8775364457894227~8540644966`) yazılı.

AdMob'da **Gizlilik ve mesajlaşma** bölümünden bir GDPR (AB onay) mesajı oluştur; oyun bu formu gerekli ülkelerde kendisi gösterir.

## 4. Reklamları kaldır satın alımı

Oyunda **Ayarlar → Reklamları kaldır** var (Google Play ile tek seferlik ödeme). Çalışması için Play Console'da:

**Para kazanma → Ürünler → Uygulama içi ürünler → Ürün oluştur**: ürün kimliği tam olarak `reklamsiz`, fiyatını belirle, etkinleştir. Bu bölüm ilk AAB yüklendikten sonra açılır. Para ödemeden denemek için **Ayarlar → Lisans testi**'ne kendi Gmail adresini ekle.

## 5. Oyunu güncellemek

`www/index.html` dosyasını yenisiyle değiştirip `main`'e gönder. Sürüm numarası her derlemede otomatik artar (`versionCode` = 100 + çalışma numarası).

## Uygulamada çalışmayanlar

Skor tablosu, haftalık turnuva ve canlı yarış claude.ai altyapısını kullanır; uygulamada "kullanılamıyor" yazar. Uygulamada da çalışmaları için Firebase gibi bir sunucuya bağlamak gerekir.
