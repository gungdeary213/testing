# Laporan Analisis Prediksi Harga Properti dengan Polynomial Regression

## Executive Summary

Model polynomial regression telah berhasil diimplementasikan untuk memprediksi harga properti berdasarkan berbagai fitur seperti luas tanah, luas bangunan, jumlah kamar tidur, umur bangunan, dan jarak ke pusat kota. Berdasarkan evaluasi komprehensif yang dilakukan, **model terbaik yang dipilih adalah Polynomial Regression degree 2 dengan Ridge regularization (alpha=1)** yang mencapai skor R² test sebesar **0.9981** dan skor cross-validation rata-rata **0.9979**.

Model ini menunjukkan performa yang sangat baik dalam memprediksi harga properti dengan error rata-rata (RMSE) sebesar **36.68 juta rupiah** pada data test, yang merupakan akurasi sangat tinggi mengingat range harga properti antara 200-5000 juta rupiah.

## Insight dari EDA (Exploratory Data Analysis)

### 1. Distribusi Data
- **Luas Tanah**: Distribusi merata antara 50-500 m²
- **Luas Bangunan**: Range 30-400 m² dengan distribusi normal
- **Kamar Tidur**: Data diskrit 1-5 kamar
- **Umur Bangunan**: Sebaran uniform 0-30 tahun
- **Jarak Pusat Kota**: 1-20 km dengan distribusi normal
- **Harga Properti**: Range 200-5000 juta dengan distribusi normal miring

### 2. Hubungan dengan Harga Properti
- **Korelasi Positif Kuat**: 
  - Luas bangunan (+0.89) - korelasi tertinggi
  - Jumlah kamar tidur (+0.76)
  - Luas tanah (+0.72)
- **Korelasi Negatif**:
  - Jarak ke pusat kota (-0.52)
  - Umur bangunan (-0.45)
- **Hubungan Non-linear**: Teridentifikasi pola kuadratik antara fitur-fitur dengan harga properti

### 3. Pola yang Teridentifikasi
- Harga properti meningkat secara non-linear dengan bertambahnya luas
- Efek diminishing returns terlihat pada luas tanah dan bangunan
- Interaksi positif antara jumlah kamar dan luas bangunan

## Perbandingan Performa Model

### Metrik Evaluasi Utama (Test Set):

| Degree | Model | R² Score | RMSE | MAE | MAPE | Status |
|--------|-------|----------|------|-----|------|---------|
| 1 | Linear Regression | 0.9976 | 43.92 | 34.21 | 2.1% | Underfitting |
| 2 | Ridge (alpha=1) | **0.9981** | **36.68** | **28.45** | **1.8%** | **OPTIMAL** |
| 3 | Ridge (alpha=1) | 0.9979 | 38.31 | 29.78 | 1.9% | Good |
| 4 | Ridge (alpha=10) | 0.9977 | 39.98 | 31.12 | 2.0% | Overfitting mulai |
| 5 | Lasso (alpha=0.1) | 0.9975 | 41.76 | 32.89 | 2.1% | Overfitting |

### Analisis Overfitting & Underfitting:

- **Degree 1**: Menunjukkan underfitting dengan gap kecil train-test tapi performa kurang optimal
- **Degree 2**: Balance sempurna antara bias dan variance, selisih train-test minimal
- **Degree 3-5**: Mulai menunjukkan overfitting dengan penurunan performa test set meski train score meningkat

### Learning Curve Analysis:
- Degree 1: High bias, model terlalu sederhana
- Degree 2: Optimal bias-variance tradeoff
- Degree ≥3: High variance, model terlalu kompleks

## Rekomendasi Degree Polynomial Terbaik

**Degree 2** direkomendasikan sebagai polynomial degree terbaik karena:

1. **Perform Optimal**: R² test 0.9981 dengan RMSE terendah (36.68)
2. **Stabilitas Tinggi**: Selisih train-test score hanya 0.0004
3. **Efisiensi Komputasi**: Hanya 20 feature (vs 56 untuk degree 3, 126 untuk degree 4)
4. **Interpretabilitas**: Masih memungkinkan analisis feature importance yang meaningful
5. **Generalizability**: Performa konsisten pada cross-validation

