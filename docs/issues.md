# Issues

## Planning assumptions
- Source PRD: `docs/prd.md` (Integrasi Subagent ke 7-Phase Workflow)
- Planning scope: Memperbarui panduan invariant `AGENTS.md` dan tiga berkas *skill* (`research-me`, `prototype-me`, `execute-me`).
- Prototype winner: N/A (Tidak ada prototipe yang digunakan).
- Key constraints: Tidak merubah skrip `.mjs`. Primary Agent harus menggunakan `inherit_context: false` dan merakit *prompt* yang steril ke agen `execute`.

## Dependency rules
- Foundation or contract tickets should land before downstream slices that rely on them: Pembaruan `AGENTS.md` (mendefinisikan peran Orkestrator vs Pekerja) harus diselesaikan pertama kali sebagai pedoman dasar.
- QA or verification tickets should depend on the relevant feature slices being complete.
- Shared behavior or schema decisions that affect multiple slices should be captured explicitly as blockers.

## Ticket conventions
- `Status`: `todo`, `in-progress`, `blocked`, `done`
- `Type`: `AFK`, `HITL`
- `Depends on`: `none` or explicit ticket IDs
- `Blocks`: explicit ticket IDs or blank when none
- `Parallelizable`: `yes`, `no`
- QA follow-up: reopen an existing ticket if the original scope still fits; otherwise add a new ticket

## Parallelization plan
Can start immediately:
- ISSUE-001 (Perbarui AGENTS.md)

Blocked until prerequisites complete:
- ISSUE-002, ISSUE-003, ISSUE-004 menunggu ISSUE-001 selesai, namun ketiganya bisa dikerjakan secara paralel (Lane A, B, C) karena masing-masing menyentuh berkas *skill* yang berbeda.

Suggested lanes:
- Lane A: Memperbarui `research-me/SKILL.md` (ISSUE-002)
- Lane B: Memperbarui `prototype-me/SKILL.md` (ISSUE-003)
- Lane C: Memperbarui `execute-me/SKILL.md` (ISSUE-004)

## Tickets

### ISSUE-001 — Update AGENTS.md with Orchestrator vs Worker constraints
- Status: done
- Type: AFK
- Goal: Menetapkan aturan invariant dasar mengenai penggunaan subagent di dalam repositori 7-phase.
- Why it exists: Untuk mencegah subagent (sebagai pekerja lepas) mengedit berkas *state* (`docs/`) atau melewati *readiness gates*, serta menetapkan Primary Agent sebagai *Orchestrator*.
- Depends on: none
- Blocks: ISSUE-002, ISSUE-003, ISSUE-004
- Parallelizable: no
- Source requirements:
  - Functional Requirement #1 & #4
- Scope:
  - Mengubah `.pi/AGENTS.md` atau `AGENTS.md` (menyesuaikan letaknya).
  - Menambahkan bagian `## Subagent Delegation (Orchestrator vs Worker)`.
- Execution brief (optional):
  - Fokus validasi: Jangan membuang aturan invariant yang sudah ada, cukup tambahkan bagian subagent di akhir atau di dalam bagian workflow invariants. Tegaskan larangan subagent memodifikasi `docs/`.
- Acceptance criteria:
  - [ ] `AGENTS.md` memuat aturan eksplisit bahwa Primary Agent adalah *Orchestrator*.
  - [ ] `AGENTS.md` memuat larangan eksplisit bagi subagent untuk merubah `docs/*` secara sepihak.
  - [ ] `AGENTS.md` menegaskan aturan "1 tiket per run" tetap berlaku untuk eksekusi subagent.
- Notes / risks:
  - Risiko: Menghapus aturan invariant 7-fase yang lama. (Gunakan tools `edit` atau `write` yang berhati-hati).

### ISSUE-002 — Update research-me skill for subagent delegation
- Status: done
- Type: AFK
- Goal: Memungkinkan `research-me` untuk mendelegasikan tugas pencarian web atau eksplorasi repo secara paralel ke subagent tipe `explore`.
- Why it exists: Mempercepat proses riset ketika konteksnya sangat besar atau memerlukan *web scraping* multi-halaman yang berjalan bersamaan.
- Depends on: ISSUE-001
- Blocks: 
- Parallelizable: yes
- Source requirements:
  - Functional Requirement #2
- Scope:
  - Memperbarui `.pi/skills/research-me/SKILL.md`.
- Acceptance criteria:
  - [ ] Ada bagian `### Optional: Subagent Delegation` atau penyesuaian instruksi kerja pada alur `research-me`.
  - [ ] Memberikan pedoman pemanggilan alat (tool call) untuk memanggil `subagent_type: explore` secara asinkron (background).
- Notes / risks:
  - Instruksi delegasi harus jelas agar agen utama tahu kapan harus menengok hasil (`get_subagent_result`).

### ISSUE-003 — Update prototype-me skill for subagent delegation
- Status: done
- Type: AFK
- Goal: Memungkinkan `prototype-me` untuk menggunakan subagent (explore/execute terbatas) guna membuat dan membandingkan prototipe.
- Why it exists: Proses prototyping sering melibatkan banyak file atau variasi UX yang bisa dilakukan lebih efisien melalui pekerja belakang layar.
- Depends on: ISSUE-001
- Blocks: 
- Parallelizable: yes
- Source requirements:
  - Functional Requirement #2
- Scope:
  - Memperbarui `.pi/skills/prototype-me/SKILL.md`.
- Acceptance criteria:
  - [ ] Ada bagian instruksi terkait pendelegasian subagent.
  - [ ] Instruksi memperingatkan agar subagent hanya bekerja di `docs/prototype/` (jika subagent membuat file) atau hasil risetnya dikembalikan sebagai teks ke Primary Agent.
- Notes / risks:

### ISSUE-004 — Update execute-me skill with isolated prompt assembly for subagents
- Status: done
- Type: AFK
- Goal: Menerapkan pendelegasian penulisan kode ke subagent `execute` tanpa melanggar prinsip 1 tiket per eksekusi.
- Why it exists: Memisahkan beban kognitif membaca tiket (Orchestrator) dan menulis kode/TDD (Worker).
- Depends on: ISSUE-001
- Blocks: 
- Parallelizable: yes
- Source requirements:
  - Functional Requirement #3
- Scope:
  - Memperbarui `.pi/skills/execute-me/SKILL.md`.
- Execution brief (optional):
  - Sangat penting: Tekankan aturan `inherit_context: false`. Primary Agent harus *menyusun ulang* (merangkum) tujuan tiket, *acceptance criteria*, dan instruksi "Test-first" ke dalam *prompt* steril sebelum memanggil subagent.
- Acceptance criteria:
  - [ ] Instruksi `execute-me` mewajibkan perakitan konteks (*isolated prompt assembly*).
  - [ ] Larangan eksplisit menggunakan `inherit_context: true`.
  - [ ] Primary Agent diinstruksikan untuk memverifikasi hasil sebelum menandai tiket `done` di `docs/issues.md`.
- Notes / risks:
  - Jika instruksi kurang jelas, Primary Agent bisa memanggil subagent dengan prompt kosong atau malah *hallucinate* dan mengerjakan tiket sendiri tanpa mendelegasikan.

