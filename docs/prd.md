# PRD

## Overview
Integrasi fitur pendelegasian subagent ke dalam workflow 7-fase pada repositori template ini. Primary Agent akan berfungsi sebagai "Orkestrator" yang mengawasi alur kerja dan memanipulasi *state artifacts*, sementara subagent difungsikan sebagai "Pekerja Lepas" yang menjalankan tugas pencarian/analisis (pada fase eksplorasi) atau penulisan kode (pada fase eksekusi) pada *process* latar belakang yang terisolasi.

## Problem statement
Saat ini alur kerja 7-fase pada repositori master ini beroperasi secara linear dan hanya bergantung penuh pada satu agen tunggal (Primary Agent). Ketika dihadapkan pada tugas yang kompleks (seperti mencari banyak URL, mengeksplorasi kode yang luas, atau mencoba perbaikan kode TDD), alur kerja menjadi lambat karena beban terpusat pada *context window* agen utama, tanpa memanfaatkan ekstensi *subagent* yang sudah tersedia secara lokal.

## Desired outcome
Template `.pi/skills/` (khususnya `research-me`, `prototype-me`, dan `execute-me`) serta panduan inti `AGENTS.md` diperbarui agar memiliki kemampuan memanggil subagent secara aman, modular, dan paralel, tanpa melanggar prinsip "gates" 7-fase (misalnya, tetap mempertahankan aturan 1 tiket per eksekusi).

## Users and actors
- Primary users: AI Agent (sebagai pengguna dari repositori *skill* ini).
- Secondary users: Manusia (Pengguna yang memerintahkan AI untuk membangun aplikasi).
- Internal actors or systems involved:
  - **Primary Agent (Orkestrator)**: Membaca `.pi/skills`, mendelegasikan tugas ke subagent, menganalisis hasil, dan memperbarui `docs/`.
  - **Subagent (Pekerja)**: Menerima perintah paralel, melakukan eksplorasi/penulisan kode di latar belakang, dan mengembalikan laporan tanpa mengedit status tiket.

## Scope
- In scope:
  - Pembaruan dokumen panduan `AGENTS.md` (menetapkan aturan Orkestrator vs Pekerja).
  - Pembaruan berkas `.pi/skills/research-me/SKILL.md`.
  - Pembaruan berkas `.pi/skills/prototype-me/SKILL.md`.
  - Pembaruan berkas `.pi/skills/execute-me/SKILL.md`.
- Included workflows:
  - Fase Eksplorasi (Research & Prototype): Penggunaan agen `explore` untuk eksplorasi dan web search/scrape secara paralel tanpa *write access*.
  - Fase Eksekusi: Penggunaan agen `execute` (TDD, baca/tulis/edit) dengan instruksi *prompt* steril dan *context* terisolasi.

## Non-goals
- Explicitly out of scope for this phase:
  - Mengubah skrip validasi `.mjs` pada `scripts/` (pembatasan ini hanya ditegakkan melalui panduan *prompt* dan *markdown invariants*).
  - Mengubah kode ekstensi subagent di luar repositori (misal, merubah core Node.js ekstensi).
  - Mengubah alur kerja `prd-me`, `issues-me`, `absorb-me` (fase perencanaan tetap manual/tunggal untuk kualitas).

## User experience and behavior
**Pada `research-me` & `prototype-me`:**
Saat AI menerima permintaan eksplorasi yang besar, ia akan membuat *prompt* steril, memanggil `subagent_type: explore` (mungkin beberapa sekaligus secara paralel), memantau hasil lewat `get_subagent_result`, lalu merangkum temuannya ke dalam `docs/research.md` atau `docs/prototype/`.

**Pada `execute-me`:**
Saat AI mengambil 1 tiket AFK, ia merangkum konteks tiket ke dalam *prompt* mandiri tanpa `inherit_context`, mendelegasikan ke `subagent_type: execute`. Subagent akan menulis tes, memperbaiki kode, dan melapor kembali. AI Orkestrator kemudian memverifikasi *output*, lalu menandai tiket "done" di `docs/issues.md`.

## Functional requirements
1. Aturan `AGENTS.md` harus mendefinisikan secara eksplisit peran Orchestrator untuk Primary Agent, dan larangan mutlak bagi subagent untuk merubah file di folder `docs/`.
2. Instruksi di `SKILL.md` (`research-me`, `prototype-me`) harus menyertakan contoh pendelegasian paralel menggunakan tipe `explore`.
3. Instruksi di `execute-me/SKILL.md` harus memaksa Primary Agent untuk merakit konteks tiket *(isolated prompt assembly)* dengan `inherit_context: false` sebelum memanggil subagent bertipe `execute`.
4. Aturan invariant "satu tiket AFK per eksekusi" pada `AGENTS.md` harus tetap berlaku tanpa terkecuali, meskipun subagent digunakan.

## Edge cases
- Subagent mencoba menulis tiket secara sepihak: Dicegah dengan aturan tertulis di `AGENTS.md` (dan *prompt isolation*).
- *Scope creep* dari *hallucination*: Dicegah dengan aturan `inherit_context: false`, di mana *prompt* ke subagent sangat dibatasi.
- Primary Agent kebingungan antara tugasnya vs tugas Subagent: Diatasi dengan struktur panduan yang jelas di dalam `SKILL.md` (menggunakan bagian tersendiri: "Subagent Delegation Rules").

## Acceptance criteria
- [ ] `AGENTS.md` telah memuat bagian `Orchestrator vs Worker` atau `Subagent Delegation`.
- [ ] `.pi/skills/research-me/SKILL.md` telah diperbarui dengan panduan pendelegasian paralel.
- [ ] `.pi/skills/prototype-me/SKILL.md` telah diperbarui dengan panduan pendelegasian.
- [ ] `.pi/skills/execute-me/SKILL.md` telah diperbarui dengan aturan penyusunan *isolated prompt* ke agen `execute`.
- [ ] Dokumen `docs/issues.md` dapat diperbarui dan diproses dengan benar oleh `validate-readiness-gates.mjs` di langkah selanjutnya (skrip tidak terganggu).

## Constraints
- Technical constraints that affect behavior: Instruksi dalam `SKILL.md` tidak boleh terlalu rumit sehingga merusak pemahaman dasar agen tentang fase itu sendiri. Bagian pendelegasian (delegation) harus dibuat sebagai sub-klausul, bukan menimpa alur utamanya.

## Dependencies
- Ekstensi *subagent* lokal (`subagent`, `get_subagent_result`) dengan tipe `explore` dan `execute`.
- Hasil fase ideasi pada `docs/idea.md`.

## Open questions
- Tidak ada. Semua asumsi batas dan *constraints* telah diselesaikan di Fase Ideasi.

## Recommended next step
- Suggested next phase: Fase Perencanaan Implementasi (Issues) menggunakan `issues-me`.
- Why that is the right next step: Persyaratan produk (PRD) telah mendefinisikan perubahan berkas markdown yang spesifik secara utuh. Langkah selanjutnya adalah mengubah ini menjadi daftar pekerjaan (*kanban tickets*).

## Source artifacts
- `docs/idea.md`

## Handoff to Issues
- [x] Main user flows are clear
- [x] Acceptance criteria are testable enough for planning
- [x] Scope boundaries are explicit
- [x] Dependencies and constraints that affect slicing are visible
- [x] Material ambiguities that could break ticket breakdown are explicitly listed

Planning approval: approved for issues planning (correctness and scope)
Ready for next phase: yes
Primary blocker: none
