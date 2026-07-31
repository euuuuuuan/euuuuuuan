## Orkestrasi agen · gerbang deterministik · pengembangan berbantuan AI

[English](README.md) · [한국어](README.ko.md) · [日本語](README.ja.md) · [简体中文](README.zh-CN.md) · [繁體中文](README.zh-TW.md) · [Español](README.es.md) · [Português (BR)](README.pt-BR.md) · [Deutsch](README.de.md) · [Français](README.fr.md) · [Русский](README.ru.md) · [Italiano](README.it.md) · **Bahasa Indonesia** · [Türkçe](README.tr.md)

Otomasi berbasis kode dan sistem yang diorkestrasi oleh agen: platform agen dengan lapisan
izin fail-closed, pipeline retrieval lokal dengan gerbang evaluasi, dan simulasi deterministik
yang dapat di-replay bit demi bit.

Setiap angka di halaman ini adalah hasil pengukuran, bukan perkiraan. Perintah yang menghasilkannya
tersimpan di repo yang dideskripsikannya, dan repo itu memeriksanya ulang sebelum dipublikasikan.

---

## Karya unggulan

| Proyek | Apa ini | Fakta terukur | Live |
|---|---|---|---|
| **[baton](https://github.com/euuuuuuan/baton-public)** | Platform orkestrasi local-first untuk pekerjaan agen yang dibatasi: daemon kontrol macOS berbasis Swift di balik gerbang izin fail-closed, digerakkan oleh orkestrator TypeScript melalui MCP. *(platform)* | **63 tool MCP terdokumentasi** di `docs/TOOL_SURFACE.json`, **234 file test/spec**, **15 ADR**, dan broker persetujuan jarak jauh yang grant-nya terikat sidik jari dan sekali pakai (ADR-003). | — |
| **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** | RAG berbahasa Korea yang dibangun untuk lingkungan dengan pemisahan jaringan (망분리): hybrid retrieval murni stdlib, masking PII, dan gerbang penolakan untuk pertanyaan di luar cakupan. *(tool)* | **0 dependensi pihak ketiga**, dan build-nya dijaga oleh **golden set 50 pertanyaan**: hit@3 **100 %** (39/39), presisi penolakan **100 %** / recall **81.8 %** — dan akurasi sitasi **36.75 %**, metrik yang lemah, dipublikasikan alih-alih disembunyikan. | — |
| **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** | Pekerjaan SQL seorang pemasar lifecycle/CRM yang dikerjakan agen melalui MCP, di balik kernel tata kelola yang menentukan seberapa jauh agen boleh melangkah dan apa saja yang dicatat. *(tool)* | **108 test lulus** (dengan MCP SDK terpasang), termasuk serangan **33 red-team PII** dan **12 red-team tata kelola** terhadap kernel. Data **100 % sintetis** dan dapat direproduksi byte demi byte: seed 42 → digest SQLite identik, 5,000 pelanggan / 99,922 event. | — |
| **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** | Perencana breach taktis dan sim regu real-time di browser; raid yang selesai bisa dibagikan sebagai URL yang tersimulasi ulang di browser orang lain. *(build yang dapat dimainkan, bukan rilis komersial)* | **Simulasi fixed-tick 30 Hz** dengan gerbang kemurnian yang melarang **11 API non-deterministik** dari paket sim dan konten — gerbang itu lulus, dan itulah yang membuat seed + log input dapat di-replay secara identik. **55 file test/spec**, dan 40 skrip di `tools/` yang 35 di antaranya adalah harness headless (5 sisanya adalah asset fetcher dan codegen). | **[mainkan](https://fatal-funnel.vercel.app)** |
| **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** | Perangkat penalaran deterministik: 8 tool MCP yang memaksakan scope lock, rencana cheapest-disconfirming-step, sapuan blind-spot, dan gerbang vonis yang menolak klaim tanpa bukti eksekusi. *(tool)* | **0 dependensi runtime**, **33/33 test lulus** — dan salah satu test itu adalah jaminannya: seluruh permukaan tidak melakukan penulisan filesystem, tidak memunculkan proses, dan tidak melakukan panggilan jaringan. | — |

---

## Bagaimana semuanya dibangun

Bagian yang menarik bukanlah "dibangun dengan AI", melainkan apa yang berada *di antara* model dan repositori.

- **Dua agen coding, satu wasit deterministik.** [`agent-relay`](https://github.com/euuuuuuan/agent-relay-public)
  adalah driver eksternal yang menjalankan dua CLI agen saling berhadapan sampai sebuah gerbang lulus.
  Gerbang itulah yang memutuskan "selesai" — tidak pernah opini agen atas pekerjaannya sendiri. 12 unit test,
  lulus dengan `PATH` dipangkas ke direktori sistem saja, sehingga penolakan si driver dapat dibuktikan
  tanpa satu pun agen terpasang.
- **Gerbang deterministik berjalan sebelum model dipanggil sama sekali.** Pemeriksa kemurnian, bukan firasat:
  gerbang sim fatal-funnel melarang 11 API non-deterministik; test read-only reasonforge menegakkan 6
  pola terlarang. Kualitas retrieval juga merupakan gerbang build — golden set plus ambang batas yang
  menggagalkan run ketika terjadi regresi ([rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)).
- **Klaim UI berasal dari probe dan screenshot, bukan dari ingatan.** [hollowmere](https://github.com/euuuuuuan/hollowmere-public)
  menyertakan harness `shot`, `relic-qa`, dan `mobile-qa`; fatal-funnel punya 35 harness headless di antara 40 skrip di `tools/`
  dan 4 kamera screenshot tetap. Pelajaran ini didapat dengan cara yang mahal: tooltip yang "berfungsi" bisa mati
  di balik satu flag input-filter; probe input sintetis menangkapnya, membaca kode tidak.
- **Aset dijaga lisensinya sebelum dibuat.** [assetforge](https://github.com/euuuuuuan/assetforge-public)
  menolak menghasilkan aset dari model mana pun yang tidak ada di registrinya. Di hilir, **seluruh 27 snapshot
  yang dipublikasikan membawa `CREDITS.md` dengan tabel redistribusi 4 tingkat** — sehingga siapa pun yang
  melakukan fork tahu persis apa yang harus mereka hapus.
- **Label yang jujur, ditegakkan oleh skema.** Di [euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)
  setiap metrik yang dipublikasikan bertipe `kind: z.enum(['measured', 'estimated'])`. Sebuah angka tidak dapat
  dipublikasikan tanpa menyatakan yang mana dirinya. Aturan itulah alasan halaman ini tidak memuat angka
  pemasaran yang dibulatkan.
- **Publikasi adalah pipeline, bukan copy-paste.** Setiap repo publik di sini dihasilkan oleh proses publikasi
  tersanitasi yang dapat diulang, yang file klaimnya mengeksekusi ulang setiap kuantitas di README dan menolak
  membuat commit jika ada satu saja yang bergeser. Di seluruh 27 snapshot itu berarti **629 file test/spec**.

---

## Karya pilihan

Bukan seluruh 27 repo — hanya yang menunjukkan kapabilitas yang berbeda.

**Game browser (buka tautannya dan mainkan)**
- **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** — sim taktis deterministik, 40 senjata, 12 misi yang ditulis tangan · [live](https://fatal-funnel.vercel.app)
- **[hollowmere](https://github.com/euuuuuuan/hollowmere-public)** — action RPG ringkas: parry, relik, dua zona. *Vertical slice pada v0.5.1* · [live](https://hollowmere-two.vercel.app)
- **[neko-shift](https://github.com/euuuuuuan/neko-shift-public)** — game idle kecil yang utuh tentang rasio penempatan staf, 18 file test · [live](https://neko-shift.vercel.app)
- **[ashglass-reliquary](https://github.com/euuuuuuan/ashglass-reliquary-public)** — slice ARPG isometrik. **Berbagi inti simulasi deterministik dengan hollowmere**; aturan combat, encounter, dan kontennya miliknya sendiri. · [live](https://ashglass-reliquary.vercel.app)
- **[moonshard-warden](https://github.com/euuuuuuan/moonshard-warden-public)** — arena isometrik tiga gelombang: baca telegraph-nya, rangkai mooncut, dash melewati serangan yang sudah dilancarkan. **43 file test** dan ledger aset paling ketat dari semuanya — 100 baris SHA-256 per file, semuanya CC0. · [live](https://moonshard-warden.vercel.app)

**Game engine (Godot / Unity — vertical slice dan prototipe, tidak ada yang dirilis komersial)**
- **[voidclad](https://github.com/euuuuuuan/voidclad-public)** — artileri kapal kapital di luar angkasa: waktu tempuh peluru, inertial lead, penetrasi sudut armor, di atas sim deterministik 30 Hz (27 file test)
- **[cairnfall](https://github.com/euuuuuuan/cairnfall-public)** — ARPG boss-raid solo, sepuluh bos tiga fase; klaim "raid ini tidak pernah menggendongmu" adalah statistik pasca-pertarungan yang diukur, bukan invarian yang dipaksakan — jika sekutu yang mendaratkan pukulan penghabisan, laporan run mengatakannya (33 file test)
- **[todak](https://github.com/euuuuuuan/todak-public)** — pet piksel desktop yang hidup di sepanjang tepi bawah layarmu (36 file test)
- **[hordecaller](https://github.com/euuuuuuan/hordecaller-public)** (30) · **[ragtail](https://github.com/euuuuuuan/ragtail-public)** (29) · **[driftfolk](https://github.com/euuuuuuan/driftfolk-public)** + **[port Unity-nya](https://github.com/euuuuuuan/driftfolk-unity-public)** — inti dari port ini adalah paritas numerik dengan versi aslinya · **[gatewarden](https://github.com/euuuuuuan/gatewarden-public)** · **[emberline](https://github.com/euuuuuuan/emberline-public)**
- **[tidewrack](https://github.com/euuuuuuan/tidewrack-public)** — battle royale directed-autonomy, Unity. Dicantumkan terpisah karena snapshot ini adalah **kerangka verifikasi, bukan slice yang dapat dimainkan**: sim deterministik dan harness golden-file-nya adalah bagian yang dikirim, dan 11 dari 15 asersi soak bernomor masih tertunda.

**Tooling AI dan agen**
- **[baton](https://github.com/euuuuuuan/baton-public)** — platform agennya: permukaan MCP 63 tool, broker persetujuan, 16 paket orkestrator, 90 source Swift
- **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** — harness penalaran read-only, 8 tool MCP, 0 dependensi
- **[agent-relay](https://github.com/euuuuuuan/agent-relay-public)** — loop antara dua agen coding yang diakhiri oleh gerbang
- **[assetforge](https://github.com/euuuuuuan/assetforge-public)** — pembuatan aset lokal tanpa biaya di balik gerbang lisensi fail-closed
- **[content-qa-pipeline](https://github.com/euuuuuuan/content-qa-pipeline-public)** — generate → judge → compose → publish, di mana "belum yakin" memarkir run alih-alih mengirimkannya

**Data, backend, dan tooling domain**
- **[officegrid](https://github.com/euuuuuuan/officegrid-public)** — administrasi aset multi-tenant yang batas tenant-nya hidup di PostgreSQL: **82 kebijakan row-level-security** di 11 file migrasi, dengan 6 file test pgTAP yang diarahkan padanya — bukan penyaringan di lapisan aplikasi
- **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** · **[rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)** — retrieval dengan gerbang evaluasi sebagai intinya
- **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** — agen + kernel tata kelola di atas CDP yang sepenuhnya sintetis
- **[ad-compliance-linter](https://github.com/euuuuuuan/ad-compliance-linter-public)** — linter tahap awal untuk naskah iklan finansial/asuransi Korea. Seluruh 5 ruleset yang dikirim ditandai `"verified": false` di dalam datanya sendiri dan laporannya tidak bisa dicetak tanpa disclaimernya: ia menandai naskah untuk ditinjau manusia, bukan mensertifikasi kepatuhan
- **[loopsmith](https://github.com/euuuuuuan/loopsmith-public)** — mainkan instrumennya atau tulis pattern-nya; lagu yang sama, disunting tanpa kehilangan apa pun dari kedua sisi (27 file test)
- **[euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)** — situs statis dwibahasa yang setiap angkanya menyatakan terukur vs perkiraan · [live](https://euuuuuuan.pages.dev)

---

## Tentang repo-repo ini

Seluruh 27 repo adalah snapshot tersanitasi: kode di bawah Apache-2.0, aset diatur per repo oleh
`CREDITS.md` 4 tingkat. Game-game di sini adalah build pribadi dan vertical slice — dapat dimainkan,
dijaga gerbang, dan dilabeli dengan jujur, bukan rilis komersial.

**Kontak:** buka issue atau diskusi di repo mana pun di sini.
