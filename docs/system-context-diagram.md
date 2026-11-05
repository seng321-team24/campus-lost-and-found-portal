# Sistem Bağlam Diyagramı ve Dış Sistem Etkileşimleri

**Proje:** Kampüs Kayıp Eşya Portalı (Lost and Found Management System)
**Hazırlayan rol:** Lead Software Architect (K3)

## 1. Sistem Bağlam Diyagramı

```mermaid
flowchart LR
    S["Kampüs Kayıp Eşya Portalı<br/>(web uygulaması + REST API)"]

    U1["Öğrenci / Personel<br/>(ilan veren, talep eden)"]
    U2["Moderatör"]

    E1["Kimlik doğrulama<br/>(e-posta/şifre JWT; ileride SSO)"]
    E2["E-posta servisi<br/>(SMTP, bildirimler)"]
    E3["Dosya depolama<br/>(eşya fotoğrafları)"]

    U1 -->|"ilan oluştur, ara, talep et"| S
    S -->|"ilan listesi, talep sonucu"| U1
    U2 -->|"ilan ve talep denetimi, onay/red"| S
    S -->|"bekleyen talepler"| U2

    S <-->|"kullanıcı doğrulama"| E1
    S -->|"talep ve karar bildirimi"| E2
    S <-->|"fotoğraf yükle/oku"| E3
```

## 2. Sistem Sorumlulukları

- Kayıp ve bulunan eşya ilanlarını yayınlama, arama ve filtreleme
- Sahiplik talebi alma ve onay/red akışını yürütme
- İlan ve talep durumlarını takip etme (`OPEN`, `CLAIM_PENDING`, `RETURNED`, `CLOSED`)
- Rol bazlı yetkilendirme (öğrenci, personel, moderatör)
- Talep ve karar bildirimlerini e-posta ile iletme

## 3. Dış Sistem Etkileşimleri

| Dış sistem | Yön | Amaç | Sprint 1 durumu |
|---|---|---|---|
| Öğrenci / Personel | İki yönlü | İlan verme, arama, sahiplik talebi | Kapsam içi |
| Moderatör | İki yönlü | İlan ve talep denetimi, onay/red | Kapsam içi |
| Kimlik doğrulama | İki yönlü | Giriş ve token doğrulama | Kapsam içi (JWT); üniversite SSO sonraya bırakıldı |
| E-posta servisi | Sistemden dışarı | Yeni talep ve karar bildirimi | Kapsam içi |
| Dosya depolama | İki yönlü | Eşya fotoğrafları | Kapsam içi, ilk sürümde isteğe bağlı |
| WhatsApp grupları, duvar panoları | Yok | Mevcut dağınık yöntem | Entegrasyon kapsam dışı |

## 4. Sistem Sınırı

- **Sınır içi:** web arayüzü, REST API (`openapi.yaml`), ilan/talep/kullanıcı verisi.
- **Sınır dışı:** üniversite öğrenci bilgi sistemi, WhatsApp ve sosyal medya entegrasyonları, fiziksel eşya teslim süreci.
