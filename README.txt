# Türkçe Şarkı Sözü Üretimi – YZM308 Final Projesi

Bu proje, **Türkçe şarkı sözleri** üretmek için eğitimli bir LSTM tabanlı dil modeli içerir. Çalışma, Yıldız Teknik Üniversitesi **YZM308 – Üretken Yapay Zeka** dersi kapsamında final projesi olarak hazırlanmıştır.

---

## İçindekiler
1. [Proje Hakkında](#proje-hakkında)
2. [Özellikler](#özellikler)
3. [Gereksinimler](#gereksinimler)
4. [Kurulum](#kurulum)
5. [Veri Kümesi](#veri-kümesi)
6. [Dosya Yapısı](#dosya-yapısı)
7. [Eğitim](#eğitim)
8. [Kullanım](#kullanım)
9. [Örnek Çıktı](#örnek-çıktı)
10. [Hiperparametreler](#hiperparametreler)
11. [Katkıda Bulunanlar](#katkıda-bulunanlar)
12. [Lisans](#lisans)

---

## Proje Hakkında
Bu repo, SentencePiece ile **Byte‑Pair Encoding (BPE)** tabanlı bir alt sözcük sözlüğü oluşturup, ardından TensorFlow ile tek katmanlı bir **LSTM** modelini Türkçe şarkı sözleri üzerinde eğitir. Model, başlangıç tohum cümlesinden (seed) itibaren anlamlı yeni sözler üretir.

---

## Özellikler
- 📜 **Türkçe** şarkı sözleri için optimize edilmiş tokenizer
- 🧠 **Tek katmanlı LSTM** mimarisi
- 🛑 **Early Stopping** desteği
- 🎲 **Nucleus (Top‑p) örnekleme** ile esnek metin üretimi
- 📊 Eğitim sürecini görselleştiren kayıp (loss) grafiği

---

## Gereksinimler
```bash
python >= 3.9
TensorFlow >= 2.15
sentencepiece >= 0.1.99
pandas >= 2.2
numpy >= 1.26
matplotlib >= 3.9
seaborn >= 0.13
openpyxl >= 3.1
```
> **Donanım:** Eğitim, orta‑büyük boyutlu veri kümeleri için en az 8 GB VRAM’e sahip bir NVIDIA GPU (ör. A100) ile ~20 dakika sürer. CPU’da eğitim mümkündür ancak çok daha uzun zaman alır.

---

## Kurulum
1. Depoyu klonlayın veya `v1.py` dosyasını indirin.
2. Sanal ortam oluşturun (önerilir):
   ```bash
   python -m venv venv
   source venv/bin/activate  # Windows: venv\Scripts\activate
   ```
3. Bağımlılıkları yükleyin:
   ```bash
   pip install --quiet sentencepiece tensorflow pandas openpyxl matplotlib seaborn
   ```

---

## Veri Kümesi
- `turkish.xlsx` dosyası, `lyrics` sütununda şarkı sözlerini içerir.
- Veri, `corpus.txt` dosyasına dönüştürülerek tokenizer eğitiminde kullanılır.
- Örnek sütunlar:
  | sarkici | şarkı_adi | lyrics |
  |---------|-----------|--------|
  | Sezen Aksu | Gülümse | *…*

> **Not:** Telif haklarına saygı için veri kümesini bu repoya dâhil etmedik. Kendi CSV/XLSX dosyanızı `EXCEL_FILE` yoluna koyun.

---

## Dosya Yapısı
```
.
├── v1.py              # Ana kaynak kodu
├── corpus.txt         # (Oluşur) Tokenizer eğitimi için tüm sözler
├── tr_lyric.model     # (Oluşur) SentencePiece model dosyası
├── checkpoints/       # (İsteğe bağlı) Model ağırlık yedekleri
└── README.md          # Bu dosya
```

---

## Eğitim
`v1.py` betiği doğrudan çalıştırıldığında aşağıdaki adımlar gerçekleşir:
1. **Veri Yükleme:** Excel → `pandas.DataFrame`
2. **Önişleme:** Tüm sözler `corpus.txt`
3. **Tokenizer Eğitimi:** `VOCAB_SIZE=16000`
4. **Dizi Oluşturma:** Giriş/çıktı çiftleri (SEQ_LEN=256)
5. **Model Kurulumu:** Embedding → LSTM → Dense (softmax)
6. **Eğitim:** `BATCH_SIZE=128`, `EPOCHS=10`
7. **Kayıt:** Kayıp grafiği

Eğitimi başlatmak için:
```bash
python v1.py
```
> Gelişmiş ayarlar için `v1.py` içindeki **Hiperparametreler** bölümünü düzenleyin.

---

## Kullanım
Eğitim tamamlandıktan sonra model, tohum cümle kullanılarak yeni sözler üretir:
```python
from v1 import generate
print(generate("Geceler kara tren", max_tokens=120, temperature=0.5, top_p=0.9))
```
Parametreler:
- `seed_text` *(str)* – Başlangıç ifadesi
- `max_tokens` *(int)* – Üretilecek maksimum token sayısı
- `temperature` *(float)* – Çıktı çeşitliliği (0–1 arası; düşük → tutarlı)
- `top_p` *(float)* – Nucleus örnekleme eşiği (0–1 arası)

---

## Örnek Çıktı
```text
---- ÖRNEK ÇIKIŞ ----
Geceler kara tren gibi sessiz ve zamansız
Bir biletin var mı kalbime yalnız
…
```

---

## Hiperparametreler
| Değişken | Açıklama | Varsayılan |
|-----------|----------|-----------|
| `VOCAB_SIZE` | Tokenizer sözlük boyutu | 16 000 |
| `SEQ_LEN` | Model giriş uzunluğu | 256 |
| `EMBED_DIM` | Gömme katmanı boyutu | 128 |
| `LSTM_UNITS` | LSTM gizli birim sayısı | 1 024 |
| `BATCH_SIZE` | Mini‑batch | 128 |
| `EPOCHS` | Eğitim döngü sayısı | 10 |
| `learning_rate` | Adam öğrenme oranı | 0.001 |

---

## Katkıda Bulunanlar
- **Hasan Atakan Öztürk** – [E‑posta](ozturkhasanfatih@gmail.com)

Katkıya açıksınız! Lütfen `pull request` veya `issue` açın.

---

## Lisans
Bu proje, MIT Lisansı ile lisanslanmıştır. Ayrıntılar için `LICENSE` dosyasına bakın.
