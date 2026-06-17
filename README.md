# 🎙️ WhisperAudioDrive — Multi-Speaker Audio Transcription

Notebook Google Colab untuk transkripsi audio/video secara otomatis dengan deteksi pembicara (*speaker diarization*). File input dibaca dari Google Drive, hasil transkripsi langsung disimpan kembali ke Drive.

---

## ✨ Fitur

- **Transkripsi akurat** menggunakan Whisper `large-v3` — dioptimalkan untuk Bahasa Indonesia
- **Deteksi pembicara** — mengenali dan membedakan siapa yang berbicara (`[Speaker 0]`, `[Speaker 1]`, dst.)
- **Batch processing** — proses banyak file sekaligus dari satu folder
- **5 format export**: `.txt`, `.md`, `.srt`, `.vtt`, `.json`
- **Smart merge** — menggabungkan kalimat berurutan dari speaker yang sama agar lebih mudah dibaca

---

## 📋 Prasyarat

### 1. Google Colab dengan GPU
Aktifkan GPU sebelum menjalankan notebook:
```
Runtime → Change runtime type → T4 GPU  (atau L4 / A100)
```

### 2. HuggingFace Token
Model diarization `pyannote` memerlukan autentikasi.

1. Buat token di [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens)
2. Setujui syarat penggunaan model berikut:
   - [pyannote/speaker-diarization-3.1](https://huggingface.co/pyannote/speaker-diarization-3.1)
   - [pyannote/segmentation-3.0](https://huggingface.co/pyannote/segmentation-3.0)
3. Simpan token di **Colab Secrets** (ikon 🔑 di sidebar) dengan nama `HF_TOKEN`

### 3. Struktur Folder Google Drive
```
MyDrive/
└── Whisper/
    ├── AudioFiles/              ← taruh file audio/video di sini
    └── Transcripts_MultiSpeaker/  ← hasil transkripsi otomatis tersimpan di sini
```

> Folder akan dibuat otomatis jika belum ada.

---

## 🚀 Cara Pakai

| # | Cell | Yang Dilakukan | Catatan |
|---|------|----------------|---------|
| 1 | **Install Dependencies** | Install ffmpeg, WhisperX, pyannote | Jalankan sekali, lalu **Restart Session** |
| 2 | **Mount Google Drive** | Hubungkan Drive ke Colab | — |
| 3 | **Konfigurasi** | Atur folder, model, dan parameter | Sesuaikan sebelum lanjut |
| 4 | **HuggingFace Token** | Login untuk akses model diarization | Butuh token & persetujuan model |
| 5 | **Load Models** | Muat model ASR, alignment, diarization | Bisa makan beberapa menit |
| 6 | **Helper Functions** | Definisikan fungsi bantu | Tidak perlu diubah |
| 7 | **Cek File Audio** | Tampilkan daftar file yang akan diproses | Pastikan file sudah di-upload |
| 8 | **Transkripsi & Export** | Proses utama | Jalankan setelah semua cell di atas |
| 9 | **Ringkasan Output** | Tampilkan lokasi semua file hasil | — |

---

## ⚙️ Konfigurasi (Cell 3)

| Parameter | Default | Keterangan |
|-----------|---------|------------|
| `AUDIO_FOLDER` | `.../AudioFiles` | Folder input audio/video |
| `OUTPUT_FOLDER` | `.../Transcripts_MultiSpeaker` | Folder output transkripsi |
| `MODEL_NAME` | `large-v3` | Ukuran model Whisper (`tiny`, `base`, `small`, `medium`, `large-v2`, `large-v3`) |
| `LANGUAGE` | `id` | Kode bahasa ISO 639-1 (`id` = Indonesia, `en` = Inggris) |
| `BATCH_SIZE` | `4` | Turunkan ke `2` atau `1` jika VRAM habis |
| `MIN_SPEAKERS` | `None` | Jumlah minimum pembicara (otomatis jika `None`) |
| `MAX_SPEAKERS` | `None` | Jumlah maksimum pembicara (otomatis jika `None`) |
| `MERGE_SAME_SPEAKER` | `True` | Gabungkan kalimat berurutan dari speaker yang sama |
| `SKIP_EXISTING` | `False` | Lewati file yang sudah punya output |

---

## 📄 Contoh Output

**Format `.txt`:**
```
00:00:00,100 --> 00:00:33,940 [Speaker 0]
Selamat pagi dan selamat datang di acara hari ini.

00:00:33,940 --> 00:03:27,580 [Speaker 1]
Terima kasih. Saya senang bisa hadir untuk membahas topik ini.

00:03:27,800 --> 00:03:34,500 [Speaker 2]
Pertanyaan pertama dari saya adalah...
```

**Format lain** yang tersedia untuk setiap file:
- `.md` — Markdown dengan heading per segment
- `.srt` — Subtitle standar (cocok untuk video editor)
- `.vtt` — WebVTT subtitle (cocok untuk web player)
- `.json` — Data lengkap termasuk raw output Whisper

---

## 🎵 Format Audio yang Didukung

| Audio | Video |
|-------|-------|
| `.mp3`, `.wav`, `.m4a`, `.aac` | `.mp4`, `.mov`, `.mkv`, `.webm` |
| `.flac`, `.ogg`, `.opus`, `.wma` | — |

---

## 🔧 Troubleshooting

| Masalah | Solusi |
|---------|--------|
| **CUDA out of memory** | Turunkan `BATCH_SIZE` di Cell 3 (coba `2` atau `1`) |
| **Speaker terlalu banyak/sedikit** | Set `MIN_SPEAKERS` dan `MAX_SPEAKERS` di Cell 3 |
| **Diarization gagal load** | Pastikan sudah accept terms model pyannote di HuggingFace |
| **Nama speaker bukan nama orang** | Normal — diarization mengenali suara, bukan identitas |
| **Akurasi transkripsi rendah** | Periksa `LANGUAGE`, atau coba model yang lebih besar |
| **File tidak terdeteksi** | Pastikan ekstensi file termasuk dalam `SUPPORTED_EXTENSIONS` |

---

## 🧰 Tech Stack

| Komponen | Library |
|----------|---------|
| Speech-to-Text | [WhisperX](https://github.com/m-bain/whisperX) + OpenAI Whisper `large-v3` |
| Speaker Diarization | [pyannote.audio](https://github.com/pyannote/pyannote-audio) 3.1 |
| Word Alignment | WhisperX built-in alignment |
| Runtime | Google Colab + NVIDIA T4/L4/A100 GPU |
