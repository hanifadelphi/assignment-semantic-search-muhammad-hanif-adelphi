# E-Commerce FAQ Semantic Search & LLM Assistant

Prototype aplikasi **Semantic Search** dan **Retrieval-Augmented Generation (RAG) sederhana** berbasis FAQ E-Commerce. Sistem ini mengubah data FAQ menjadi embedding, menyimpannya di FAISS vector database, melakukan pencarian kontekstual, dan menyusun jawaban natural menggunakan Gemini AI API.

## Problem Statement & Objective
- **Problem**: Tim CS e-commerce menerima ratusan pertanyaan repetitif seputar pengiriman, pengembalian, dan pembayaran setiap harinya.
- **Objective**: Membangun prototype asisten AI yang dapat mencari entri FAQ paling relevan (meski kata-katanya tidak persis sama/paraphrase) dan memberikan jawaban yang akurat berdasarkan knowledge base.
- **Constraint**: Sistem hanya boleh menjawab pertanyaan berdasarkan konteks FAQ yang tersedia untuk mencegah halusinasi.

## Dataset & Knowledge Base
- **Source**: `data/ecommerce_faq_knowledge_base.csv`
- **Unit Retrievable**: Penggabungan kolom `question` dan `answer` menjadi satu kesatuan teks utuh agar menjaga keutuhan konteks makna saat di-embed.

## Alur Pipeline Sistem
1. **Ingest**: Memuat dataset FAQ (.csv) dan menyusun unit teks retrievable.
2. **Embed**: Mengubah unit teks FAQ menjadi vektor embedding menggunakan model `models/gemini-embedding-001`.
3. **Store**: Menyimpan seluruh vektor embedding ke dalam vector database lokal (`FAISS`).
4. **Retrieve**: Menerima query user, mengubahnya jadi embedding, lalu mencari Top-K FAQ terdekat menggunakan metode similarity search.
5. **Generate**: Menyisipkan hasil retrieval sebagai `Context` ke dalam terstruktur prompt, kemudian mengirimkannya ke `gemini-2.5-flash-lite` untuk memproduksi jawaban akhir.

## Hasil Pengujian & Analisis Relevansi
Pengujian dilakukan menggunakan 5 query bervariasi:
1. **Direct Match (2 Query)**: 
   - *Query*: "Bagaimana cara melakukan pengembalian barang?" & "Apakah ada garansi untuk produk elektronik?"
   - *Hasil*: Retrieval menghasilkan distance score rendah (< 0.4) dan LLM merespons dengan akurat sesuai isi FAQ.
2. **Paraphrase / Bahasa Berbeda (2 Query)**:
   - *Query*: "Paketku kok belum nyampe-nyampe ya?" & "Bisa bayar pas barang sampe ga sih?"
   - *Hasil*: Semantic search berhasil menghubungkan sinonim/bahasa gaul ke topik FAQ yang relevan (Estimasi Pengiriman & COD/Pembayaran) meskipun kata-katanya tidak persis sama.
3. **Out-of-Domain / Tidak Ada di FAQ (1 Query)**:
   - *Query*: "Apakah toko ini menjual tiket konser Coldplay?"
   - *Hasil*: Distance score relatif tinggi, dan LLM dengan tepat menolak menjawab sesuai instruksi constraint prompt (*"Mohon maaf, saya belum memiliki informasi tersebut..."*).

## Keterbatasan Sistem (Limitations)
- Tidak ada mekanisme *chunking* dokumen panjang (karena fokus pada FAQ berformat pendek).
- Pencarian masih murni vector similarity (belum menerapkan *Hybrid Search* / *Re-ranking*).
- Indeks FAISS disimpan secara in-memory/lokal.

## Cara Menjalankan Project
1. Clone repository ini:
   ```bash
   git clone https://github.com/hanifadelphi/assignment-semantic-search-muhammad-hanif-adelphi
