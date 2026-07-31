## Ajan orkestrasyonu · deterministik kapılar · yapay zekâ ile güçlendirilmiş geliştirme

[English](README.md) · [한국어](README.ko.md) · [日本語](README.ja.md) · [简体中文](README.zh-CN.md) · [繁體中文](README.zh-TW.md) · [Español](README.es.md) · [Português (BR)](README.pt-BR.md) · [Deutsch](README.de.md) · [Français](README.fr.md) · [Русский](README.ru.md) · [Italiano](README.it.md) · [Bahasa Indonesia](README.id.md) · **Türkçe**

Kod tabanlı otomasyon ve ajan orkestrasyonuyla çalışan sistemler: fail-closed bir izin
katmanına sahip bir ajan platformu, değerlendirme kapılarıyla korunan yerel retrieval hatları
ve bit bit aynı şekilde yeniden oynatılabilen deterministik simülasyonlar.

Bu sayfadaki her sayı bir tahmin değil, bir ölçümdür. Sayıyı üreten komut, anlattığı reponun
içinde yaşar ve repo yayımlamadan önce onu yeniden doğrular.

---

## Öne çıkan çalışmalar

| Proje | Nedir | Ölçülen gerçek | Canlı |
|---|---|---|---|
| **[baton](https://github.com/euuuuuuan/baton-public)** | Sınırları belirlenmiş ajan işleri için local-first orkestrasyon platformu: fail-closed izin kapılarının arkasında duran Swift tabanlı bir macOS kontrol daemon'ı ve onu MCP üzerinden süren TypeScript bir orkestratör. *(platform)* | `docs/TOOL_SURFACE.json` içinde **63 belgelenmiş MCP aracı**, **234 test/spec dosyası**, **15 ADR** ve verdiği izinler parmak izine bağlı ve tek kullanımlık olan bir uzaktan onay aracısı (ADR-003). | — |
| **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** | Ağdan yalıtılmış (망분리) ortamlar için geliştirilmiş Korece RAG: yalnızca stdlib kullanan hibrit retrieval, PII maskeleme ve kapsam dışı sorular için bir ret kapısı. *(araç)* | **0 üçüncü taraf bağımlılık**; build, **50 soruluk bir golden set** ile kapılanıyor: hit@3 **%100** (39/39), ret kesinliği **%100** / duyarlılığı **%81.8** — ve zayıf metrik olan atıf doğruluğu **%36.75**, saklanmak yerine yayımlanmış. | — |
| **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** | Bir yaşam döngüsü/CRM pazarlamacısının SQL işini MCP üzerinden bir ajanın yaptığı sistem; ajanın nereye kadar gidebileceğine ve nelerin kayda geçeceğine karar veren bir yönetişim çekirdeğinin arkasında. *(araç)* | **108 test geçiyor** (MCP SDK kuruluyken), buna çekirdeğe karşı **33 PII red-team** ve **12 yönetişim red-team** saldırısı dahil. Veri **%100 sentetik** ve bayt bayt yeniden üretilebilir: seed 42 → birebir aynı SQLite özeti, 5.000 müşteri / 99.922 olay. | — |
| **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** | Tarayıcıda çalışan taktiksel baskın planlayıcı ve gerçek zamanlı manga simülasyonu; tamamlanan bir baskın URL olarak paylaşılır ve başkasının tarayıcısında yeniden simüle edilir. *(oynanabilir yapı, ticari bir sürüm değil)* | **30 Hz sabit tikli simülasyon** ve sim ile içerik paketlerinde **11 deterministik olmayan API'yi** yasaklayan bir saflık kapısı — kapı geçiyor; seed + girdi kaydının birebir aynı şekilde yeniden oynamasını sağlayan da bu. **55 test/spec dosyası** ve `tools/` içinde 40 betik; bunların 35'i headless harness (kalan 5'i asset indirici ve codegen). | **[oyna](https://fatal-funnel.vercel.app)** |
| **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** | Deterministik bir akıl yürütme aygıtı: kapsam kilidini, en ucuz çürütücü adım planını, kör nokta taramasını ve yürütme kanıtı olmayan iddiayı reddeden bir hüküm kapısını dayatan 8 MCP aracı. *(araç)* | **0 çalışma zamanı bağımlılığı**, **33/33 test geçiyor** — ve bu testlerden biri garantinin ta kendisi: tüm yüzey hiçbir dosya sistemi yazması, hiçbir süreç başlatması ve hiçbir ağ çağrısı yapmıyor. | — |

---

## Bunlar nasıl inşa ediliyor

İlginç kısım "yapay zekâ ile yapıldı" değil. İlginç olan, model ile depo *arasında* duran şey.

- **İki kodlama ajanı, tek deterministik hakem.** [`agent-relay`](https://github.com/euuuuuuan/agent-relay-public),
  iki ajan CLI'ını bir kapı geçene kadar birbirine karşı çalıştıran harici bir sürücü. "Bitti" kararını
  kapı verir — asla bir ajanın kendi işi hakkındaki kanaati değil. 12 birim testi, `PATH` sistem
  dizinlerine indirgenmiş hâlde geçiyor; böylece sürücünün retleri iki ajandan hiçbiri kurulu
  olmadan kanıtlanabiliyor.
- **Deterministik kapılar, model daha çağrılmadan önce çalışıyor.** His değil, saflık denetleyicileri:
  fatal-funnel'ın sim kapısı 11 deterministik olmayan API'yi yasaklıyor; reasonforge'un salt okunur
  testi 6 yasak deseni zorluyor. Retrieval kalitesi de bir build kapısı — gerilediğinde koşuyu
  düşüren bir golden set artı eşikler ([rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)).
- **UI iddiaları hafızadan değil, problardan ve ekran görüntülerinden gelir.** [hollowmere](https://github.com/euuuuuuan/hollowmere-public),
  `shot`, `relic-qa` ve `mobile-qa` harness'larıyla geliyor; fatal-funnel'da `tools/` içindeki 40 betiğin 35'i headless harness
  ve 4 sabit ekran görüntüsü kamerası var. Bu ders pahalıya öğrenildi: "çalışan" bir tooltip, tek bir
  input-filter bayrağının arkasında ölü olabilir; sentetik bir girdi probu bunu yakalar, kodu okumak yakalamaz.
- **Asset'ler daha üretilmeden lisans kapısından geçer.** [assetforge](https://github.com/euuuuuuan/assetforge-public),
  kayıt defterinde bulunmayan herhangi bir modelden üretim yapmayı reddediyor. Aşağı akışta,
  **yayımlanan 27 snapshot'ın tamamı, 4 kademeli bir yeniden dağıtım tablosu içeren bir `CREDITS.md`
  taşıyor** — böylece fork'layan herkes tam olarak neyi kaldırması gerektiğini biliyor.
- **Dürüst etiketler, bir şemayla zorlanır.** [euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)
  içinde yayımlanan her metrik `kind: z.enum(['measured', 'estimated'])` olarak tiplenmiş. Bir sayı,
  hangisi olduğunu beyan etmeden yayımlanamıyor. Bu sayfada yuvarlak pazarlama rakamlarının
  olmamasının nedeni bu kural.
- **Yayımlama bir kopyala-yapıştır değil, bir boru hattıdır.** Buradaki her public repo, tekrarlanabilir
  bir temizleme (sanitizing) yayınıyla üretiliyor; bu yayının claims dosyası README'deki her niceliği
  yeniden çalıştırıyor ve biri kaydıysa commit oluşturmayı reddediyor. 27 snapshot genelinde bu,
  **629 test/spec dosyası** demek.

---

## Seçilmiş çalışmalar

27 reponun hepsi değil — ayırt edici bir yeteneği gösterenler.

**Tarayıcı oyunları (bağlantıyı açın ve oynayın)**
- **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** — deterministik taktiksel sim, 40 silah, 12 elle tasarlanmış görev · [canlı](https://fatal-funnel.vercel.app)
- **[hollowmere](https://github.com/euuuuuuan/hollowmere-public)** — kompakt aksiyon RPG: savuşturma (parry), relikler, iki bölge. *v0.5.1'de dikey dilim* · [canlı](https://hollowmere-two.vercel.app)
- **[neko-shift](https://github.com/euuuuuuan/neko-shift-public)** — personel oranları üzerine küçük ama eksiksiz bir idle oyunu, 18 test dosyası · [canlı](https://neko-shift.vercel.app)
- **[ashglass-reliquary](https://github.com/euuuuuuan/ashglass-reliquary-public)** — izometrik ARPG dilimi. **Deterministik simülasyon çekirdeğini hollowmere ile paylaşıyor**; savaş kuralları, karşılaşmalar ve içerik kendine ait. · [canlı](https://ashglass-reliquary.vercel.app)
- **[moonshard-warden](https://github.com/euuuuuuan/moonshard-warden-public)** — üç dalgalı izometrik arena: telegrafı oku, mooncut'ı zincirle, kesinleşen vuruştan dash ile kaç. **43 test dosyası** ve setin en katı asset defteri — dosya başına birer tane olmak üzere 100 SHA-256 satırı, tamamı CC0. · [canlı](https://moonshard-warden.vercel.app)

**Motor oyunları (Godot / Unity — dikey dilimler ve prototipler, hiçbiri ticari olarak yayımlanmadı)**
- **[voidclad](https://github.com/euuuuuuan/voidclad-public)** — uzayda ana gemi topçuluğu: mermi uçuş süresi, ataletli nişan öngörüsü, zırh açısına bağlı delme; deterministik 30 Hz sim üzerinde (27 test dosyası)
- **[cairnfall](https://github.com/euuuuuuan/cairnfall-public)** — solo boss-raid ARPG, on adet üç fazlı boss; "raid seni asla taşımaz" iddiası zorlanan bir değişmez değil, dövüş sonrası ölçülen bir istatistik — öldürücü darbeyi bir müttefik indirirse koşu raporu bunu söylüyor (33 test dosyası)
- **[todak](https://github.com/euuuuuuan/todak-public)** — ekranınızın alt kenarında yaşayan masaüstü piksel evcil hayvanları (36 test dosyası)
- **[hordecaller](https://github.com/euuuuuuan/hordecaller-public)** (30) · **[ragtail](https://github.com/euuuuuuan/ragtail-public)** (29) · **[driftfolk](https://github.com/euuuuuuan/driftfolk-public)** + onun **[Unity portu](https://github.com/euuuuuuan/driftfolk-unity-public)** — portun asıl amacı orijinalle sayısal eşdeğerlik · **[gatewarden](https://github.com/euuuuuuan/gatewarden-public)** · **[emberline](https://github.com/euuuuuuan/emberline-public)**
- **[tidewrack](https://github.com/euuuuuuan/tidewrack-public)** — yönlendirilmiş özerklikli battle royale, Unity. Ayrı listeleniyor çünkü bu snapshot **oynanabilir bir dilim değil, bir doğrulama iskeleti**: teslim edilen kısım deterministik sim ve onun golden-file harness'ı; 15 numaralandırılmış soak doğrulamasından 11'i hâlâ beklemede.

**Yapay zekâ ve ajan araçları**
- **[baton](https://github.com/euuuuuuan/baton-public)** — ajan platformu: 63 araçlık MCP yüzeyi, onay aracısı, 16 orkestratör paketi, 90 Swift kaynak dosyası
- **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** — salt okunur akıl yürütme harness'ı, 8 MCP aracı, 0 bağımlılık
- **[agent-relay](https://github.com/euuuuuuan/agent-relay-public)** — iki kodlama ajanı arasında kapıyla sonlanan döngü
- **[assetforge](https://github.com/euuuuuuan/assetforge-public)** — fail-closed bir lisans kapısının arkasında yerel, sıfır maliyetli asset üretimi
- **[content-qa-pipeline](https://github.com/euuuuuuan/content-qa-pipeline-public)** — üret → değerlendir → derle → yayımla; "emin değilim" durumunda koşu yayımlanmak yerine park edilir

**Veri, backend ve alan araçları**
- **[officegrid](https://github.com/euuuuuuan/officegrid-public)** — kiracı sınırının PostgreSQL'de yaşadığı çok kiracılı varlık yönetimi: 11 migration dosyasına yayılmış **82 satır düzeyi güvenlik (RLS) politikası** ve onlara nişan almış 6 pgTAP test dosyası — uygulama katmanında filtreleme değil
- **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** · **[rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)** — asıl meselesi değerlendirme kapısı olan retrieval
- **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** — tamamı sentetik bir CDP üzerinde ajan + yönetişim çekirdeği
- **[ad-compliance-linter](https://github.com/euuuuuuan/ad-compliance-linter-public)** — Korece finans/sigorta reklam metinleri için ilk geçiş linter'ı. Yayımlanan 5 kural setinin tamamı verinin kendisinde `"verified": false` olarak işaretli ve rapor, feragatnamesi olmadan basılamıyor: metni insan incelemesi için işaretler, uyumluluğu tasdik etmez
- **[loopsmith](https://github.com/euuuuuuan/loopsmith-public)** — enstrümanları çal ya da desenleri yaz; aynı şarkı, iki taraftan da kayıpsız düzenlenir (27 test dosyası)
- **[euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)** — her sayının ölçülmüş mü tahmin mi olduğunu beyan ettiği iki dilli statik site · [canlı](https://euuuuuuan.pages.dev)

---

## Bu repolar hakkında

27 reponun tamamı temizlenmiş snapshot'lardır: kod Apache-2.0 altında, asset'ler repo bazında
4 kademeli bir `CREDITS.md` ile yönetilir. Oyunlar kişisel yapılar ve dikey dilimlerdir —
oynanabilir, kapılarla korunan ve dürüstçe etiketlenmiş; ticari sürümler değil.

**İletişim:** buradaki herhangi bir repoda bir issue veya tartışma açın.
