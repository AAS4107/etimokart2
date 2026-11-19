# Etimokart - Android

Bu klasör masaüstü oyunun Android (Kivy) sürümüdür. APK'yı bu ortamda derleyemedim,
aşağıdaki yollardan biriyle sen derleyeceksin.

## Dosyalar
- `main.py`       arayüz (Kivy)
- `core.py`       kayıt, giriş, oyun kuralları, mağaza, site bağlantısı
- `wordlists.py`  kelime listeleri
- `buildozer.spec` APK ayarları

## Yol 1 - GitHub ile (bilgisayara bir şey kurmadan, önerilen)
1. github.com'da yeni (özel olabilir) bir depo aç.
2. Bu klasörün içindekileri (`.github` klasörü dahil) depoya yükle.
3. Depoda **Actions** sekmesi -> **APK derle** -> **Run workflow**.
4. 15-30 dakika sonra bitince çalışmanın sayfasındaki **Artifacts** bölümünden `etimokart-apk` dosyasını indir.
5. İndirdiğin zip'in içindeki `.apk` dosyasını telefona at, aç. (Telefonda "bilinmeyen kaynaklardan yükleme" izni gerekir.)

## Yol 2 - Linux / WSL
```
sudo apt install -y python3-pip git zip unzip openjdk-17-jdk autoconf libtool pkg-config zlib1g-dev libncurses-dev cmake libffi-dev libssl-dev
pip install buildozer cython
buildozer android debug
```
APK `bin/` klasörüne çıkar. İlk derleme uzun sürer.

## Supabase
Masaüstü sürümle aynı projeyi kullanır (`core.py` içindeki `SB_URL` / `SB_KEY`).
`supabase_kurulum.sql` dosyasının çalıştırılmış olması gerekir.

## AAB (Google Play'e yüklemek için)
Play Store APK değil **AAB** ister. İki şey gerekir:

1. **İmza anahtarı (keystore) oluştur** (bir kere, bilgisayarında):
   ```
   keytool -genkeypair -v -keystore release.keystore -alias etimokart -keyalg RSA -keysize 2048 -validity 10000
   ```
   Bu dosyayı ve şifreleri **kaybetme**; sonraki güncellemeler aynı anahtarla imzalanmak zorunda.
   (Play App Signing kullanırsan yüklemede ayrıca sorulur.)
2. GitHub deposunda **Settings -> Secrets and variables -> Actions** altına şu 4 secret'ı ekle:
   - `KEYSTORE_BASE64`: `base64 -w0 release.keystore` çıktısı (Windows PowerShell: `[Convert]::ToBase64String([IO.File]::ReadAllBytes("release.keystore"))`)
   - `KEYSTORE_PASSWORD`: keystore şifresi
   - `KEY_ALIAS`: `etimokart`
   - `KEY_PASSWORD`: anahtar şifresi

Sonra **Actions -> AAB derle (Google Play) -> Run workflow**. Bitince **Artifacts** içinden `etimokart-aab` indir, içindeki `.aab` dosyasını Play Console'a yükle.

Linux/WSL'de elle: `P4A_RELEASE_KEYSTORE=... P4A_RELEASE_KEYSTORE_PASSWD=... P4A_RELEASE_KEYALIAS=... P4A_RELEASE_KEYALIAS_PASSWD=... buildozer android release`
