<p align="center">
  <a href="https://ispatla.tr"><img src="public/brand/ispatla-favicon.png" alt="ispatla.tr simgesi" width="88"></a>
</p>

<h1 align="center">ispatla.tr</h1>

<p align="center"><strong>Algoritmaya tapma. İspatla.</strong></p>

<p align="center">
  𝕏'teki kaynakları incele. Hesabına uygun konuları bul. Taslağını kendi sesinle yaz.
</p>

<p align="center">
  <a href="https://ispatla.tr">Siteyi aç</a> ⁜
  <a href="https://ispatla.tr/tr">Türkçe arayüz</a> ⁜
  <a href="https://ispatla.tr/tr/docs">Nasıl kullanılır?</a> ⁜
  <a href="CONTRIBUTING.md">Katkı</a>
</p>

<p align="center">
  <a href="https://github.com/OnurByte/ispatla.tr/actions/workflows/social-sharing-ci.yml"><img src="https://github.com/OnurByte/ispatla.tr/actions/workflows/social-sharing-ci.yml/badge.svg" alt="Kod ve paylaşım kontrolleri"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/lisans-AGPL--3.0--or--later-315BF5" alt="AGPL-3.0-or-later lisansı"></a>
</p>

## Ne işe yarıyor?

Bir 𝕏 gönderisini görüp kendi hesabında nasıl ele alacağını düşünüyorsan, ispatla.tr kaynak gönderiyi, neden ilgili olduğunu ve düzenleyebileceğin bir taslağı aynı yerde gösterir. Birden fazla hesabın varsa her biri için ayrı bir yaklaşım seçebilirsin.

1. **Hesabını ekle.** Herkese açık 𝕏 kullanıcı adından konularını ve yazım tarzını incele. 𝕏 parolanı vermen gerekmez.
2. **Kaynağı gör.** İlgili gönderiyi, hesabına neden uyduğunu ve bilinen verinin sınırlarını incele.
3. **Taslağı düzenle.** Kendi AI sağlayıcını kullan, önerilen metni değiştir veya tamamen kendin yaz.
4. **Kararı sen ver.** Paylaşımı kendin tamamla. Sonuçlar varsa kaynak ve doğrulama düzeyiyle ayrı gösterilir.

<p align="center">
  <img src="docs/assets/interactive-preview.png" alt="Solda kaynak 𝕏 gönderisi, sağda hesap seçimi ve düzenlenebilir taslak" width="900">
</p>

## Bilmen gerekenler

- Fırsat puanı, gönderinin viral olacağı anlamına gelmez.
- Herkese açık 𝕏 verileri FxTwitter üzerinden okunabilir; bu, resmî 𝕏 doğrulaması veya hesap sahipliği kanıtı değildir.
- AI anahtarı ve sağlayıcısı sana aittir. Sağlayıcı veya altyapı ücretli olabilir.
- Resmî ücretli 𝕏 veri ve otomatik yayın işlemleri Prime öncesinde kapalıdır. Hazırlanan taslağın yayımlanması kullanıcı kontrolündedir.

**Kod açık. Karar sende.** [AGPL-3.0-or-later lisansı](LICENSE) ⁜ [Mimari](docs/ARCHITECTURE.md) ⁜ [Ürün yaklaşımı](docs/PRODUCT_STRATEGY.md)

<details>
<summary>Geliştiriciler için: kurulum, MCP, testler ve teknik sınırlar</summary>

### Yerel başlangıç

Node.js 22.5+ ve Bun kullanılır. Repo kökünde çalıştırın:

```sh
bun install --frozen-lockfile
```

Yerel, Git'e eklenmeyen ortam dosyanızda veya süreç ortamında yapılandırın:

```text
BETTER_AUTH_SECRET=<en az 32 karakterlik rastgele gizli değer>
BETTER_AUTH_URL=http://localhost:3000
ISPATLA_TOKEN_KEY_CURRENT=<rastgele token şifreleme anahtarı>
ISPATLA_SECRET_KEY=<AI kasası için ayrı rastgele anahtar>
ISPATLA_AUTOMATION=0
DATABASE_URL=postgresql://<role>:<password>@<host>:<port>/<database>
```

Boş geliştirme veritabanına `supabase/migrations/` dosyalarını ad sırasıyla uygulayın. Mevcut üretim veritabanı üzerinde deneme yapmayın. Supabase kullanılıyorsa Connect panelindeki tam transaction-pooler URI'sini alın; host/parola tahmin etmeyin. Uygulama Supabase bağlantılarında TLS sertifikasını doğrular. Landing veritabanı olmadan okunabilir; oturum ve özel çalışma alanı veritabanına ihtiyaç duyar.

```sh
bun run dev
```

Landing `/`, kayıt `/signup`, giriş `/login`, çalışma alanı `/dashboard`, fırsatlar `/opportunities`, hesaplar `/accounts`, taslaklar `/drafts` adreslerindedir. Yerelleştirilmiş rotalar 20 dili destekler; varsayılan İngilizce rotası öneksizdir; Türkçe `/tr` altındadır.

Mail gönderimi için `ISPATLA_MAIL_API_URL` ve `ISPATLA_MAIL_API_TOKEN` kullanılır. HTTPS JSON taşıyıcısı `to`, `subject`, `text` alanlarını kabul etmelidir. Gerçek teslim ayrıca doğrulanır. `ISPATLA_PRIVATE_BETA=1`, e-posta doğrulamasını kapatan geliştirme/beta tercihidir.

### Hesap, giriş ve yayın

