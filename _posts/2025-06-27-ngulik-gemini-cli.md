---
layout: post
title: "Belajar Gemini CLI dan Komparasi AI Command Line Tools"
date: 2025-06-27
categories: [ai, cli]
tags: [gemini, cli, ai-tools]
---

Hari ini saya mencoba **Gemini CLI**, yaitu interface command-line untuk mengakses model AI Gemini (Google) secara langsung dari terminal. Ini cocok untuk developer atau penulis skrip yang ingin interaksi cepat tanpa harus membuka browser atau UI visual.

### 🔍 Fitur Gemini CLI:
- Dukungan prompt dan context langsung via terminal
- Bisa menyimpan history query
- Integrasi sederhana dengan shell script
- Performa cepat untuk prototyping

### 🤝 Dibandingkan dengan AI CLI lain:
| Tool             | Provider  | Kelebihan                                  | Kekurangan                          |
|------------------|-----------|--------------------------------------------|-------------------------------------|
| **Gemini CLI**   | Google    | Cepat, native untuk Gemini, support context | Belum banyak plugin & integrasi     |
| **OpenAI CLI**   | OpenAI    | Stabil, dokumentasi kuat, banyak integrasi  | Setup awal perlu API key            |
| **Ollama**       | Lokal     | Bisa offline, pakai model lokal (llama)     | Butuh resource besar                |
| **llm (by Simon)**| Open     | Support banyak provider, plugin system      | Setup kompleks, tidak cocok pemula  |

---

### ✍️ Catatan Pribadi:
Saya suka Gemini CLI karena ringan dan fokus. Tapi untuk eksplorasi plugin atau integrasi kompleks, saya tetap pilih OpenAI CLI.

---

### 📚 Next:
- Coba chaining antara Gemini CLI dan bash script
- Bandingkan juga output dari prompt yang sama
- Pelajari cara integrasi Gemini CLI ke dalam workflow TIL