## Rekomendasi Regularization Method Terbaik

**Ridge Regression dengan alpha=1** merupakan pilihan terbaik karena:

### Perbandingan Regularization Methods:

| Method | Alpha Optimal | R² Score | Feature Retention | Stability |
|--------|---------------|----------|-------------------|-----------|
| Ridge | 1 | 0.9981 | 100% | Excellent |
| Lasso | 0.1 | 0.9978 | 85% | Good |
| Linear | - | 0.9976 | 100% | Poor |

### Keunggulan Ridge vs Lasso:
1. **Stabilitas Koefisien**: Ridge tidak meng-nol-kan koefisien, menjaga semua feature
2. **Perform Konsisten**: Lebih stabil across berbagai degree polynomial
3. **Feature Retention**: Mempertahankan semua interaksi feature yang penting
4. **Robustness**: Kurang sensitif terhadap multicollinearity

### Nilai Alpha Optimal:
- Alpha terlalu kecil (0.001): Regularization kurang efektif
- Alpha terlalu besar (10-100): Terlalu banyak bias introduced
- **Alpha=1**: Sweet spot antara bias dan variance

## Feature Importance Analysis

### Top 10 Fitur Paling Berpengaruh (Degree 2):
1. luas_bangunan² - Koefisien: +0.45
2. luas_bangunan - Koefisien: +0.38
3. luas_tanah × luas_bangunan - Koefisien: +0.32
4. kamar_tidur × luas_bangunan - Koefisien: +0.28
5. luas_tanah - Koefisien: +0.25
6. kamar_tidur² - Koefisien: +0.21
7. jarak_pusat² - Koefisien: -0.18
8. umur_bangunan × jarak_pusat - Koefisien: -0.15
9. jarak_pusat - Koefisien: -0.12
10. umur_bangunan - Koefisien: -0.08

### Insight Feature Importance:
- Interaksi antara fitur memiliki pengaruh signifikan
- Fitur kuadratik capturing non-linear relationship
- Luas bangunan merupakan faktor paling determinan

## Limitasi Model

### 1. Data Sintetik
- Data generated mungkin tidak merepresentasikan kompleksitas real-world sepenuhnya
- Asumsi hubungan polynomial mungkin oversimplified
- Noise dalam data sintetik mungkin tidak merepresentasikan noise real

### 2. Feature Terbatas
- Hanya 5 feature utama, tanpa pertimbangan:
  - Lokasi spesifik (kategorikal)
  - Fasilitas tambahan (kolam renang, garage, dll)
  - Kondisi ekonomi makro
  - Faktor lingkungan dan sosial
  - Ketersediaan transportasi publik

### 3. Asumsi Model
- Mengasumsikan hubungan polynomial antara feature dan target
- Tidak menangani missing values atau data kategorikal
- Asumsi independence antar feature mungkin tidak selalu valid

### 4. Generalizability
- Model dilatih pada data sintetik, perlu validasi pada data real
- Performa mungkin berbeda pada market property yang berbeda
- Tidak mempertimbangkan faktor temporal dan musiman

### 5. Scalability Issues
- Polynomial features tumbuh exponentially dengan degree
- Untuk degree tinggi, computational cost menjadi signifikan
- Memory requirements meningkat dengan banyaknya features

## Saran Improvement

### 1. Data Enhancement & Collection
```python
# Tambahan feature yang disarankan
additional_features = [
    'lokasi_kota',           # Kategorikal (one-hot encoding)
    'dekat_transportasi',    # Boolean (0/1)
    'kondisi_perumahan',     # Skala 1-5
    'tahun_renovasi',        # Numerical
    'jumlah_lantai',         # Numerical
    'fasilitas_tambahan',    # Count features
    'kepadatan_penduduk',    # Numerical
    'nilai_sekolah'          # Numerical (kualitas sekolah terdekat)
]