# PyTorch ile LSTM ve GRU Kullanarak Hisse Senedi Tahmini

Bu proje, **Amazon (AMZN)** hisse senedinin geçmiş 10 yıllık kapanış fiyatlarını kullanarak
gelecek dönem kapanış fiyatlarını tahmin etmek için iki derin öğrenme yaklaşımını (**LSTM** ve
**GRU**) karşılaştırır. Problem, tek değişkenli bir **zaman serisi regresyon** problemidir.

## Proje Yapısı

```
stock-prediction-pytorch/
├── stock_prediction_lstm_gru.ipynb   # Ana notebook (veri, model, eğitim, değerlendirme)
├── requirements.txt                  # Python bağımlılıkları
├── README.md                         # Bu dosya
└── .gitignore
```

- `stock_prediction_lstm_gru.ipynb`: Veri çekme, kayan pencere (sliding window) dönüşümü,
  LSTM/GRU model mimarileri, eğitim döngüleri ve değerlendirmenin yer aldığı, Markdown
  açıklamalarıyla bölümlere ayrılmış Jupyter Notebook.

## Veri Kaynağı

Veri, `yfinance` kütüphanesi aracılığıyla doğrudan Yahoo Finance'ten çekilir
(AMZN, 2015-01-01 – 2025-01-01, ~2516 işlem günü). Yalnızca `Close` (kapanış) fiyatı
kullanılır. Veri canlı indirildiği için repoda ayrı bir CSV dosyası tutulmaz; notebook her
çalıştırıldığında güncel veriyi indirir.

## Kurulum ve Çalıştırma

```bash
# 1. Sanal ortam oluşturun (önerilir)
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# 2. Bağımlılıkları kurun
pip install -r requirements.txt

# 3. Notebook'u açın ve baştan sona çalıştırın
jupyter notebook stock_prediction_lstm_gru.ipynb
```

Notebook'taki hücreleri sırayla çalıştırmak, veri indirmeden model karşılaştırmasına ve
grafiklere kadar tüm adımları yeniden üretir.

## Yöntem Özeti

- **Normalizasyon:** `MinMaxScaler` ile fiyatlar `[-1, 1]` aralığına ölçeklenir.
- **Kayan pencere:** 20 günlük diziler, 21. günün fiyatını tahmin edecek girdi/hedef çiftlerine
  dönüştürülür (`lookback = 20`).
- **Ayrım:** Zaman serisi karıştırılmadan ilk %80 eğitim, son %20 test olarak ayrılır.
- **Modeller:** İki katmanlı, 32 gizli birimli LSTM ve GRU; her ikisi de aynı hiperparametrelerle.
- **Eğitim:** MSE kaybı + Adam optimizer, 50 epoch, öğrenme oranı 0.01.
- **Değerlendirme:** Tahminler gerçek USD ölçeğine geri döndürülüp **RMSE** hesaplanır.

## Sonuçlar ve Karşılaştırma

Aynı hiperparametreler (32 gizli birim, 2 katman, 50 epoch, 0.01 öğrenme oranı) ile:

| Model | Test RMSE (Hata Payı) | Eğitim Süresi |
|---|---|---|
| **GRU**  | **4.97 USD** | 3.23 sn |
| **LSTM** | 11.24 USD | 3.16 sn |

Bu deneyde GRU, LSTM'e kıyasla belirgin şekilde daha düşük test hatası verdi. GRU'nun daha az
parametreye sahip olması, tek özellikli ve görece küçük bir veri setinde daha kararlı bir
öğrenmeye yardımcı olmuş görünüyor. Eğitim süreleri bu boyutta neredeyse eşittir.

## Kritik Değerlendirme ve Sınırlamalar

Düşük RMSE, modelin "piyasayı yendiği" anlamına gelmez. Model yalnızca son 20 günün fiyatına
bakarak bir sonraki günü tahmin etmektedir ve bu tür modeller çoğu zaman bir önceki günün
fiyatını tekrar etme eğiliminde olur (naive baseline'a yakınsama). Gerçek dünyada hisse
fiyatları büyük ölçüde rastgele yürüyüşe (random walk) benzer; tek değişkenli geçmiş fiyat,
geleceği güvenilir biçimde öngörmek için genellikle yetersizdir. Narayanan & Kapoor'un
*AI Snake Oil* çalışmasında vurgulandığı gibi, tahmine dayalı modellerin başarısı kolayca
abartılabilir; metrikler şüpheci bir gözle yorumlanmalıdır.

## Olası Sonraki Adımlar

- Naive baseline (dünün fiyatı = bugünün tahmini) ile karşılaştırıp modelin gerçek katkısını ölçmek
- Ek özellikler eklemek (işlem hacmi, hareketli ortalamalar, teknik göstergeler)
- Dropout / erken durdurma ile overfitting kontrolü
- Transformer tabanlı zaman serisi modellerini denemek
