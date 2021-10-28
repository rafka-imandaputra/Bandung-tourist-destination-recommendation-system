# Sistem Rekomendasi Wisata Kota Bandung Berbasis Collaborative Filtering
## 1. Domain Projek
### 1.1. Latar Belakang
Sebelum bepergian, biasanya seseorang akan membuat rencana terlebih dahulu tentang lokasi yang akan dikunjungi dan waktu keberangkatannya. Hal ini dilakukan untuk perencanaan sebelum berwisata ke suatu Kota.

Kota Bandung memiliki daya tarik wisata yang cukup tinggi, namun dikutip dari humas.bandung.go.id/ jumlah wisatawan ke Kota Bandung turun 50% pada tahun 2021. Tentunya angka ini wajar karena penyebaran Covid-19 yang mengkhawatirkan di Indonesia.

Untuk periode Oktober 2021, sejumlah wisata di Kota Bandung sudah mulai beroperasi, ini akan menjadi momentum yang tepat untuk menggerakkan pariwisata kembali.

Pada projek ini, saya akan membuat sebuah sistem rekomendasi berbasis collaborative filtering untuk menampilkan top-n recommendation destinasi wisata di Kota Bandung berdasarkan data user, rating, dan place yang diharapkan akan menambah jumlah kunjungan wisata di Kota Bandung
## 2. Business Understanding
### 2.1. Problem Statements
Masalah yang melatarbelakangi projek ini adalah bagaimana dapat meningkatkan jumlah kunjungan terhadap wisata di Kota Bandung melalui sistem rekomendasi dan bagaimana cara membuat model yang dapat memberi hasil rekomendasi paling sesuai.
### 2.2. Goals
Tujuan dalam projek ini adalah menampilkan top 7 rekomendasi tempat wisata yang mungkin saja disukai user.
### 2.3.Solution Approach
1. Preprocessing Data
    - Membuang beberapa kolom yang tidak digunakan
    - Membuang tempat wisata yang bukan berasal dari Kota Bandung
    - Menyocokkan data user dan data rating agar hanya berisi tempat wisata yang berasal dari Kota Bandung
    - Membersihkan data duplikasi.
2. Persiapan Data
    - Encoding label place dan user 
    - Mapping label place dan user hasil encoding ke data rating
    - Normalisasi kolom rating
3. Pemodelan
    - Membagi dataset menjadi train dan test
    - Menginisialisasi model RecommenderNet
    - Menginisialisasi callbacks
    - Melakukan training dengan model RecommenderNet
    - Melakukan rekomendasi berdasarkan hasil encoded suatu User_Id 

## 3. Data Understanding
### 3.1. Informasi Data
Dataset yang saya gunakan ada 3 jenis data:
* tourism_with_id.csv - mengandung informasi tempak wisata di 5 kota besar di Indonesia (hanya kota Bandung yang dipakai)
* user.csv - mengandung informasi pengguna untuk membuat rekomendasi fitur berdasar pengguna
* tourism_rating.csv - mengandung informasi pengguna, tempat wisata, dan rating untuk membuat sistem rekomendasi berdasar rating

### 3.2. Sumber Data
https://www.kaggle.com/aprabowo/indonesia-tourism-destination
### 3.3. Uraian Fitur Data
Adapun fitur-fitur datanya adalah sebagai berikut:
* tourism_with_id.csv
    - Place_Id,	berisi id unique tempat wisata (int)
    - Place_Name, berisi nama tempat wisata (obj)
    - Description, berisi deskripsi dari tempat wisata (obj)
    - Category, berisi kategori tempat wisata (Cagar Alam, Budaya, Taman Hiburan, Tempat Ibadah, Pusat Perbelanjaan) (obj)
    - City, berisi nama kota dari tempat wisata (obj)
    - Price, berisi harga masuk dari tempat wisata (int)
    - Rating, berisi rating rata-rata dari tempat wisata (float)
    - Lat, berisi latitude dari tempat wisata (float)
    - Long, berisi longitude dari tempat wisata (float)
* user.csv
    - User_Id, berisi id unique dari user (int)
    - Location, berisi asal kota dan provinsi dari user (obj)
    - Age, berisi usia dari user (int)
* tourism_rating.csv
    - User_Id, berisi id dari user (turunan dari user.csv) (int)
    - Place_Id, berisi id tempat wisata yang diberi ratingnya oleh user (int)
    - Place_Ratings, berisi rating yang diberi oleh user terhadap PlaceId (float)

Visualisasi wisata dengan jumlah rating terbanyak:

