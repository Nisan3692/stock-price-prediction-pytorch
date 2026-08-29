# Hisse Senedi Fiyat Tahmini: LSTM vs GRU (PyTorch)

Bu proje, Amazon (AMZN) hissesinin geçmiş kapanış fiyatlarını kullanarak bir sonraki günün
fiyatını tahmin etmeye çalışan iki RNN modelini (LSTM ve GRU) aynı koşullarda karşılaştırıyor.
Problem tek değişkenli bir zaman serisi regresyonu: elimizde sadece geçmiş fiyatlar var.

Başlarken amacım borsada para kazanmak değildi; daha çok PyTorch ile RNN'leri uçtan uca
kullanmayı öğrenmek ve LSTM ile GRU'nun pratikte nasıl ayrıştığını görmekti. Bu yüzden
sonuçları yorumlarken de temkinli davrandım (aşağıda "Sonuçlar" kısmına bakabilirsiniz).

## Repoda neler var

- `stock_prediction_lstm_gru.ipynb` — tüm çalışma burada: veriyi çekme, kısa bir keşif,
  ön işleme (normalize + kayan pencere), LSTM ve GRU modelleri, eğitim, değerlendirme ve
  bir naive baseline karşılaştırması. Hücreler arasına ne yaptığımı anlatan notlar ekledim.
- `requirements.txt` — gerekli kütüphaneler.
- `.gitignore`

## Veri

Veriyi `yfinance` ile doğrudan Yahoo Finance'ten çekiyorum (AMZN, 2015-2025, günlük;
yaklaşık 2500 işlem günü). Sadece kapanış fiyatını kullanıyorum. Veri canlı indirildiği
için repoda ayrı bir CSV tutmuyorum — notebook her çalıştığında güncel veriyi çeker.

## Nasıl çalıştırılır

```bash
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook stock_prediction_lstm_gru.ipynb
```

Notebook'u baştan sona (Kernel > Restart & Run All) çalıştırdığınızda veri çekme,
eğitim, grafikler ve baseline karşılaştırması dahil her şey baştan üretilir.

## Kısaca yöntem

Fiyatları `[-1, 1]` aralığına normalize ettim, sonra kayan pencere yöntemiyle her örneği
son 20 günün fiyatından oluşan bir diziye çevirdim (hedef = 21. gün). Zaman serisi olduğu
için veriyi karıştırmadan ilk %80 eğitim / son %20 test olarak ayırdım. LSTM ve GRU'yu
aynı hiperparametrelerle (32 gizli birim, 2 katman, 50 epoch, Adam, lr=0.01) eğitip
test setinde RMSE ile karşılaştırdım.

## Sonuçlar

Sonuç tablosu ve kısa değerlendirme notebook'un içinde, doğrudan hesaplanan değerlerden
üretiliyor; bu yüzden burada sabit sayılar vermiyorum (model ağırlıkları rastgele başladığı
için her çalıştırmada değerler biraz oynayabiliyor). Genel gözlemim: GRU çoğunlukla LSTM'e
yakın ya da ondan biraz daha iyi bir RMSE veriyor, eğitim süreleri ise neredeyse aynı.

## Ama dikkat: düşük RMSE yanıltıcı olabilir

Notebook'ta bir de naive baseline testi var: hiçbir model kullanmadan "dünkü fiyatı yarına
taşıyarak" bir tahmin üretip onun RMSE'sini hesaplıyorum. Hisse serilerinde bu basit yöntem
bile güçlü sonuç verir, çünkü fiyatlar günden güne çok az değişir. Kendi çalıştırmalarımda
modellerin bu naive çizgiyi geçmekte zorlandığını gördüm — yani öğrenilen şey büyük ölçüde
"önceki günü tekrar etmek" oluyor. Düşük RMSE tek başına modelin piyasayı anladığı anlamına
gelmiyor.

Genel olarak hisse fiyatları rastgele yürüyüşe yakın davranır ve sadece geçmiş fiyata bakarak
geleceği güvenilir tahmin etmek zordur. Bu yüzden metriklere biraz şüpheyle yaklaşmakta fayda
var (bu bakış açısı için Narayanan & Kapoor'un *AI Snake Oil* çalışması güzel bir kaynak).

## Sonraki adımlar

- Fiyat dışında özellikler eklemek (işlem hacmi, hareketli ortalamalar).
- Overfitting'i sınırlamak için dropout / erken durdurma.
- Hedefi değiştirmek: "yarın artacak mı azalacak mı" gibi bir yön tahmini, salt fiyattan
  daha anlamlı olabilir.
- Merak edilirse Transformer tabanlı zaman serisi modelleri.
