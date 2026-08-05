# Panduan Utama Workflow Project

Dokumen ini adalah SOP (Standard Operating Procedure) untuk semua project di lingkungan Windows dengan integrasi VSCode, OpenCode, Magic Context, dan GitHub CLI (`gh`). Inti dokumen ini adalah workflow GitHub yang profesional: issue-driven, branch terpisah, Pull Request, CI hijau, dan commit yang atomik.

Di awal setiap sesi, baca ulang dokumen ini (khususnya bagian [Core Rules](#core-rules), [Merge Strategy](#merge-strategy), [Branching Model](#branching-model), [Operating Steps](#operating-steps), dan [Format Specs](#format-specs)), lalu pilih flow yang relevan dengan situasi. Semua aturan wajib dipatuhi tanpa pengecualian kecuali yang disebutkan eksplisit dalam dokumen ini, untuk menjaga kebersihan kode dan keamanan repositori.

## Core Rules

### Workflow Principles

* Issue-Driven: Tidak boleh ada perubahan kode atau konfigurasi tanpa didukung GitHub Issue (kecuali saat setup/adopsi workflow, lihat [Project Bootstrap](#project-bootstrap) dan [Workflow Adoption](#workflow-adoption)). Issue dapat berasal dari item `docs/IMPROVEMENTS.md` yang telah diverifikasi (lihat [Findings and Planning](#findings-and-planning)) atau dibuka langsung oleh pengguna umum (bug report / feature request).
* GitHub Flow Ketat: Semua fitur, perbaikan bug, dokumentasi, atau tugas lain (termasuk `chore`) wajib dibuat di branch terpisah yang bersumber dari branch `main`.
* No Direct Push: Dilarang keras melakukan push langsung ke branch `main`. Semua perubahan wajib melalui Pull Request (PR).
* Atomic Conventional Commits: Setiap commit hanya boleh berisi 1 perubahan logis, ditulis dalam bahasa Inggris (lowercase setelah tanda titik dua), tanpa titik di akhir kalimat.
* No Force-Push: Jika ada kesalahan setelah push ke remote branch, perbaiki dengan membuat commit baru (fixup commit). Jangan di-amend lalu di-force-push. Satu-satunya pengecualian adalah pembersihan riwayat git ([Data Security and Public Readiness](#data-security-and-public-readiness)) yang hanya boleh dilakukan saat repo masih private.
* Public-Safe: Dilarang menyimpan file `.env`, token, secret, endpoint internal, atau nama instansi/institusi asli di dalam repositori atau riwayat Git.
* Preview Sebelum Commit: Seluruh perubahan kode wajib disajikan terlebih dahulu ke USER untuk direview dan disetujui sebelum commit dibuat, disertai penjelasan perubahan perilakunya. Detail penerapan dijelaskan di masing-masing flow.

### Repository Protection

Untuk mengunci aturan di atas, repositori wajib dikonfigurasi sebagai berikut:

* Aktifkan Branch Protection pada `main`.
* Aktifkan status check CI `build` sebagai syarat wajib (require status checks to pass) sebelum merge.
* Atur `enforce_admins=false` agar AGENT dapat melakukan merge PR sendiri atas izin USER menggunakan bypass `--admin`. Perlu dipahami: `enforce_admins=false` membuat GitHub secara teknis mengizinkan admin push langsung ke `main` (proteksi tidak diterapkan ke admin). Oleh karena itu, No Direct Push adalah aturan SOP yang tetap mengikat siapa pun yang mengeksekusi perintah git (termasuk AGENT yang bertindak atas nama admin): push langsung ke `main` dilarang, satu-satunya pengecualian teknis bypass adalah `gh pr merge --admin`.
* Aktifkan `delete_branch_on_merge=true` untuk menghapus branch otomatis setelah di-merge.
* Aktifkan ketiga metode merge (`allow_merge_commit=true`, `allow_squash_merge=true`, `allow_rebase_merge=true`). Metode mana yang dipakai untuk sebuah PR ditentukan per kasus sesuai [Merge Strategy](#merge-strategy), bukan satu metode tetap untuk semua PR.
* Aktifkan Dependabot untuk memeriksa update secara berkala (weekly) pada ecosystem yang relevan (`composer`, `npm`, `github-actions`); file konfigurasi `dependabot.yml` dibuat saat [Project Bootstrap](#project-bootstrap), langkah 3, dengan isi mengikuti [Format Specs > Dependabot Config](#dependabot-config).

### Merge Strategy

Pilihan metode merge ditentukan per PR, bukan per repo, dengan prioritas sebagai berikut. Tie-breaker: keputusan ditentukan oleh siapa penulis PR terlebih dahulu, baru jumlah author pada branch.

1. PR dari **non-collaborator** (contributor eksternal, termasuk bot seperti Dependabot) → **squash merge** (`gh pr merge --squash --delete-branch`). Commit asli pada branch tidak dikendalikan oleh project ini, sehingga seluruh isi PR disatukan menjadi satu commit bersih di `main`.
2. PR dari **collaborator** dengan branch yang hanya di-author oleh 1 collaborator → **rebase merge** (`gh pr merge --rebase --delete-branch`). Commit atomik yang sudah ditulis dengan disiplin tetap dipertahankan utuh di atas `main`, dan riwayat `main` tetap linear.
3. PR pada branch yang **di-share oleh 2+ collaborator** (fitur tim yang dikerjakan bersama) → **merge commit** (`gh pr merge --merge --delete-branch`). Jejak integrasi kolaborasi dipertahankan sebagai satu kesatuan; trade-off wajar yang diterima adalah `main` tidak lagi murni linear dalam skenario ini.
4. PR yang **hanya berisi 1 commit** → cukup **squash merge** (hasilnya identik dengan rebase untuk satu commit).

Catatan: metode merge dan model branch adalah dua keputusan yang independen. Metode merge menentukan *bagaimana* satu PR masuk ke branch target, sedangkan model branch menentukan *dari mana ke mana* alur PR tersebut (lihat [Branching Model](#branching-model)).

### Boundaries

* Bahasa Komunikasi: Percakapan, diskusi, dan instruksi kerja antara USER dan AGENT mengikuti bahasa yang dipakai USER (dapat Bahasa Indonesia atau English). Namun, seluruh tulisan yang masuk ke file dokumentasi dalam proyek (kode, komentar skrip, pesan commit Git, isi file issue, berkas `IMPROVEMENTS.md`, `README`, dan dokumen formal lainnya) wajib ditulis menggunakan English International karena akan dibaca publik. Bahasa elemen UI bersifat fleksibel mengikuti permintaan USER, apakah UI berbahasa English atau berbahasa Indonesia.
* Aturan Pembungkusan Baris (No Hardwrapping): Secara umum dilarang keras memotong baris teks paragraf secara manual di dalam dokumen markdown. Biarkan baris teks memanjang secara kontinu agar pembacaan teks di web/editor modern lebih rapi. Pemotongan baris (line-break) hanya diizinkan untuk memisahkan baris pada tabel, daftar poin (bullet points), atau blok kode program. Pengecualian diberikan pada dokumen yang secara konvensional memang menggunakan hardwrapping, contohnya `LICENSE` yang mengikuti format standar; selain itu gunakan softwrapping.
* Batch Berhenti Otomatis: Setiap kali AGENT menyelesaikan satu putaran proses verifikasi kode atau implementasi fitur dalam satu kelompok (batch), sistem wajib menghentikan eksekusi secara total di akhir batch tersebut. AGENT dilarang melanjutkan ke kelompok kerja berikutnya tanpa adanya persetujuan atau perintah baru dari USER.
* Persetujuan Perubahan Non-File: Perubahan pengaturan GitHub yang bukan commit (branch protection, visibility, labels, settings repo) dieksekusi via `gh api` / `gh repo edit` dan wajib disajikan dulu untuk disetujui USER — jangan dieksekusi langsung tanpa konfirmasi.
* Tanya Saat Ambigu: Jika instruksi tidak jelas, atau ada pilihan besar dengan trade-off yang signifikan, tanyakan ke USER sebelum bertindak. Jangan berasumsi.
* Isolasi Folder Kerja: Peralatan eksternal (tool CLI, linter, dll) yang perlu diinstal di dalam project mengikuti konvensi project (lokasi install ditentukan sesuai jenis project, misalnya virtual environment) dan masuk `.gitignore` — dilarang menginstal ke sistem global. File sementara (log, catatan, scratch) ditulis ke folder `temp/` di dalam project yang masuk `.gitignore`, tidak boleh berceceran di luar project.
* Lingkungan Windows: Semua file sumber disimpan sebagai `UTF-8` tanpa `BOM` (kecuali memang diperlukan) karena encoding lain seperti `Windows-1252` menghasilkan karakter rusak atau error parsing. Jangan berasumsi perintah default (`cmd`/`PowerShell`) berperilaku seperti `bash` karena banyak dokumentasi ditulis untuk Linux; jika sebuah perintah gagal dengan cara yang tidak wajar, periksa dulu apakah perbedaan shell/OS penyebabnya sebelum mengubah kode. Argumen gh yang mengandung backtick atau karakter khusus sering rusak ketika disisipkan langsung di PowerShell; untuk body issue/PR yang panjang, tulis body ke file sementara di `temp/` lalu gunakan opsi `--body-file`.

### Workspace and AI Context

* Magic Context wajib digunakan penuh untuk mengatur context chat. Perintah bawaan `/compact` dari OpenCode tidak digunakan lagi karena Magic Context sudah mengelola pemadatan context secara otomatis.
* Magic Context menyinkronkan dua file pemetaan project: `ARCHITECTURE.md` (cetak biru arsitektur sistem) dan `STRUCTURE.md` (peta folder dan file proyek). Kedua file ini dapat berubah sewaktu-waktu tanpa sepengetahuan AGENT secara langsung; AGENT wajib membacanya ulang sebelum mulai bekerja dan selalu mengikuti state terkini. Kedua file dikelola otomatis oleh Magic Context, sehingga AGENT umumnya tidak perlu menyentuhnya dan tidak melalui Flow PR. Pengecualian: jika perubahan arsitektur atau struktur sangat signifikan dan berdampak langsung pada hasil implementasi, AGENT boleh memperbarui kedua file tersebut secara langsung saat itu juga.
* AGENT OpenCode memiliki izin penuh untuk menjalankan perintah terminal dalam lingkup project folder, termasuk mengeksekusi perintah GitHub CLI (gh) yang sudah terautentikasi. File sementara ditulis ke folder `temp/` di dalam project (lihat [Boundaries](#boundaries), Isolasi Folder Kerja). Perintah di luar project folder memerlukan izin eksplisit dari USER.

## Branching Model

Model branch adalah kebijakan tentang susunan branch yang dipakai project dan bagaimana alur sebuah perubahan berpindah dari satu branch ke branch lain. Model yang dipilih project ditentukan oleh kebutuhan pengembangan dan dapat berubah seiring arah pengembangan; pilih sesuai [tabel pemilihan](#tabel-pemilihan-model).

### Definisi Branch

* **`main`** — branch paling utama dan paling stabil. Isinya selaras dengan rilis terakhir yang sudah dikeluarkan. Rilis dipotong dari `main`.
* **`dev` / `develop`** — branch untuk pengerjaan rilis berikutnya. Isinya adalah salinan (copy) dari `main` pada saat dibuat, lalu berkembang seiring fitur-fitur digabung. Branch ini dibuat ketika arah atau tujuan pengembangan sudah jelas dan beberapa fitur akan diluncurkan dalam satu rilis.
* **Branch kerja** — `feature/`, `release/`, `fix/`, `hotfix/`, dan sejenisnya. Branch yang fleksibel, umumnya bercabang dari `main`, tetapi jika `dev`/`develop` tersedia, branch kerja mengambil dari `dev`/`develop` terlebih dahulu untuk mengerjakan satu fitur atau satu perbaikan yang akan menjadi bagian rilis berikutnya.

### Alur Dasar

```
main  ──────────────────────────────────────────►  (selaras dengan rilis terakhir)
  │
  └── dev ── feature / fix ──► dev ──► main        (pengerjaan rilis berikutnya)
  │
  └── hotfix ──► main  ──► release (segera)
       └──► dev  (digabung balik agar dev ikut terbawa perbaikan)
```

* Perubahan fitur/perbaikan dikerjakan di branch kerja yang bercabang dari `dev`/`develop` bila tersedia, atau dari `main` bila tidak ada `dev`.
* Setelah selesai dan lolos review, branch kerja di-merge kembali ke sumbernya (`dev` atau `main`).
* Saat `dev` sudah lengkap untuk rilis berikutnya, `dev` di-merge ke `main` dan rilis dilakukan dari `main`.
* Bug kritikal pada rilis yang sudah beredar ditangani lewat jalur **hotfix** terpisah: bercabang dari `main`, di-merge kembali ke `main` (untuk segera dirilis), lalu digabung juga ke `dev` agar perbaikan ikut pada rilis berikutnya.

### Tabel Pemilihan Model

| Model | Susunan branch | Kapan dipakai |
|-------|----------------|---------------|
| GitHub Flow (tanpa `dev`) | `main` + branch kerja | Project kecil / solo, deploy sering, arah pengembangan belum jelas, atau rilis tidak terjadwal |
| main + dev | `main` + `dev` + branch kerja | Beberapa fitur digabung dalam satu rilis berikutnya; arah pengembangan sudah jelas |
| GitFlow penuh | `main` + `dev` + `release/*` + `hotfix/*` | Rilis terjadwal dan perlu stabilitas paralel (fitur besar, tim besar) |

Catatan: model dengan `dev` (baris kedua dan ketiga) memakai alur hotfix yang sama, hanya saja `release/*` ditambahkan pada GitFlow penuh untuk menstabilkan satu rilis tanpa menahan pengerjaan fitur berikutnya.

## Operating Steps

### Project Bootstrap

> Trigger: USER ingin membuat project baru dan meminta AGENT menyiapkan struktur repositori dari nol.

1. USER meminta AGENT membuat project baru. AGENT menginisialisasi repositori: buat repositori di GitHub (`gh repo create`) dan setup local, lalu buat struktur folder dasar di repositori — `docs/` (dokumentasi), `temp/` (folder kerja sementara yang masuk `.gitignore`), folder kode sumber (`app/`, `src/`, atau sesuai stack) bila project memang memiliki kode aplikasi, serta guardrail files di root (`.gitignore`, `.editorconfig`, `.gitattributes`). Isi guardrail files mengikuti [Format Specs > Guardrail Files](#guardrail-files).
2. USER meminta AGENT menyiapkan dokumentasi standar. AGENT membuat `README.md`, `CHANGELOG.md`, `LICENSE`, `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, dan `SECURITY.md` mengikuti [Format Specs > Standard Docs](#standard-docs) dan [Format Specs > CHANGELOG](#changelog). AGENT **menyajikan hasil ke USER dan berhenti**.
3. USER menyetujui dokumentasi. AGENT menyiapkan folder `.github`: `workflows/ci.yml` (CI: lint + test + build), `ISSUE_TEMPLATE/` (template issue: `bug_report.md`, `feature_request.md`, `config.yml`), `PULL_REQUEST_TEMPLATE.md`, `RELEASE_NOTES_TEMPLATE.md`, dan `dependabot.yml`. Isi lengkap masing-masing mengikuti [Format Specs](#format-specs): [Issue Templates](#issue-templates), [Pull Request Template](#pull-request-template), [Release Notes Template](#release-notes-template), dan [Dependabot Config](#dependabot-config). AGENT juga membuat `docs/IMPROVEMENTS.md` dengan kerangka lifecycle dan ID Scheme (lihat [Format Specs > IMPROVEMENTS Structure](#improvements-structure)).
4. USER menyetujui struktur repositori. AGENT menerapkan semua pengaturan proteksi GitHub pada [Core Rules](#core-rules) (setelah mendapat persetujuan USER sesuai batasan "Persetujuan Perubahan Non-File" di [Boundaries](#boundaries)) — termasuk mengaktifkan ketiga metode merge sesuai [Merge Strategy](#merge-strategy) — dan memastikan Issues aktif.
5. USER meminta verifikasi akhir. AGENT mempush seluruh file via PR pertama (bukan langsung ke `main`), menunggu CI hijau, dan memastikan repositori layak dipublikasikan (lihat [Data Security and Public Readiness](#data-security-and-public-readiness)).

> Jika perintah GitHub CLI (gh) belum terautentikasi selama proses di atas, USER cukup menjalankan `gh auth login` yang disarankan AGENT lalu mengikuti arahan login di browser dari gh.

### Workflow Adoption

> Trigger: USER ingin menerapkan workflow ini pada project yang sudah ada (repo GitHub sudah berdiri) untuk membawanya ke standar [Core Rules](#core-rules) dan [Project Bootstrap](#project-bootstrap).

1. USER meminta AGENT mengaudit kondisi repo lama. AGENT memeriksa dan mencatat temuan sebagai item `docs/IMPROVEMENTS.md` yang mengikuti [Findings and Planning](#findings-and-planning). Hal-hal yang wajib diperiksa: status CI (workflow terakhir hijau atau merah), branch stale (branch lokal/remote yang tidak aktif atau sudah di-merge), data sensitif di riwayat (token, `.env`, endpoint internal, nama institusi — periksa dengan perintah di [Data Security and Public Readiness](#data-security-and-public-readiness)), kelengkapan guardrail files dan struktur folder (apakah sudah ada atau perlu dibuat), serta pengaturan proteksi GitHub yang ada. AGENT **menyajikan hasil audit ke USER dan berhenti**.
2. USER menyetujui hasil audit. AGENT menerapkan setiap langkah [Project Bootstrap](#project-bootstrap) pada repo lama: buat jika belum ada, verifikasi dan sesuaikan jika sudah ada. Inisialisasi repo dan konfigurasi proteksi cukup diverifikasi/diambil dari kondisi yang sudah berjalan, bukan dibuat dari nol.
3. USER meminta perbaikan hasil audit dikerjakan. AGENT mengerjakan tiap temuan setelah seluruh adopsi selesai, masing-masing melalui Flow yang sesuai ([Findings and Planning](#findings-and-planning) → Issue → [Code Implementation](#code-implementation)).

### Findings and Planning

> Trigger: Setiap kali ada ide fitur baru, bug yang ditemukan, atau rencana optimasi saat USER berdiskusi dengan AGENT.

1. USER memberitahukan ide atau temuan ke AGENT. Setelah itu, AGENT mencatat ide atau temuan tersebut ke berkas `docs/IMPROVEMENTS.md`. AGENT menggunakan ID dengan format `<LABEL>-<NNN>` (Contoh: `ENH-001` untuk enhancement, `BUG-002` untuk bug, `DOC-003` untuk documentation — lihat [Format Specs > IMPROVEMENTS Structure](#improvements-structure)). AGENT menulis detail Problem dan Possible Fix. AGENT set status awal menjadi `recorded`.
2. USER meminta AGENT menyiapkan rencana verifikasi terhadap semua item di `docs/IMPROVEMENTS.md`. AGENT menyusun rencana verifikasi seluruh item yang statusnya `recorded`, dan menampilkan rencana verifikasi ide atau temuan, dengan mengelompokkan ide / temuan yang bisa diverifikasi sekaligus (karena memiliki file dan/atau scope yang sama) menjadi batch yang sama.
3. USER meminta AGENT mulai verifikasi batch. AGENT mulai verifikasi terhadap item-item di batch tersebut, satu per satu, dengan 3 tahapan:
   * Tahap 1 (Verifikasi Kode): AGENT periksa kode asli di editor untuk memastikan masalahnya valid.
   * Tahap 2 (Verifikasi Online): AGENT verifikasi solusi yang direncanakan ke dokumentasi resmi atau sumber online lain agar tidak salah arah.
   * Tahap 3 (Sajikan Hasil): AGENT berhenti dan sajikan hasilnya.
4. Jika Hasil Valid: USER meminta AGENT untuk membuat GitHub Issue baru menggunakan GitHub CLI (`gh issue create`). AGENT menerapkan label GitHub default yang sesuai dengan kode ID (misalnya `enhancement`, `documentation`, `bug`). AGENT memastikan ID item ditulis pada judul issue, bukan dijadikan label custom. AGENT mengisi body issue dengan detail teknis lengkap, lalu memperbarui status di markdown menjadi `verified` beserta nomor Issue: `#N` dan Actual Fix. AGENT berhenti, lalu menyajikan hasilnya ke USER.

   Jika Hasil Invalid: AGENT langsung mengubah status di markdown menjadi `rejected` dan jangan dilanjutkan ke proses tracking berikutnya. AGENT berhenti, lalu menjelaskan alasan item tersebut ditolak.
5. USER meminta `docs/IMPROVEMENTS.md` diarsipkan. AGENT menyalin `docs/IMPROVEMENTS.md` ke `docs/archived/IMPROVEMENT_YYYY-MM-DD.md`, lalu AGENT mengosongkan kembali file `docs/IMPROVEMENTS.md` untuk batch berikutnya. Catatan: untuk menyelesaikan item yang berstatus `verified`, lihat [Code Implementation](#code-implementation); setelah issue di-merge, status item menjadi `implemented` dan kolom Issue tetap mencatat nomor Issue, bukan nomor PR.

### Code Implementation

> Trigger: Ada Issue yang sudah berstatus `verified` dan siap dikerjakan, atau perubahan kode yang telah disetujui USER.

1. USER memerintahkan AGENT mengerjakan Issue `#N`. AGENT memastikan repositori lokal up-to-date dengan server, lalu membuat branch baru dari `main` dengan prefix yang sesuai:

```
git switch main
git pull origin main
git checkout -b <prefix>/<deskripsi-kebab-case>
```

Pilihan Prefix: `feature/`, `fix/`, `chore/`, `docs/`, `refactor/`. Selesaikan 1 Issue per 1 branch. Penggabungan beberapa issue hanya boleh dilakukan jika scope dan/atau file yang diubah sama persis demi efisiensi PR.
2. USER meminta AGENT memverifikasi perubahan secara lokal sebelum commit. AGENT menjalankan verifikasi sesuai stack project yang sedang dikerjakan:
   * Syntax check atau lint untuk bahasa yang dipakai (contoh PHP: `php -l`; JavaScript/TypeScript: `eslint` / `tsc`).
   * Jalankan code-style fixer hanya pada file yang diubah saja (jangan global, untuk menghindari noise baris CRLF Windows). Contoh PHP: `vendor/bin/php-cs-fixer fix`.
   * Jalankan unit test sesuai runner project (contoh PHP: `vendor/bin/phpunit`; Node: `npm test`).
   * Jalankan build jika ada perubahan pada UI, CSS, atau JavaScript (contoh: `npm run build`).
   * Periksa jalur URL/routing jika ada perubahan route aplikasi.
   * Periksa kembali kepatuhan kode sesuai file `CONTRIBUTING` project: semua output di-escape (anti-XSS) sesuai API framework yang dipakai, ada token CSRF untuk setiap form, kode ber-tipe data dan terdokumentasi sesuai konvensi bahasa, serta tidak ada sisa kode debug.
   * Checklist verifikasi di atas sejajar dengan checklist di [Format Specs > Pull Request Template](#pull-request-template) — keduanya harus konsisten.
3. USER meminta AGENT menampilkan preview perilaku. AGENT menampilkan preview perilaku ke log chat dan **berhenti menunggu persetujuan USER**. "Preview perilaku" berarti deskripsi dari sudut pandang pengguna atau efek aplikasi (contoh: "login kini menampilkan pesan saat token kedaluwarsa"), bukan lokasi file atau detail baris kode.
4. USER menyetujui preview. AGENT melakukan commit mengikuti format [Format Specs > Commit Messages](#commit-messages).
5. USER memerintahkan AGENT untuk push dan membuat PR. AGENT push branch ke GitHub dan buat Pull Request otomatis lewat GitHub CLI:

```
gh pr create --body "Fixes #N"
```

Jika body PR panjang dan mengandung karakter khusus, gunakan `--body-file` (lihat [Boundaries](#boundaries), Lingkungan Windows). Lalu AGENT menunggu CI (GitHub Actions build) selesai. Jika gagal, perbaiki kodenya di lokal lalu push sebagai commit baru (fixup commit) ke branch yang sama. Jangan lakukan amend.
6. USER memerintahkan merge setelah indikator CI sudah hijau. AGENT melakukan merge secara administratif menggunakan hak akses bypass admin, dengan metode yang dipilih sesuai [Merge Strategy](#merge-strategy):

```
gh pr merge --squash --delete-branch --admin   # PR non-collaborator, atau PR yang hanya 1 commit
gh pr merge --rebase --delete-branch --admin   # PR collaborator, branch di-author 1 orang
gh pr merge --merge  --delete-branch --admin   # branch di-share 2+ collaborator (fitur tim)
```

7. USER meminta AGENT melakukan bookkeeping. AGENT kembali ke `main` dan sinkronkan (`git switch main`; `git pull origin main`). Perbarui file `docs/IMPROVEMENTS.md` menjadi `implemented` dan tambahkan catatan rilis baru di bawah kategori `[Unreleased]` pada file `CHANGELOG.md` (format mengikuti [Format Specs > CHANGELOG](#changelog)). Ingat, perubahan dokumen ini juga wajib melalui proses branch dan PR (tidak boleh commit langsung ke `main`).

### CI and Git Rescue

> Trigger: CI gagal di PR, commit tidak sengaja masuk ke `main`, atau push ditolak (non-fast-forward).

1. USER melaporkan CI gagal di PR. AGENT memperbaiki kodenya secara lokal, lalu membuat commit baru di branch tersebut dan push kembali. CI di GitHub akan mendeteksi dan mengulang tes otomatis. AGENT menunggu hasilnya dan **menyajikan hasil ke USER, lalu berhenti**.
2. USER melaporkan ada commit yang tidak sengaja masuk ke `main` (misal commit tanpa branch terpisah). AGENT membatalkan segera dan mengamankan pekerjaan ke branch baru:

```
git reset --hard HEAD~1
git checkout -b chore/salvaged-work
```

Jika commit hilang dari riwayat lokal, ambil kembali dari `reflog`:

```
git cherry-pick <sha-commit-dari-reflog>
```

Setelah itu, AGENT push branch tersebut, buat PR, tunggu CI hijau, dan merge via `--admin` dengan metode sesuai [Merge Strategy](#merge-strategy). AGENT **menyajikan hasil ke USER, lalu berhenti**.
3. USER melaporkan push ditolak (non-fast-forward). AGENT mengecek penyebabnya. Jika karena aturan branch protection, gunakan jalur PR. Jika karena ada perbedaan riwayat sinkronisasi dengan remote server, reset branch lokal agar sama persis dengan `origin`:

```
git fetch origin
git reset --hard origin/main
```

AGENT **menyajikan hasil ke USER, lalu berhenti**.

### Dependabot PRs

> Trigger: Dependabot membuka PR pembaruan dependensi (`composer`, `npm`, `github-actions`).

1. USER meminta AGENT memeriksa PR Dependabot yang baru dibuka. AGENT memeriksa PR yang ada, mengklasifikasikan risikonya (rendah: versi minor, patch, dependensi developer, atau GitHub Actions dengan CI hijau; tinggi: versi major yang berpotensi merusak kode lama), lalu **menyajikan daftar beserta klasifikasi risikonya ke USER dan berhenti**.
2. USER memerintahkan merge untuk PR yang disetujui:
   * Risiko Rendah: AGENT langsung melakukan merge menggunakan perintah squash (Dependabot adalah bot non-collaborator, sesuai [Merge Strategy](#merge-strategy)):

```
gh pr merge --admin --squash
```

   * Risiko Tinggi: AGENT melakukan pengujian lokal terlebih dahulu:

```
gh pr checkout <nomor-pr-dependabot>
```

Jalankan perintah build lokal (seperti `npm run build`) karena Dependabot hanya mengubah file manifest dan tidak menjalankan proses build/kompilasi (misal menghasilkan aset ke folder publik). Tes fungsi aplikasi oleh AGENT menggunakan tool atau script test yang tersedia. Jika ada penyesuaian kode, commit langsung ke branch Dependabot tersebut, pastikan CI di GitHub kembali hijau, lalu merge via squash (`--admin`), sesuai [Merge Strategy](#merge-strategy).

Catatan: PR Dependabot yang mengubah file di `.github/workflows/` hanya bisa di-merge jika token gh memiliki scope `workflow`. Jika gagal, refresh token dengan perintah: `gh auth refresh -h github.com -s workflow`.

### Release Process

> Trigger: Versi aplikasi siap dirilis mengikuti Semantic Versioning (SemVer) dengan format versi `vMAJOR.MINOR.PATCH`.

1. USER meminta rilis versi `vX.Y.Z`. AGENT memastikan branch `main` lokal sudah ditarik (pull) dan sama persis dengan server remote, lalu mengedit file `CHANGELOG.md` (format mengikuti [Format Specs > CHANGELOG](#changelog)): pindahkan semua daftar perubahan di kategori `[Unreleased]` ke bawah judul versi baru dengan format `## [0.X.0] - YYYY-MM-DD`, buat blok `[Unreleased]` baru yang kosong di bagian paling atas, lalu perbarui link perbandingan git di bagian bawah file. AGENT **menyajikan changelog yang diedit ke USER dan berhenti**.
2. USER menyetujui changelog. AGENT melakukan commit perubahan tersebut melalui jalur branch dan PR baru:

```
git checkout -b chore/release-v0.X.0
git add CHANGELOG.md
git commit -m "chore: release v0.X.0"
```

Push branch, buat PR, tunggu CI hijau, lalu merge via `--admin` dengan metode sesuai [Merge Strategy](#merge-strategy).
3. USER meminta AGENT membuat dan push tag versi baru. AGENT kembali ke branch `main`, lakukan pull, lalu buat dan push tag versi baru:

```
git switch main
git pull origin main
git tag v0.X.0
git push origin v0.X.0
```

4. USER meminta AGENT membuat rilis resmi. AGENT membuat rilis resmi di halaman GitHub menggunakan GitHub CLI, dengan mengisi release notes mengikuti template `.github/RELEASE_NOTES_TEMPLATE.md` (lihat [Format Specs > Release Notes Template](#release-notes-template)):

```
gh release create v0.X.0 --title "v0.X.0" --notes-file release_notes.md
```

5. USER meminta AGENT membersihkan file sementara. AGENT menghapus semua file sementara atau catatan rilis yang tidak diperlukan lagi dari workspace project.

### Data Security and Public Readiness

> Trigger: Repositori akan diubah menjadi publik, atau ada dugaan data sensitif di riwayat commit.

1. USER meminta repositori disiapkan untuk publik. AGENT melakukan audit sebelum publik: pastikan tidak ada data sensitif (seperti token, password, file `.env`, riwayat commit yang memuat log/internal, database dumps, atau nama instansi riil) yang tertinggal. Penting dibedakan: riwayat Git (seluruh commit masa lalu) dan file log (artefak runtime) keduanya harus bebas dari data sensitif. AGENT menjalankan pemeriksaan berikut sebagai bukti:

```
# Cari kata kunci sensitif di seluruh history
git log --all -S "<kata-kunci>"
# Cari file ter-track yang mencurigakan
git ls-files | Select-String -Pattern "(env|secret|key|credential|password)"
# Pastikan .env tidak pernah ter-track (output harus kosong)
git log --all --oneline -- .env
```

AGENT **menyajikan hasil audit ke USER dan berhenti**.
2. Jika ditemukan data sensitif di commit masa lalu, USER menyetujui pembersihan riwayat Git permanen. AGENT menghapusnya menggunakan tools `git-filter-repo` di dalam folder virtual environment Python terisolasi:

```
# Buat klon repositori sebagai cermin darurat
git clone --mirror https://github.com/<owner>/<repo>.git temp/purge.git
cd temp/purge.git
# Jalankan pembersihan menggunakan Python virtual environment
python -m venv .venv
& .\.venv\Scripts\Activate.ps1
pip install git-filter-repo
git-filter-repo --replace-text rules.txt
# Pastikan string sensitif benar-benar hilang dari log
git log -S "kata-kunci-sensitif-anda"
# Force push riwayat baru ke server remote
# PENTING: Force push ini HANYA boleh dilakukan saat status repo MASIH PRIVATE
git push origin --force --all
git push origin --force --tags
```

Setelah force-push, AGENT menyinkronkan repo kerja lokal: hapus stale refs (`git fetch --prune`) dan reset ke riwayat baru, lalu verifikasi dengan `git log --all -S "kata-kunci"` (bukan hanya `git log`) untuk memastikan tidak ada ref yang masih membawa data sensitif. AGENT **menyajikan hasil dan berhenti**.
3. USER menyetujui status repo diubah menjadi publik. AGENT menjalankan perintah GitHub CLI berikut:

```
gh repo edit --visibility public --accept-visibility-change-consequences
```

Segera aktifkan kembali semua pengaturan Branch Protection pada branch `main` begitu status repositori berubah menjadi publik. AGENT menyajikan hasil akhirnya ke USER.

## Format Specs

Bagian ini berisi format penulisan dan isi lengkap dokumen serta file konfigurasi. Setiap blok kode di sini adalah contoh isi yang harus dilengkapi sesuai kondisi project.

### Guardrail Files

File `.gitattributes` dan `.editorconfig` di root project mengontrol penanganan line ending. Keduanya wajib memakai `LF` untuk mencegah masalah CRLF di Windows: file yang diedit di Windows tersimpan sebagai `CRLF`, sedangkan repo dan CI (Linux) memakai `LF`, sehingga tool lint lokal menandai semua file berubah padahal isinya identik (false-positive).

```
# Isi file .gitattributes
* text=auto eol=lf

# Isi file .editorconfig
[*]
end_of_line = lf
```

Normalisasi line ending (jika project sudah berjalan dan ingin menormalkan seluruh file ke `LF` tanpa mencampurnya dengan perubahan fitur) dilakukan di branch terisolasi:

```
git checkout -b chore/normalize-global-line-endings
git add --renormalize .
git commit -m "chore: normalize all repository line endings to LF"
```

Lalu buat PR dan merge secara independen.

### Dependabot Config

Isi file `.github/dependabot.yml` — daftar ecosystem yang dipantau beserta lokasi manifestnya dan jadwal interval pembaruan. Daftar ecosystem disesuaikan dengan manifest yang benar-benar dipakai project (`composer` jika ada Composer, `npm` jika ada Node, `github-actions` untuk action).

```
version: 2

updates:
  - package-ecosystem: composer
    directory: "/"
    schedule:
      interval: weekly

  - package-ecosystem: npm
    directory: "/"
    schedule:
      interval: weekly

  - package-ecosystem: github-actions
    directory: "/"
    schedule:
      interval: weekly
```

### Issue Templates

Template issue di folder `.github/ISSUE_TEMPLATE/` otomatis muncul saat USER membuka issue baru. Sesuaikan isi template dengan stack dan istilah project.

**`bug_report.md`** — laporan bug berisi frontmatter YAML dan seksi-seksi terstruktur:

```markdown
---
name: Bug report
about: Report a problem with <Nama Project>
title: "[Bug] "
labels: bug
assignees: ''
---

**Describe the bug**
A clear and concise description of what the bug is.

**Steps to reproduce**
1. Open the page '...'
2. Click on '...'
3. See error

**Expected behavior**
A clear and concise description of what you expected to happen.

**Actual behavior**
What actually happened instead.

**Screenshots / video**
If applicable, add screenshots or a short recording to help explain your problem.

**Environment**
- OS: [e.g. Windows 11, macOS]
- Browser: [e.g. Chrome 120, Firefox 121]
- App version: [e.g. 0.2.0]

**Additional context**
Anything else relevant, e.g. whether the problem reproduces on a fresh login vs. after repeated use.
```

**`feature_request.md`** — usulan fitur dengan frontmatter yang sama dan seksi masalah/solusi/alternatif:

```markdown
---
name: Feature request
about: Suggest an idea for <Nama Project>
title: "[Feature] "
labels: enhancement
assignees: ''
---

**Is your feature request related to a problem?**
A clear and concise description of what the problem is. Ex. "I'm always frustrated when ..."

**Describe the solution you'd like**
A clear and concise description of what you want to happen.

**Describe alternatives you've considered**
A clear and concise description of any alternative solutions or features you've considered.

**Use case**
Describe the scenario where this feature would be used (e.g. "a data analyst importing weekly exports", "a customer reviewing their invoice").

**Additional context**
Add any other context or mockups about the feature request here.
```

**`config.yml`** — pengaturan template issue, misalnya mengarahkan laporan keamanan ke `SECURITY.md`:

```yaml
blank_issues_enabled: true
contact_links:
  - name: Security vulnerabilities
    url: https://github.com/<owner>/<repo>/blob/main/SECURITY.md
    about: Please report security vulnerabilities through SECURITY.md, not as a public issue.
```

### Pull Request Template

Isi file `.github/PULL_REQUEST_TEMPLATE.md` — kerangka deskripsi PR yang otomatis terisi saat USER/AGENT membuka PR. Checklist verifikasi lokal disesuaikan dengan stack project.

```markdown
## Summary

<!-- What does this PR do? Keep it short and clear. -->

## Related issues

<!-- Link the issue(s) this PR closes - use one "Fixes #N" line per issue. -->

Fixes #

## Checklist

- [ ] <syntax/lint command> passes on all modified files
- [ ] <build command> succeeds and committed assets are up to date
- [ ] <routes command> shows correct new routes (if routes changed)
- [ ] <code-style fixer command> passes (no style violations)
- [ ] <test command> is green (if tests exist)
- [ ] No debug code: <framework debug helpers>
- [ ] All user inputs validated server-side
- [ ] All POST forms include <CSRF token helper>
- [ ] All HTML output uses <output escaping helper>
- [ ] No unrelated files changed
- [ ] CHANGELOG updated if this is a user-facing change
- [ ] Behaviour verified in the browser if UI changed (attach a screenshot if useful)

## Screenshot (if applicable)

<!-- Paste screenshots here -->

## Notes for reviewers

<!-- Optional: anything the reviewer needs to know -->
```

### Release Notes Template

Isi file `.github/RELEASE_NOTES_TEMPLATE.md` — kerangka release notes dengan seksi yang sejajar dengan CHANGELOG agar konsisten.

```markdown
# <Nama Project> vX.Y.Z

Release date: YYYY-MM-DD

## Summary

_One or two sentences describing the theme of this release._

## Added

- _Describe new features, one line each, referencing the issue if applicable._

## Changed

- _Describe behaviour changes, including any migration notes._

## Fixed

- _Describe bug fixes, one line each, referencing the issue if applicable._

## Removed

- _Describe removed functionality, if any._

## Security

- _Describe security fixes, if any._

---

**Compare:** [`vX.Y.Z...vX.(Y-1).Z`](https://github.com/<owner>/<repo>/compare/vX.(Y-1).Z...vX.Y.Z)

_Delete this footer if this is the first release._
```

### IMPROVEMENTS Structure

File `docs/IMPROVEMENTS.md` adalah tracker semua ide fitur, bug, dan rencana optimasi yang muncul. Setiap item mengikuti lifecycle dan ditandai dengan ID.

**Lifecycle** — setiap item melewati alur berikut:

```
Recorded → Verified → Issue → Implemented → Archived
```

* `recorded` — dicatat saat ide/temuan dibahas, berisi `Problem` dan `Possible Fix`.
* `verified` — verifikasi mendalam mengonfirmasi valid; GitHub Issue dibuat dan nomornya dicatat, `Actual Fix` diisi. Jika invalid, item ditandai `rejected` dan tidak dilanjutkan.
* `implemented` — Issue terkait sudah di-merge; `Actual Implemented` dan `Changes` diisi, kolom `Issue` tetap mencatat nomor Issue (bukan PR).
* `archived` — dipindah ke `docs/archived/IMPROVEMENT_YYYY-MM-DD.md` hanya atas instruksi eksplisit USER, tidak pernah otomatis.

**ID Scheme** — ID item berformat `<LABEL_CODE>-<NNN>` yang dibangun dari label GitHub default (bukan label custom). Nomor berhitung per kode label:

| GitHub Label | Code |
|--------------|------|
| `bug` | BUG |
| `documentation` | DOC |
| `enhancement` | ENH |
| `duplicate` | DUP |
| `good first issue` | GFI |
| `help wanted` | HW |
| `invalid` | INV |
| `question` | QST |
| `wontfix` | WFX |

**Status Legend:**

| Status | Meaning |
|--------|---------|
| `recorded` | Logged, not yet deeply reviewed |
| `verified` | Deep review confirmed valid, Issue created |
| `rejected` | Deep review found it invalid — skipped |
| `implemented` | Linked Issue is closed |

**Field Guide:**

| Field | When filled | Content |
|-------|-------------|---------|
| `Problem` | `recorded` | The finding described as application behavior when possible; for non-application items (CI, settings, tooling), describe the impact instead. |
| `Possible Fix` | `recorded` | The initial fix plan — written while still `recorded`, so it is not guaranteed to work. |
| `Actual Fix` | `verified` | The final fix plan confirmed during deep review. |
| `Actual Implemented` | `implemented` | What was actually changed during implementation. |
| `Changes` | `implemented` | The behavior changes that result after `implemented`. |
| `Issue` | `verified` | The GitHub Issue number (`#N`); recorded once the issue is opened. |

**Item Template** — setiap item ditulis mengikuti kerangka berikut:

```markdown
### <ID> — <Title>
- **Status:** `recorded` | `verified` | `rejected` | `implemented`
- **Issue:** <#NN> | `—`
- **Recorded:** YYYY-MM-DD
- **Implemented:** YYYY-MM-DD | `—`
- **Problem:** ...
- **Possible Fix:** ...
- **Actual Fix:** ...
- **Actual Implemented:** ...
- **Changes:** ...
```

### CHANGELOG

File `CHANGELOG.md` mengikuti format Keep a Changelog. Semua perubahan dikumpulkan di kategori `[Unreleased]` selama pengembangan; saat rilis, isinya dipindahkan ke judul versi baru.

```markdown
# Changelog

## [Unreleased]

### Added

- _New features, one line each, referencing the issue if applicable._

### Changed

- _Behaviour changes, including any migration notes._

### Fixed

- _Bug fixes, one line each, referencing the issue if applicable._

## [0.1.0] - YYYY-MM-DD

### Added

- Initial release.

[Unreleased]: https://github.com/<owner>/<repo>/compare/v0.1.0...HEAD
[0.1.0]: https://github.com/<owner>/<repo>/releases/tag/v0.1.0
```

### Standard Docs

Dokumen standar di root project beserta fungsinya:

* `README.md` — halaman depan project: badges, quick start, screenshot bila ada.
* `LICENSE` — MIT atau lisensi lain sesuai kebutuhan; mengikuti format standar lisensi (boleh hardwrapped).
* `CONTRIBUTING.md` — aturan kontribusi dan workflow yang berlaku di project ini.
* `CODE_OF_CONDUCT.md` — kode etik komunitas (Contributor Covenant).
* `SECURITY.md` — petunjuk melaporkan kerentanan keamanan dan kebijakan respons.

### Commit Messages

Semua commit mengikuti standar Conventional Commits:

* Tipe: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `perf`, `build`, `ci`, `revert`.
* Format: Bahasa Inggris, huruf kecil setelah tanda titik dua, tanpa titik di akhir, 1 commit = 1 perubahan logis.
* Contoh: `"feat: add user profile endpoint"`, `"fix: handle empty token response"`.
