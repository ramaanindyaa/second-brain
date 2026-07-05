# Second Brain — Multi-Wiki (Pola Karpathy)

Vault ini adalah implementasi pola **LLM Wiki** Andrej Karpathy: LLM membangun
dan memelihara wiki markdown yang persisten, bukan sekadar retrieve-and-forget
seperti RAG standar.

Baca [CLAUDE.md](CLAUDE.md) di root untuk aturan main lengkap sebelum mulai
ingest sumber apa pun.

## Wiki yang Aktif

- [[medium-wiki/wiki/index|Riset Medium]] — historical journalism, gaya Rama Anindya
- [[career-wiki/wiki/index|Karier AI Engineering]] — roadmap & riset tools
- [[client-wiki/wiki/index|Freelance WordPress]] — konteks per-klien
- [[finance-wiki/wiki/index|Personal Finance]] — dana darurat & alokasi

## Cara Kerja Sehari-hari

1. **Ingest** — drop sumber baru ke `<domain>-wiki/raw/`, minta agent (mis.
   Claude Code) memprosesnya sesuai `<domain>-wiki/CLAUDE.md`
2. **Query** — tanya apa saja ke agent, dia baca `index.md` domain terkait dulu
   baru drill in ke halaman relevan
3. **Lint** — jalankan berkala untuk cek drift/kontradiksi/halaman orphan

Mulai dari domain yang paling sering kamu proses ulang — jangan ingest ke
keempat wiki sekaligus di hari pertama, biar polanya kelihatan hasilnya dulu
sebelum di-scale ke semua domain.