![satu](https://github.com/rafka-imandaputra/Machine-Learning-Terapan/blob/main/satu.png?raw=true)

Visualisasi jumlah kategori wisata di Kota Bandung:

![dua](https://github.com/rafka-imandaputra/Machine-Learning-Terapan/blob/main/dua.png?raw=true)

Visualisasi distribusi usia user:

![tiga](https://github.com/rafka-imandaputra/Machine-Learning-Terapan/blob/main/tiga.png?raw=true)

Visualisasi distribusi harga masuk tempat wisata:

![empat](https://github.com/rafka-imandaputra/Machine-Learning-Terapan/blob/main/empat.png?raw=true)

Visualisasi asal kota dari user:

![lima](https://github.com/rafka-imandaputra/Machine-Learning-Terapan/blob/main/lima.png?raw=true)

## 4. Data Preparation
Berikut adalah beberapa langkah yang saya lakukan untuk mempersiapkan data:
1. Preprocessing Data
    - Membuang beberapa kolom yang tidak digunakan
        Terdapat beberapa kolom yang tidak digunakan, seperti kolom 'Unnamed: 11','Unnamed: 12', dan 'Time_Minutes' dari data tourism_with_id.csv.
    - Membuang tempat wisata yang bukan berasal dari Kota Bandung
        Disini saya ingin membuat sistem rekomendasi untuk wisata di Kota Bandung, sehingga semua data yang merujuk pada wisata diluar Kota Bandung, maka datanya dibuang menggunakan filtering pada kolom City di data tourism_with_id.csv
    - Menyocokkan data user dan data rating agar hanya berisi tempat wisata yang berasal dari Kota Bandung
        semua data yang merujuk pada wisata diluar Kota Bandung, maka datanya dibuang menggunakan right join berdasarkan place_id yang berasal dari data tourism_with_id.csv
    - Membersihkan data duplikasi.
        Hal ini dilakukan karena dapat menyebabkan munculnya data yang sama sebanyak 2 kali atau lebih pada sistem rekomendasi yang nantinya dibuat. Oleh karena itu data duplikasi ini perlu dihilangkan karena sebenarnya data tersebut sudah ada pada dataset.
2. Persiapan Data
    - Encoding label place dan user 
        Ini dilakukan untuk memberi encode identifier kepada place dan user
    - Mapping label place dan user hasil encoding ke data rating
        Setelah identifier dibuat, hasilnya dimasukan pada data tourism_rating.csv (rating) untuk persiapan memasuki tahap pemodelan
    - Normalisasi kolom rating
        Hal ini dilakukan agar rentang nilai pada label numerik hanya antara 0-1 sehingga dapat mempercepat komputasinya menggunakan MinMaxScaler dari sklearn.

## 5. Modeling & Result
Setelah dilakukan pra-pemrosesan data, selanjutnya adalah membuat sistem rekomendasi collaborative filtering.
1. Membagi dataset menjadi train dan validation
        Disini saya membagi data rating (tourism_rating.csv) menjadi 80% data train dan 20% data validasi
2. Menginisialisasi model RecommenderNet
        Disini saya melakukan proses embedding terhadap data user dan place. Selanjutnya, melakukan operasi perkalian dot product antara embedding user dan place serta menambahkan bias untuk setiap user dan place. Skor kecocokannya ditetapkan dalam skala [0,1] dengan fungsi aktivasi sigmoid melalui Class RecommenderNet
3. Menginisialisasi callbacks
        Disini saya menggunakan callbacks untuk menghentikan pelatihan bila nilai validasi dibawah 0.25
4. Melakukan training dengan model RecommenderNet
        Selanjutnya, model di compile dengan Binary Crossentropy untuk menghitung loss function, Adam untuk optimizer, dan root mean squared error sebagai metrics evaluation.
5. Melakukan rekomendasi berdasarkan hasil encoded suatu User_Id 
        Disini saya mengambil satu sampel user untuk kemudian dicari tempat wisata mana saja yang sudah dikunjungi user, kemudian User_Id dari user tersebut diambil untuk dicari hasil encodingnya untuk kemudian dijadikan sebagai data testing dari model yang sudah dibuat sebelumnya. Setelah hasil prediksi keluar, saya mengurutkan top 7 tempat wisata rekomendasi untuk user tersebut.

Ini adalah hasil rekomendasi yang ditampilkan pada satu sample user:

![hasil](https://github.com/rafka-imandaputra/Machine-Learning-Terapan/blob/main/rek.png?raw=true)

## 6. Evaluation
Untuk mengukur kinerja model RecommenderNet untuk sistem rekomendasi digunakan metriks RMSE yang mana metriks ini memprediksi Place_Ratings yang dinormalisasi berdasarkan User_Id dan Place_Id yang sudah di encoded.

RMSE atau RMSD dihitung dengan menguadratkan error (predicted – observed) dibagi dengan jumlah data (= rata-rata), lalu diakarkan. Secara matematis, rumusnya ditulis sebagai berikut

![rmse](https://i0.wp.com/rifqimulyawan.com/wp-content/uploads/Gambar-Rumus-Dan-Cara-Menghitung-Root-Mean-Square-Error-RMSE.png?resize=696%2C353&ssl=1)

Kelebihan dari RMSE yaitu memiliki tingkat sensitivitas yang cukup tinggi, dan RMSE juga menghindari penggunaan pengambilan nilai absolut, yang tidak diinginkan dalam banyak perhitungan matematis.

RMSE pada model collaborative filtering ini berkisar 0.3562, tentunya ini dirasa sudah cukup untuk dapat menampilkan hasil rekomendasi yang paling sesuai.


## 6. Penutup
Model untuk menampilkan top 7 rekomendasi tempat wisata telah selesai dibuat dan model ini dapat digunakan untuk menampilkan rekomendasi kepada user yang harapannya dapat meningkatkan jumlah wisatawan di Kota Bandung. Namun demikian beberapa pengembangan lain masih dapat dilakukan agar dapat membuat model yang menampilkan rekomendasi yang lebih sesuai dengan kebiasaan pengguna, dll.

Terimakasih:)
