# PyTorch ile LSTM ve GRU Kullanarak Hisse Senedi Tahmini

Bu proje, Amazon (AMZN) şirketinin geçmiş 10 yıllık hisse senedi verilerini kullanarak gelecek dönem kapanış fiyatlarını tahmin etmek amacıyla derin öğrenme yaklaşımlarını (LSTM ve GRU) karşılaştırır.

## 📁 Proje Yapısı
- `Untitled.ipynb`: Veri çekme, kayan pencere (sliding window) dönüşümü, model mimarileri ve eğitim döngülerlerinin yer aldığı Jupyter Notebook.
- `README.md`: Proje özeti ve sonuç raporu.

## 📊 Sonuçlar ve Karşılaştırma
Aynı hiperparametreler (32 hidden birim, 2 katman, 50 Epoch, 0.01 Öğrenme Oranı) ile yapılan test sonuçları:

| Model | Test RMSE (Hata Payı) |
|---|---|
| **GRU** | 4.97 USD |
| **LSTM** | 11.24 USD |

### Kritik Değerlendirme
Model çıktıları incelendiğinde, GRU mimarisinin zaman serisindeki ani dalgalanmalara ve yükseliş trendlerine LSTM'e kıyasla daha hızlı ve doğru uyum sağladığı gözlemlenmiştir. Stok fiyat tahmini gibi finansal verilerin yüksek varyans içermesi sebebiyle, modellerin geçmiş trendleri ezberlemeden (overfitting olmadan) genelleme yapabilmesi adına GRU bu senaryoda daha başarılı bir performans ortaya koymuştur.