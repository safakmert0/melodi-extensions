# Melodi Extensions

Melodi için merkeziyetsiz eklenti deposu (SpotiFLAC tarzı). Kullanıcılar
uygulamada **Ayarlar → Kaynaklar → Eklenti mağazası** bölümüne bu deponun
`registry.json` bağlantısını yapıştırır ve sağlayıcıları tek dokunuşla kurar.
Sunucu IP/adresi girmek gerekmez; adresler manifest içinde taşınır.

## Resmî depo bağlantısı

```
https://raw.githubusercontent.com/safakmert0/melodi-extensions/main/registry.json
```

Bu adres Melodi uygulamasına varsayılan olarak gömülüdür
(`lib/services/extension_service.dart` → `officialRepoUrl`).

## Depo yapısı

```
melodi-extensions/
├── registry.json                  # Depo dizini: ad + manifest bağlantıları
└── extensions/
    ├── melodi-public-ytdlp.json   # backend türü eklenti manifesti
    └── melodi-hifi-lossless.json  # hifi (lossless) türü eklenti manifesti
```

## registry.json şeması

```json
{
  "name": "Depo adı",
  "apiVersion": 1,
  "updatedAt": "ISO-8601 tarih",
  "extensions": [
    {
      "id": "saglayici.kimligi",
      "name": "Görünen ad",
      "kind": "backend | hifi",
      "version": "1.0.0",
      "author": "yazar",
      "description": "Kısa açıklama",
      "url": "extensions/manifest.json"
    }
  ]
}
```

`url` alanı mutlak (`https://…`) veya depoya göreli yol olabilir.

## Manifest şeması

```json
{
  "id": "saglayici.kimligi",
  "name": "Görünen ad",
  "kind": "backend | hifi",
  "version": "1.0.0",
  "author": "yazar",
  "description": "Kısa açıklama",
  "baseUrl": "https://sunucu-adresi",
  "homepage": "https://…",
  "minAppVersion": "4.2.0",
  "capabilities": ["search", "playback", "downloads", "lossless"]
}
```

| Alan           | Zorunlu | Açıklama                                                        |
| -------------- | ------- | --------------------------------------------------------------- |
| `id`           | ✓       | Benzersiz kimlik (`a-z0-9._-`)                                  |
| `name`         | ✓       | Uygulama içi görünen ad                                         |
| `kind`         | ✓       | `backend` → yt-dlp motoru, `hifi` → lossless sunucu             |
| `baseUrl`      | ✓       | http(s) uç nokta; sonda `/` olmamalı                            |
| `capabilities` | –       | Kaynak kartında gösterilen yetenekler                           |

### Tür davranışı

- **backend**: Adres `BackendApiService`'e enjekte edilir (`/api/search`,
  `/api/info`, `/api/download`, `/api/playlist` uç noktaları beklenir).
- **hifi**: Adres `HiFiSource`'a enjekte edilir (`/api/hifi/search`,
  `/api/hifi/download`, `/api/library/search` uç noktaları beklenir).

Etkin eklenti yoksa uygulama kullanıcının manuel girdiği (gelişmiş) adrese
dönüş yapar. Aynı türden birden fazla eklenti kurulursa listedeki sıra
önceliği belirler.

## Yeni eklenti yayınlama

1. `extensions/<id>.json` manifest dosyasını ekleyin.
2. `registry.json` içine kaydı ekleyin ve `updatedAt`'ı güncelleyin.
3. `main` dalına push edin — uygulamada "Depoları yenile" yeterlidir.
