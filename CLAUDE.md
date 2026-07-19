# CLAUDE.md — Second Brain (Multi-Wiki Index)

## Apa Ini

Vault ini mengimplementasikan pola **LLM Wiki** Andrej Karpathy: alih-alih re-derive
pengetahuan dari nol tiap kali ditanya sesuatu (ini yang terjadi di RAG standar),
LLM disuruh membangun dan memelihara wiki markdown yang persisten — setiap sumber
baru masuk, LLM baca, ekstrak insight, lalu update halaman-halaman wiki yang sudah
ada. Accumulate-and-compound, bukan retrieve-and-forget.

> "Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase."
> — Andrej Karpathy

Vault ini berisi **beberapa wiki independen** (multi-wiki), satu per domain
kehidupan/kerja pengguna. Tiap domain punya folder sendiri dengan struktur 3-layer
yang identik.

## Wiki yang Aktif

| Folder | Domain | Status |
|---|---|---|
| [medium-wiki/](medium-wiki/CLAUDE.md) | Riset artikel Medium (historical journalism, gaya Rama Anindya) | initialized, 0 sumber |
| [career-wiki/](career-wiki/CLAUDE.md) | Karier AI Engineering (roadmap, riset tools) | initialized, 0 sumber |
| [client-wiki/](client-wiki/CLAUDE.md) | Freelance WordPress (konteks per-klien eksternal) | initialized, 0 sumber |
| [finance-wiki/](finance-wiki/CLAUDE.md) | Personal finance (dana darurat, alokasi) | initialized, 0 sumber |
| [nexana-studio-wiki/](nexana-studio-wiki/CLAUDE.md) | Brand identity & materi bisnis Nexana Studio (milik sendiri, bukan klien) | initialized, 1 sumber |
| [linux-cachyos-wiki/](linux-cachyos-wiki/CLAUDE.md) | Belajar Linux & CachyOS (baru pindah dari OS lain) | initialized, 0 sumber |

## Struktur Setiap Wiki (identik di semua domain)

```
<domain>-wiki/
├── CLAUDE.md      ← schema spesifik domain ini — BACA INI dulu sebelum kerja di domain terkait
├── raw/           ← sumber mentah, immutable, read-only bagi LLM
└── wiki/
    ├── index.md       ← katalog semua halaman
    ├── log.md         ← catatan kronologis (ingest/query/lint)
    ├── overview.md    ← sintesis level-tertinggi domain ini
    ├── entities/      ← halaman orang/organisasi/produk/tool/klien
    ├── concepts/      ← halaman ide/konsep/tema
    └── sources/       ← ringkasan per-sumber
```

## Aturan Global (berlaku di semua domain)

1. **raw/ tidak pernah diubah.** LLM baca, tidak pernah menulis ke sana. Ini source of truth.
2. **wiki/ sepenuhnya milik LLM.** User baca & browse (via Obsidian), LLM yang menulis & maintain.
3. **Setiap halaman wiki wajib punya YAML frontmatter**: `type, title, description, tags, timestamp, sources`.
4. **Setiap klaim faktual wajib disitasi** ke file spesifik di raw/.
5. **Halaman baru HANYA lahir dari ingest sumber baru di raw/** — bukan dari hasil
   obrolan/sintesis tanpa sumber baru. Ini mencegah wiki "mengarang" halaman dari halusinasi.
6. **Update in-place**, jangan duplikasi halaman, kalau itu cuma atribut/pembaruan dari
   entitas/konsep yang sudah ada.
7. **Jangan hapus apa pun secara sepihak** (baik saat ingest maupun lint) — selalu flag
   untuk approval user, jangan langsung eksekusi.
8. **Sebelum mengerjakan ingest/query/lint di domain tertentu, baca CLAUDE.md di folder
   domain itu dulu** — file itu extend/override aturan global ini dengan gaya penulisan
   dan workflow spesifik domain (mis. sensitivitas data di client-wiki/finance-wiki).

## Tiga Alur Kerja Inti

Detail lengkap tiap alur (termasuk hal spesifik per domain) ada di `CLAUDE.md` masing-masing wiki.

- **INGEST** — sumber baru masuk `raw/` → baca → diskusi poin kunci dengan user →
  tulis/update `wiki/sources/` → update `index.md` → update entity/concept terkait
  (biasanya 10-15 halaman kena dampak) → catat di `log.md`
- **QUERY** — baca `index.md` dulu untuk cari halaman relevan → drill ke halaman yang
  relevan → sintesis jawaban dengan sitasi → kalau jawabannya bernilai jangka panjang,
  tawarkan untuk difilekan balik sebagai halaman baru
- **LINT** — jalankan berkala: cek kontradiksi antar halaman, klaim usang/superseded,
  halaman orphan (tanpa inbound link), konsep penting yang disebut tapi belum punya
  halaman sendiri, cross-reference hilang, gap data

## Catatan Skala & Anti-Pattern

- Kalau sebuah sumber sudah ringkas & tunggal, **jangan paksa** buat halaman wiki
  terpisah — kalau rasio kompresinya negatif (halaman jadi lebih besar dari sumbernya),
  biarkan agent grep langsung ke raw/.
- Wiki hanya se-update terakhir kali di-ingest — **bukan sistem live-sync**.
- Jadwalkan LINT secara berkala (mis. tiap 10-20 ingest per domain) — drift diam-diam
  (cross-reference nggak ke-update, halaman jadi stale) adalah failure mode #1.
- Ingest satu-per-satu dan tetap terlibat (baca ringkasan, arahkan penekanan) di ~20
  sumber pertama tiap domain, baru pertimbangkan batch setelah percaya sama kualitas
  sintesisnya.
- Jawaban query yang bagus harus difilekan balik ke wiki — jangan biarkan hilang di
  chat history, supaya eksplorasi ikut compound seperti sumber yang di-ingest.

## Data Sensitif

`client-wiki/` dan `finance-wiki/` berpotensi berisi data pribadi/klien yang sensitif
(brief klien, statement bank). Isi `raw/` di kedua folder itu di-gitignore secara
default (lihat `.gitignore` di root) — jangan hapus aturan itu tanpa memastikan repo
tetap private atau data sudah di-review dulu.

## Sumber Pola Ini

Andrej Karpathy, gist `llm-wiki.md` —
https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
