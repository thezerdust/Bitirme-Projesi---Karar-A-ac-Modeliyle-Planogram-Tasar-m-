#  Karar Ağacı Modelleriyle Planogram Tasarımı

Perakende mağazalarının raf düzenlemesini (planogram) optimize etmek amacıyla makine öğrenmesi tabanlı bir segmentasyon ve sınıflandırma sistemi.

---

##  Proje Özeti

Bu proje, farklı perakende mağazalarını demografik, coğrafi ve ticari verilere dayanarak segmentlere ayırmayı ve her segmente özel planogram önerileri üretmeyi hedeflemektedir. Mağazalar K-Means kümeleme algoritmasıyla **Bakır, Bronz, Gümüş ve Altın** olmak üzere dört stratejik segmente ayrılmış; ardından XGBoost, Random Forest ve Gradient Boost modelleriyle sınıflandırılarak her segment için raf yerleşim (facing) önerileri oluşturulmuştur.

---

##  Proje Akışı

```
Ham Veri → Veri Ön İşleme → SMOTE Dengesi → K-Means Kümeleme
    → Segment Profilleme → Model Eğitimi (RF / XGB / GB)
        → Feature Importance & SHAP → Planogram Üretimi
```

1. **Veri Ön İşleme** — Rakip marka sütunları gizlilik kapsamında maskelendi (`Rakip_XX`, `OncelikliRakip_XX`). Eksik değerler medyan ile dolduruldu.
2. **Kümeleme** — Optimal küme sayısı Elbow Grafiği ile belirlendi. K-Means algoritması hem orijinal veri hem de SMOTE dengeli veri üzerinde k=4 ve k=5 için uygulandı.
3. **Sınıf Dengesi (SMOTE)** — Dengesiz sınıf dağılımı SMOTE (Synthetic Minority Over-sampling Technique) yöntemiyle giderildi.
4. **Model Eğitimi** — Random Forest, XGBoost ve Gradient Boost modelleri 80/20 train-test bölümüyle eğitildi ve karşılaştırıldı.
5. **Açıklanabilir Yapay Zeka (XAI)** — SHAP değerleriyle değişkenlerin model çıktısına yön ve büyüklük bazında katkısı analiz edildi.
6. **Planogram Üretimi** — Her segment × model kombinasyonu için 4 raf × 20 facing = 80 kapasiteli, kısıt denetimli planogramlar görsel olarak oluşturuldu.

---

##  Kullanılan Teknolojiler

| Kategori | Kütüphaneler |
|---|---|
| Veri İşleme | `pandas`, `numpy` |
| Makine Öğrenmesi | `scikit-learn`, `xgboost`, `imbalanced-learn` |
| Açıklanabilirlik | `shap` |
| Görselleştirme | `matplotlib`, `seaborn` |
| Raporlama | `openpyxl` |

**Python sürümü:** 3.8+

---

##  Dosya Yapısı

```
├── Karar_Ağacı_Modelleriyle_Planogram_Tasarımı.ipynb   # Ana notebook
├── Bitirme Analiz Dosyası.xlsx                          # Giriş veri seti (mağaza verileri)
├── Around Store Data_Yeni_Anonim.xlsx                   # Çevre & rakip verileri (anonim)
├── Cluster Kodları.xlsx                                 # SPSS küme merkezi çıktısı
│
├── smote_segmented_data.xlsx                            # SMOTE sonrası etiketli veri
├── smote_magaza_kodlu_veri.xlsx                         # SPSS uyumlu çıktı
├── segment_profiles.xlsx                                # K4/K5 segment profilleri
├── Model_Karsilastirma_Maskeli_Planogram_Raporu.xlsx    # Model & planogram raporu
│
├── elbow_grafigi_cikti.png                              # Elbow grafiği
├── pca_segmentation_plots.png                           # Orijinal veri PCA kümeleme
├── magaza_segmentasyonu.png                             # SMOTE sonrası PCA
├── rf_feature_importance_visual.png                     # Feature importance karşılaştırması
├── shap_global_feature_importance_bar_plot.png          # SHAP bar grafiği
├── shap_beeswarm_plot.png                               # SHAP beeswarm grafiği
├── confusion_matrix_karsilastirma.png                   # SMOTE öncesi/sonrası CM
│
└── cık_10/                                              # Görsel planogram dosyaları
    ├── Altin_XGBoost_Planogrami.png
    ├── Altin_Random_Forest_Planogrami.png
    └── ... (12 kombinasyon)
```

---

##  Model Performansı

| Model | Test Accuracy | Öne Çıkan Özellik |
|---|---|---|
| **Random Forest** | %94.83 | En yüksek doğruluk, stabil çapraz doğrulama |
| XGBoost | %93.10 | En düşük Log Loss (0.159), sınır mağazalarda güvenilir |
| Gradient Boost | %87.93 | Ensemble destekçi rol için uygundur |

> Hedef değişken: **Perakende Derece** | Model: 80/20 bölüm, n_estimators=300

---

##  Segment Profilleri (k=4)

| Segment | Profil |
|---|---|
|  **Bakır** | Küçük çaplı, düşük nüfus yoğunluklu, butik tarzı mağazalar |
|  **Bronz** | Orta ölçekli, gelişmekte olan bölge mağazaları |
|  **Gümüş** | Yüksek nüfuslu, kentsel; geniş çevre altyapısı |
|  **Altın** | Yüksek gelirli, beyaz yakalı ağırlıklı, premium segment |

---

##  Kurulum ve Çalıştırma

```bash
# Bağımlılıkları yükle
pip install pandas numpy scikit-learn xgboost imbalanced-learn shap matplotlib seaborn openpyxl

# Notebook'u çalıştır
jupyter notebook "Karar_Ağacı_Modelleriyle_Planogram_Tasarımı.ipynb"
```

>  **Not:** `Bitirme Analiz Dosyası.xlsx` ve `Around Store Data_Yeni_Anonim.xlsx` dosyaları ticari gizlilik kapsamında bu repoya dahil edilmemiştir. Notebook, bu dosyaların proje dizininde mevcut olduğu varsayımıyla çalışır.

---

##  Gizlilik Notu

Rakip marka ve öncelikli rakip marka sütunları ticari gizlilik gereği `Rakip_XX` ve `OncelikliRakip_XX` kodlarıyla maskelenmiştir. Planogram görsellerinde gösterilen SKU etiketleri (`M-01` – `M-63`) gerçek marka isimlerini içermemektedir.

---

##  Lisans

Bu proje yalnızca akademik ve araştırma amaçlı kullanım için paylaşılmıştır.
