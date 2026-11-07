# Seretân Digital Audio — güncelleme deposu

Bu klasör, GitHub'daki **herkese açık** [Tmscorptr/seretan-update](https://github.com/Tmscorptr/seretan-update) deposunun içeriğidir. Uygulamanın kodu burada
**yoktur**. Kod gizli kalır; burada yalnızca cihazların indireceği dosyalar bulunur.

| Dosya | Ne işe yarar | Cihazda nerede kullanılır |
|---|---|---|
| `radyolar.json` | İnternet radyosu listesi (`name`, `url`, `genre`) | Ayarlar → Radyo → Sunucudan güncelle |
| `fm.txt` | FM kanal listesi (`Ad \| frekans`) | Ayarlar → Radyo → FM'i sunucudan güncelle |
| `son-surum.json` | Yayındaki sistem güncellemeleri | Ayarlar → Sistem → Güncellemeleri denetle |
| Releases | `.raucb` sistem güncelleme paketleri (imzalı) | `son-surum.json` içindeki adresten indirilir |

## Cihazlar dosyaları nereden okur

`https://raw.githubusercontent.com/Tmscorptr/seretan-update/main/<dosya>`
(`server/src/config.ts` → `DEFAULT_UPDATE_BASE`; `UPDATE_BASE_URL` ortam değişkeniyle değiştirilebilir).
Depo herkese açık olduğu için ek ayar (Pages) gerekmez. GitHub bu adresi birkaç dakika önbellekte tutar;
değişiklik cihazlara en geç ~5 dakikada ulaşır.

## Dosyaları depoya yüklemek

GitHub'da depo → **Add file → Upload files** → bu klasördeki `radyolar.json`, `fm.txt`,
`son-surum.json` ve `README.md` dosyalarını sürükleyin → **Commit changes**.

## Radyo listesini güncellemek

`radyolar.json` dosyasını düzenleyip depoya gönderin. Cihazlar "Sunucudan güncelle" dendiğinde yeni
istasyonları ekler, var olanları günceller.

## Yeni sistem sürümü yayınlamak

1. Buildroot ile imzalı `.raucb` paketini üretin (ör. `seretan-cm4-0.2.0.raucb`).
2. Depo → **Releases** → yeni sürüm (`v0.2.0`) → paketi ekleyip yayınlayın.
3. `son-surum.json` dosyasına bir kayıt ekleyin (her donanım için ayrı):

```json
{
  "bundles": [
    {
      "compatible": "seretan-cm4",
      "version": "0.2.0",
      "url": "https://github.com/Tmscorptr/seretan-update/releases/download/v0.2.0/seretan-cm4-0.2.0.raucb",
      "size": 123456789,
      "sha256": "<paketin sha256 özeti>",
      "notes": "Klasörden çalma, harf atlama çubuğu"
    }
  ]
}
```

- `compatible`, imajdaki `/etc/rauc/system.conf` ile aynı olmalıdır. Cihaz yalnızca kendi donanımına
  uygun ve kurulu sürümden **daha yeni** paketi gösterir.
- `sha256` için: Windows'ta `certutil -hashfile paket.raucb SHA256`, Linux'ta `sha256sum paket.raucb`.
- Paketler imzalıdır. Depoya başkası dosya koysa bile cihaz imzasız ya da yanlış imzalı paketi kurmaz.
- Aynı paket USB bellekle de kurulabilir: dosyayı belleğin ana klasörüne koyun (internetsiz güncelleme).