Public kullanıcı adını eklemek OAuth veya 𝕏 parolası gerektirmez. Profil ve public gönderiler FxTwitter üzerinden analiz edilir; eksik veri partial sonuç üretebilir. Handle eklemek sahiplik kanıtı değildir. Sahiplik ayrı doğrulanır. Manuel konu ve ses tercihleri yeniden analizde korunur.

𝕏 ile uygulamaya giriş, `X_OAUTH_CLIENT_ID` ve `X_OAUTH_CLIENT_SECRET` yapılandırıldığında kullanılabilir. Developer Console callback adresi:

```text
http://localhost:3000/api/auth/callback/twitter
```

Üretimde kendi HTTPS origin'inizi kullanın. Giriş salt okuma scope'ları ve PKCE kullanır; giriş grant'i otomatik yayın izni vermez. Girişteki `/2/users/me` çağrısının maliyeti için sıfır ücret iddiası yapılmaz.

**Prime öncesinde resmî ücretli veri/yayın istemcisi kapalıdır.** Bir ortam değişkeni, OAuth scope'u veya MCP parametresi bu sınırı açmaz. Prime entitlement ve bütçe doğrulaması henüz uygulanmadığından bu işlemler kullanılabilir gösterilmez. Public FxTwitter okuma ve kullanıcı tarafından tamamlanan paylaşım hazırlığı ayrı akışlardır. [Resmî API sınırı](docs/evidence/official-api-boundary.md).

Web Intent veya zamanlama ekranının açılması yayın başarı kanıtı değildir. Public receipt author/metin/hedef bağlamıyla ilişkilendirilir; belirsiz işlem kör yeniden denenmez. Otomatik like, DM ve spam kampanyaları ürün yolu değildir.

### MCP

Yerel stdio sunucusu:

```sh
bun run mcp
```

İstemci süreç ortamındaki `ISPATLA_MCP_SESSION_COOKIE`, gerçek uygulama oturumunu temsil eder. Parola gibi koruyun; Git, log veya paylaşılan ayarlara koymayın.

Uzak istemciye dağıtımınızın `https://<origin>/mcp` adresini ekleyin. OAuth discovery, PKCE, açık consent ve iptal edilebilir scopes kullanılır: `read`, `drafts:write`, `analysis:write`, `publishing:prepare`, `receipts:write`. Taslak veya yayın hazırlama yetkisi otomatik gönderim yetkisi değildir. Erişim `/settings/security` üzerinden iptal edilebilir. [Bağlantı rehberi](https://ispatla.tr/docs#mcp).

### Kontroller ve worker

```sh
bun test --isolate
bun run lint
bun run typecheck
bun run test:seo
bun run build
```

Kaynak taraması `bun run automation:scan`, kalıcı worker `bun run automation:worker` ile çalışır. Kaynaklar, owner sınırları ve veritabanı hazır olmalıdır; bir sürecin başlaması veri kalitesini veya gerçek yayını kanıtlamaz. [Worker rehberi](docs/RUN-WORKER.md).

Günlük pazarlama raporu `bun scripts/landing-report.ts` ile alınır. Olay sayıları kişi sayısı değildir; anonim landing ziyareti kayıt/taslak sahibiyle eşlenmez. İlk kalıcı taslak süreleri mevcut kayıtların anlık görüntüsüdür.

[Mimari](docs/ARCHITECTURE.md) ⁜ [Brief kabul kaydı](docs/BRIEF_ACCEPTANCE.md) ⁜ [Katkı](CONTRIBUTING.md) ⁜ [Lisans](LICENSE)

Yerel fixture, test, Lighthouse veya build sonucu canlı OAuth, mail teslimi, sağlayıcı faturası ve üretim davranışının yerine geçmez. Bu kanıtlar kabul kaydında ayrı tutulur.

### Lighthouse ve SEO

`bun run test:seo`; 20 dilde canonical/hreflang, robots/sitemap, sosyal resim ve oEmbed sınırlarını doğrular. Typecheck komutu `--strict` ile çalışır.

Lighthouse için önce üretim derlemesini yerelde başlatın; geliştirme sunucusunu ölçmeyin:

```sh
bun run build
bun run start
# Ayrı terminalde:
LIGHTHOUSE_URL=http://localhost:3000/ bun run quality:lighthouse
```

Chrome/Chromium gerekir; `CHROME_PATH` ile seçilebilir. Mobile ve desktop HTML/JSON raporları `lighthouse-reports/` altına yazılır. Eşikler: performans en az 90; erişilebilirlik, Best Practices ve SEO 100. İhlal komutu başarısız yapar. Bunlar yerel laboratuvar ölçümleridir; saha Core Web Vitals veya üretim erişilebilirliği kanıtı değildir. Ölçülen origin sunucunun yapılandırılmış origin'iyle eşleşmelidir.


### SEO ve favicon doğrulaması

After a production build is running locally, audit its actual responses:

```sh
bun run quality:seo http://localhost:3000
```

The gate covers sitemap pages in all 20 locales, reciprocal canonical/hreflang links, crawl rules, the reviewed brand favicon, social images, auth noindex and unknown-route 404s. It fails on missing, duplicate, conflicting or non-crawlable metadata. CI starts its own loopback production server for the same check. The origin argument can also be an explicitly selected deployment for a read-only audit; passing this gate does not prove Google has recrawled the site or changed its Search Console icon. Evidence and upstream references: [27 October usability/SEO verification](docs/qa/usability-seo-2026-10-27.md).

</details>
