# CLAUDE.md — finance-wiki

## Tujuan Wiki

Melacak alokasi keuangan pribadi (BCA/Jago/Krom, dana darurat) dan keputusan
alokasi yang berubah dari waktu ke waktu — pola "personal: tracking goals".

Lihat aturan global di [../CLAUDE.md](../CLAUDE.md) — dokumen ini extend, bukan
menggantikan.

## PENTING: Sensitivitas Data

Folder ini berisi data finansial pribadi. `raw/` di domain ini sudah di-gitignore
secara default (lihat `.gitignore` di root vault). **Jangan** commit statement
bank/mutasi mentah ke remote git publik. Jangan simpan nomor rekening lengkap
atau PII sensitif lain di body halaman wiki — cukup nama bank & tujuan akun.

## Struktur Direktori

- `raw/` — statement bank, catatan alokasi bulanan (screenshot/CSV/catatan
  manual). Immutable.
- `wiki/entities/accounts/` — satu halaman per rekening/bank (BCA, Jago, Krom, dst)
- `wiki/concepts/goals/` — dana darurat, target alokasi, target tabungan
- `wiki/sources/` — ringkasan per-sumber (mis. ringkasan statement bulan tertentu)
- `wiki/index.md`, `wiki/log.md`, `wiki/overview.md`
- `wiki/ledger.md` — snapshot alokasi bulanan (angka agregat, bukan detail
  transaksi mentah)

## Konvensi Halaman

Frontmatter wajib: `type (entity|concept|source), title, description, tags, timestamp, sources`

Field tambahan untuk `entities/accounts/`: `account_type`
(checking/savings/emergency-fund), `last_balance_check` (tanggal)

## Alur INGEST

1. Baca statement/catatan baru dari `raw/`
2. Update halaman account terkait (saldo, tanggal cek terakhir)
3. Update `wiki/ledger.md` dengan snapshot bulan tersebut
4. Update concept/goals kalau ada perubahan target (mis. target dana darurat
   naik/turun)
5. Update `index.md`, log ke `log.md`

## Alur QUERY

Baca `ledger.md` untuk tren cepat → drill ke halaman account/goal spesifik kalau
butuh detail.

## Alur LINT

Cek tambahan: saldo account yang belum di-update dalam waktu lama; target dana
darurat vs. saldo aktual — flag gap-nya secara eksplisit.

## Gaya Penulisan

Ringkas, numerik, faktual. Tidak perlu narasi panjang — angka + tanggal +
interpretasi singkat.
