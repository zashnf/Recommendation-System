# Recommendation System - Zashika Hanifa

## Project Overview 
Pertumbuhan banyaknya penonton bioskop yang meningkat selaras dengan banyaknya jumlah film yang diproduksi. Berbagai film dengan alur cerita, genre, dan tema film yang serupa ataupun berbeda beda meramaikan pasar industri dari bidang perfilman di luar negeri hingga dalam negeri. Dari banyaknya film yang diproduksi membuat calon penonton bingung dan kesulitan untuk mencari dan menentukan film apa yang akan ditonton selanjutnya sehingga menghabiskan waktu lebih banyak dalam mencari film. Beberapa orang menggunakan fitur yang disediakan di beberapa situs untuk mencari film untuk memutuskan film yang akan ditonton. Setiap orang memiliki selera yang berbeda beda dan cenderung memilih menonton film yang serupa dengan film yang disukainya. Salah satu cara untuk mendapatkan informasi yang tepat terhadap film adalah dengan sistem rekomendasi. Sistem rekomendasi membantu untuk menavigasi dengan cepat dan menerima informasi yang diperlukan [1].

## Business Understanding
### Problem Statements
Berdasarkan penjelasan yang telah disampaikan maka permasalahan yang didapat yaitu:
- Bagaimana membuat sistem rekomendasi dengan teknik _Content Based Filtering_? 
- Bagaimana membuat sistem rekomendasi berdasarkan preferensi masa lalu pengguna untuk menemukan film yang serupa?
  
### Goals

- Menghasilkan beberapa rekomendasi film dengan teknik _Content Based Filtering_ 
- Menghasilkan beberapa rekomendasi film sesuai preferensi pengguna dimasa lalu yang belum pernah ditonton dengan teknik _Collaborative Filtering_


### Solution Approach
Solusi yang dapat dilakukan untuk menangani permasalahan sebagaimana terdapat dalam _problem statements_, yaitu dengan menggunakan metode _Content Based Filtering_ dan _Collaborative filtering_ .

## Data Understanding
Dataset yang digunakan didalam proyek ini didapat pada situ Kaggle, untuk mendownload dataset dapat dilakukan di [link](https://www.kaggle.com/datasets/rohan4050/movie-recommendation-data?select=ml-latest-small) berikut. 
Dataset terdiri dari beberapa file seperti movies, tags, ratings, dan links. Dimana :
- Pada file Movies terdapat 9742 baris dan 3 kolom. Untuk _sample_ dataset _movies_ dapat dilihat pada Tabel 1. 
  
Tabel 1. Sample Dataset Movies

| **movieId**  | **title**  | **genres** |
|--------------|------------|------------|
|   1|   Toy Story(1995)|   Adventure, Animation, Children, Fantasy|
|   2|   Jumanji(1995)|   Adventure, Children, Fantasy|
|   3|   Grumpier Old Men(1995)|   Comedy, Romance|

Pada Tabel 1 _moviesId_ memuat nomor id film, _title_ memuat judul film, dan _genres_ memuat _genre_ film

- Pada file Tags terdapat 3683 baris dan 4 kolom. Untuk _sample_ dataset _tags_ dapat dilihat pada Tabel 2.

Tabel 2. Sample Dataset Tags

| **userId** | **movieId** | **tag** | **timestamp** |
|---|---|---|---|
|   2|   60756|   funny|   1445714994|
|   2|   60756|   Highly quoteable|   1445714996|
|   2|   89774|   Boxing story|   1445715207|

Pada Tabel 2  _userId_ memuat nomor id _user_, _movieId memuat nomor id film, _tag_ memuat _tag_ film, dan _timestamp_ memuat kode _timestamp_.

- Pada file Ratings terdapat 101000 baris dan 4 kolom. Untuk _sample_ dataset _ratings_ dapat dilihat pada Tabel 3.

Tabel 3. Sample Dataset Ratings  

| **userId** | **movieId** | **rating** | **timestamp** |
|------------|-------------|---------|---------------|
|   1|   1|   4.0|   964982703|
|   1|   3|   4.0|   964981247|
|   1|   47|   5.0|   964983815|

  Pada Tabel 3 _userId_ memuat nomor id _user_, _movieId_ memuat nomor id film, _rating_ memuat penilaian film dalam rentang 0.5 - 5, dan _timestamp_ memuat kode _timestap_.
  
- Pada file Links terdapat 9742 baris dan 3 kolom. Untuk _sample_ datast _links_ dapat dilihat pada Tabel 4.

Tabel 4. Sample Dataset Links

| **movieId**  | **imdbId**  | **tmdbId** |
|---|---|---|
|   1|   114709|    862.0|
|   2|   113497|   8844.0|
|   3|   113228|   15602.0|

Pada Tabel 4 _movieId_ memuat nomor id film yang merujuk pada website MoviLens, _imdbId_ memuat nomor id film yang merujuk pada website IMDB, dan _tmdbId yng memuat nomor id fim yang merujuk pada website TMDB.

### Exploratory Data Analysis (EDA)
Pada proses _Exploratory Data Analysis_ dilakukan untuk melihat hubungan antar dataset berdasarkan variabel id, disini menggunakan _moviesId__ dan _userId_.

- _moviesId_
Menggabungkan seluruh movieId yang merujuk ke film yang sama pada file (_movies_, _tags_, _ratings_, dan _links_) dan disimpan pada _all_movies_.Sehingga didapat jumlah seluruh data _movies_ berdasarkan _moviesId_ sebanyak 9742.

- _userId_
Menggabungkan semua userId pada users, dalam hal ini userId pada file _tags_ dan _ratings_, kemudian mengambil nilai unik pada _userId_ . Sehingga didapat jumlah seluruh pengguna(_user_) ada sebanyak 610.

## Data Preparation
Tahapan _data preparation_ yang dilakukan pada  _Content Based Filtering_ , yaitu sebagai berikut:
- Mengubah dataset _movies_, _tags_, _ratings_, dan _links_ menjadi _dataframe_ dengan menggunakan pandas.
- Melakukan _Exploratory Data Analysis_ terhadap _movieId_ dan _userId, bisa dilihat pada Gambar 1 dan Gambar 2.
- Menggabungkan data dengan fungsi _merge_
Melakukan _merge_(penggabungan) file _ratings_ dan file _movies_ dengan _left join_ (dalam hal ini mengambil data _ratings_(df kiri(_left_)) dan membuang baris dari df kanan yang tidak memiliki kecocokan dikolom kunci df kiri(_left_)) dalam hal ini berdasarkan _movieId_ dan disimpan di df _movies_names_. Untuk hasil _merge_ file _ratings_ dan file _movies_ dapat dilihat pada Tabel 5.

Tabel 5. Hasil _Merge_ file _ratings_ dan file _movies_  

|userId|movieId|rating|timestamp|title|genres|
|---|---|---|---|---|---|
| 1|1|4.0|964982703|Toy Story (1995)|Adventure, Animation, Children, Comedy, Fantasy|
| 1|50|5.0|964982931|Usual Suspects, The (1995)	|Crime, Mystery, Thriller |
|...|...| ...|...|...|...|
|610|166534|4.0|1493848402|Split (2017)|Drama, Horror, Thriller|
|610|168250|5.|1494273047|Get Out (2017)|	Horror|

Kemudian dilanjutkan dengan menggabungkan _movies_names_ dan _tags_ dengan _left join_ berdasarkan _movieId_. Untuk hasilnya bisa dilihat pada Tabel 6.

Tabel 6. Hasil _Merge_ file _movies_names_ dan _tags_

|**userId**|**movieId**|**rating**|**timestamp**|**title**|**genres**|**tag**|
|---|---|---|---|---|---|---|
|1	|1|	4.0|	964982703	|Toy Story (1995)|Adventure, Animation, Children, Comedy, Fantasy|pixar|
|1	|1|	4.0|	964982703   |Toy Story (1995)|Adventure, Animation, Children, Comedy, Fantasy|pixar|
|1 |1|	4.0|	964982703	|Toy Story (1995)|Adventure, Animation, Children, Comedy, Fantasy|fun|
|1	|3|	4.0|964981247	|Grumpier Old Men (1995)|Comedy, Romance|moldy|
|...|...|...|...|...|...|...|
|610|	168252|5.0|	1493846352	|Logan (2017)|Action, Sci-Fi|emotional|
|610|	168252|5.0|	1493846352	|Logan (2017)|Action, Sci-Fi|gritty|
|610| 170875|3.0|	1493846415	|The Fate of the Furious (2017)|Action, Crime, Drama, Thriller|NaN|

- Mengecek _missing value_ dengan fungsi _isnull_. Fungsi ini akan mengecek nilai pada setiap kolom apakah ada nilai yang hilang atau tidak, disini saya mengecek _missing value_ pada data  _movies_merge_ dan hasilnya terdapat _missing values_ (NaN) sekitar 52549 pada kolom _tags_, maka bisa menggunakan _dropna_ untuk menghapus _missing value_. Jumlah data ada 285.762 baris dan 7 kolom.
- Membersihkan data dari nilai _null_ dengan menggunakan fungsi _dropna_. Fungsi ini akan menghapus semua baris yang memiliki _missing value_ (NaN) pada kolom _tags_. Sehingga jumlah data setelah dihapus _missing value_ nya ada 233.213 baris dan 7 kolom.
- Membersihkan data dari duplikasi menggunakan fungsi _drop duplicates_.
Membuang data duplikat dengan _drop_duplicates_ pada _movieId_,sehingga _movieId_ tidak ada yang sama dan didapati data sebanyak 1554 baris dan 7 kolom.
- Mengembalikan daftar _list_ _movieId_,_title_, dan _genres_ pada _id_, _names_, dan _genres_. Kemudian simpan didataframe baru _new_movies_ lalu _dictionary_ untuk menentukan pasangan _key-value_ pada data yang telah disiapkan. Hasilnya dapat dilihat pada Tabel 7.

Tabel 7. Data _new_movies_

| **id**	|**names**|	**genres**|
|---|---|---|
| 1|Toy Story (1995)|Adventure, Animation, Children, Comedy, Fantasy|
| 2|Jumanji (1995)|Adventure Children, Fantasy  |
| 3|Grumpier Old Men (1995)| Comedy, Romance|
| 5|Father of the Bride Part II (1995)|	Comedy|
| 7|Sabrina (1995)|Comedy, Romance|

- Melakukan _mapping_ _array_ dari kolom _genres_ ke _feature_names_. _get_feature_names_out_ akan mengeluarkan nilai array sebanyak 24 _genres_ yang nantinya akan di _fit_ dan _transform_ ke TfidfVectorizer. Kemudian melakukan _fit_ dan _transform_ TfidfVectorizer ke dalam bentuk matriks menggunaan fungsi _todense_. Matriks yang dimiliki berukuran (1554,24) Dimana nilai 1554 merupakan ukuran data dan 24 adalah banyaknya kategori genres. Selanjutnya jika ingin melihat matriks tfidf untuk beberapa nama film (disini saya menampilkan 5 nama film)berdasarkan _genres_ yang dapat dilihat pada Tabel 9.
- Menggunakan cosine similarity untuk menghitung derajat kesamaan antar film, dapat dilihat pada Tabel 10.
- Membuat fungsi rekomendasi dengan parameter:
    - movies_name = berisikan judul film.
    - similarity_data = berisi tentang hasil perhitungan cosine.
    - Items = fitur yang digunakan untuk kemiripan (disini menggunakan names dan genres).
    - k = banyak rekomendasi yang ingin diberikan(menggunakan k sebanyak 3).
 

Tahapan _data preparation_ yang dilakukan pada  _Collaborative Filtering_ , yaitu sebagai berikut:
- Mengimport library yang dibutuhkan(tensorflow, keras, layers, dan matplotlib).
- Melakukan _encode_ fitur _user_ dan _movies_ untuk memetakan setiap nilai fitur tersebut ke dalam bentuk index.
- Mapping _userId_ dan _moviesId_ ke dataframe. Memetakan _userId_ dan _movieId_ kedalam df _users_ dan df _movies_.
- Mengacak dataset agar distribusinya menjadi _random_ hasilnya disimpan pada dataframe dataf yang bisa dilihat pada Tabel 8.

Tabel 8. Sample dataf

  |userId|movieId|rating|timestamp|users|movies|ratings|
  |---|---|---|---|---|---|---|
  |432|77866|4.5|1335139641|431|4730|4.5|
  |288|474|3.0|978465565|287|474|3.0|
  |42|2987|4.0|996262677|41|194|4.0|
  |599|4351|3.0|1498524542|598|2631|3.0|
  |...|...|...|...|...|...|...|
  |42|4005|4.0|996259059|41|1873|4.0|
  |364|141|4.0|869443367|363|524|4.0|
  |480|6867|4.0|1179163171|479|2240|4.0|
  |103|6711|5.0|1431957425|102|2046|5.0|
- Memetakan data _users_ dan _movies_ menjadi satu value,membuat variabel y untuk membuat ratings, membagi data menjadi 70% data train dan 30% data validasi
- Melakukan teknik _embedding_ untuk menghitung skor kecocokan antara _movies_ dengan _user_
- Melakukan _compile_ model dengan metrik evaluasi RMSE
- Membuat visualisasi metrik evaluasi dengan plot, bisa dilihat ada Gambar 6.
- Membuat rekomendasi _movies_

## Modeling and Result
Pada proyek ini saya melakukan modeling dengan menggunakan algoritma _Content Based Filtering_ dan _Collaborative Filtering_. 

### _Content Based Filtering_ 
Pada _Content Based Filtering_ rekomendasi yang dihasilkan itu berdasarkan dengan _item_ atau dalam hal ini film yang disukai sebelumnya, sehingga hasilnya akan menampilkan beberapa film yang memiliki _genres_ mirip dengan yang sebelumnya. Cara kerja dari model ini untuk mendapatkan hasil rekomendasi yaitu dimulai dari menyiapkan dataframe yang telah dibersihkan pada tahap _data preparation_ sebelumnya. Kemudian menggunakan _tfidfvectorizer_ yang ada pada _library scikit-learn_ untuk mencari kemiripan bobot pada _genres_ dalam bentuk matriks. Untuk hasil yang dikoversikan ke dalam dataframe bisa dilihat pada Tabel 9. 

Tabel 9. Matriks tfidf berdasarkan _genres_

|**names**|**crime**|**musical**|**war**|**drama**|**film**|...|**horror**|**documentary**|**action**|**comedy**|
|---|---|---|---|---|---|---|---|---|---|---|
|Igby Goes Down (2002)|0.000000|0.0|0.000000|0.600342|0.000000|...|0.0|0.0|0.000000|0.799743|
|Blood Simple (1984)|0.396795|0.0|0.000000|0.206813|0.632369|...|0.0|0.0|0.000000|0.000000|
|...|...|...|...|...|...|...|...|...|...|...|
|Apocalypse Now (1979)|0.000000|0.0|0.772897|0.304359|0.000000|...|0.0|0.0|0.556774|0.000000|
|Celebration, The (Festen) (1998)|0.000000|0.0|0.000000|1.000000|0.000000|...|0.0|0.0|0.000000|0.000000|

Kemudian hasil yang ada pada Tabel 9 akan melalui tahap _cosine similarity_ untuk dihitung derajat kesamaan antar film. output dari _cosine similarity_ berupa matriks ini bisa dikonversikan ke bentuk dataframe yang bisa dilihat pada Tabel 10.

Tabel 10. Sample data _cosine_

|names|Da Vinci Code, The (2006)|Lost Weekend, The (1945)|City of God (Cidade de Deus) (2002)|Chain Reaction (1996)|Love Me If You Dare (Jeux d'enfants) (2003)|
|---|---|---|---|---|---|
|Life Is Beautiful (La Vita è bella) (1997)|0.098907|0.291622|0.076756|0.000000|0.548325|
|Hurricane, The (1999)|0.339161|1.000000|0.263204|0.000000|0.531842|
|...|...|...|...|...|...|
|Devil's Rejects, The (2005)|0.000000|0.000000|0.514006|0.297762|0.000000|
|Good Copy Bad Copy (2007)|0.000000|0.000000|0.000000|0.000000|0.000000|

Tahapan terakhir pada _Content Based Filtering_ yaitu membuat fungsi rekomendasi dengan parameter _movies_name_, _similarity_data_, _Items_ , dan k. Fungsi ini bekerja dengan mengambil _similarity_data_ yang berisikan hasil perhitungan _cosine_similarity_ dengan _items_ _names_ dan _genres_ yang digunakan untuk kemiripan pada _dataframe_ _new_movies_, terdapat juga variabel _closest_ yang memuat data nama film(_movies_name_) yang mirip. Lalu parameter k memuat banyaknya rekomendasi film yang ingin diberikan(disini saya memasukkan k=3). 

Misal disini saya memasukan nama film yang ada pada Tabel 11. maka ia akan mencari _genres_ yang serupa dengan film yang dimasukan itu. Untuk hasil rekomendasi dari _Content Based Filtering_ dapat dilihat pada Tabel 12.
 
Tabel 11. Film yang disukai sebelumnya

|  **id** | **names** | **genres** |
|---------|-----------|------------|
|  118696 |The Hobbit: The Battle of the Five Armies(2014)| Adventure, Fantasy|

Dapat dilihat bahwa pada tabel 11 _user_ menyukai atau pernah menonton film The Hobbit: The Battle of the Five Armies(2014) yang ber _genres_ _Adventure_ dan _Fantasy_ . Maka sistem akan merekomendasikan beberapa film berdasarkan _genres_ yang disukai oleh _user_ yaitu _Adventure_ dan _Fantasy_ bisa dilihat pada Tabel 12

Tabel 12. Hasil Rekomendasi Film The Hobbit: The Battle of the Five Armies(2014)

| **names** | **genres** | 
|-----------|------------|
| Lord of the Rings:The Two Towers,The(2002)| Adventure, Fantasy|
| Harry Potter and the Chamber of Secrets(2002)| Adventure, Fantasy|  
| Excalibur(1981)| Adventure, Fantasy|

Pada Tabel 12 dapat dilihat bahwa _genres_ yang direkomendasikan oleh sistem tidak jauh berbeda dengan _genres_ film yang pernah ditonton sebelumnya.

###  _Collaborative Filtering_
Pada _Collaborative Filtering_ rekomendasi yang dihasilkan itu berdasarkan dari _rating_ yang diberikan oleh _user_ pada film film yang sebelumnya pernah dinilai(_rating_). 

Cara kerja dari  _Collaborative Filtering_ ini dimulai dari mengimpor beberapa _library_ yang digunakan, disini daya menggunakan tensorflow dan dataframe yang dipakai adalah file _ratings_ yang sebelumnya telah digunakan. Kemudian melakukan data _preparation_ dimana pada proses ini melakukan encode pada fitur _userId_ dan _moviesId_ yang berfungsi untuk memetakan setiap nilai fitur tersebut ke dalam bentuk index. Setelah itu melakukan _split data_ _training_ dan _validation_ disini saya membagi dengan komposisi 70:30. _Split data_ ini dibuat dengan tujuan agar data yang digunakan dapat digunakan untuk mengembangkan model dan mengevaluasi _performance_ dari model yang telah dikembangkan. Setelah melakukan _split data_ maka akan lanjut ke proses _training_ dengan mengimplementasikan teknik _embedding_ untuk menghitung skor kecocokan antara _movies_ dengan _user_, lalu melakukan _compile model_ dengan _BinaryCrossentropy_ untuk menghitung _los function_, Adam sebagai _optimizer_, dan RMSE sebagai _metrics evaluation_. Tahapan terakhir pada  _Collaborative Filtering_ yaitu membuat _model predict_ untuk membuat rekomendasi film. Untuk hasil dari _Collaborative Filtering_ dapat dilihat pada Tabel 13 dan Tabel 14.

Tabel 13. Movies with high rating from user 599

| **names** | **genres**|
|-----------|----------|
|His Girl Friday(1940)| Comedy, Romance|
|Magnolia (1999)| Drama|
|Lost in Translation (2003) | Comedy, Drama, Romance| 
|Kill Bill: Vol 1 (2003)| Action, Crime, Thriller|

Pada tabel 13 diatas memuat beberapa film yang pernah dirating oleh _user_, _genres_ yang disukai-pun cukup beragam sehingga hasil rekomendasi yang didapat bisa dilihat pada Tabel 14. 

Tabel 14. Top 5 Movies Recommendation

| **names** | **genres**|
|-----------|----------|
|   More(1998)|   Animation, Drama, Sci-fi, IMAX|
|   Woman Under the Influence, A(1974)|   Drama|
|   Adam's Rib(1949)|   Comedy, Romance|
|   Into the Woods (1991)|   Comedy, Fantasy, Musical|
|   Wild Parrots of Telegraph Hill, The (2003)|   Documentary|  

Bisa dilihat pada Tabel 14 diatas bahwa hasil rekomendasi film yang diberikan oleh sistem memiliki _genres_ yang tidak terlalu jauh berbeda dengan _genres_ film yang pernah dinilai(_rating_) sebelumnya.


## Evaluation
Metrik evaluasi yang digunakan pada _Content Based Filtering_ adalah metrik _precision_. Rumus dari metrik _precision_ dapat dilihat pada gambar

![precision](https://github.com/zashnf/Recommendation-System/assets/89719711/77468252-cf7b-441b-8a33-c6312deb8067)

Gambar 5. Rumus _Precision_

Mengacu pada contoh rekomendasi film di Tabel 11, diketahui bahwa The Hobbit: The Battle of the Five Armies(2014) termasuk ke dalam kategori _Adventure_ dan _Fantasy_. Maka dari 3 item yang direkomendasikan di Tabel 2, 3 item memiliki kategori _Adventure_ dan _Fantasy_(_similar_). Artinya jika menggunakan rumus _precision_ hasil yang didapat sebesar 3/3 atau 100%.

Pada _Collaborative Filtering_ metrik evaluasi yang digunakan adalah RMSE (_Root Mean Squared Error_). RMSE (_Root Mean Squared Error_) berfungsi untuk mendapatkan besaran tingkat kesalahan dari hasil prediksi. Untuk menerapkannya bisa dengan menggunakan: 

 		metrics=[tf.keras/metrics.RootMeanSquaredError()]

Visualisasi model evaluasi metrik dapat dilihat pada gambar 6 dibawah ini.

![model](https://github.com/zashnf/Recommendation-System/assets/89719711/0727adc2-6fe5-4505-8622-0e701eecf2bf)

Gambar 6. model evaluasi

Pada gambar 6. proses training model dengan batch size 64 serta epoch 100  diperoleh nilai error akhir (RMSE) di sekitar 0.1939 dan error pada data validasi sebesar 0.2064.  Nilai tersebut cukup bagus untuk sistem rekomendasi. Semakin kecil (mendekati 0) nilai RMSE maka semakin akurat nilai prediksinya [2].Sehingga analisa grafik pada Gambar 6 dapat dikatakan _overfit_ karena _training error_ menurun dan _validation error_ naik.

### Kesimpulan
Kesimpulan dari pembuatan sistem rekomendasi film ini adalah baik secara _Content Based Filtering_ dan _Collaborative Filtering_ masing masing dapat merekomendasikan film berdasarkan kriterianya, dimana pada _Content Based Filtering_ rekomendasi yang dihasilkan berdasarkan film yang disukai oleh _user_ sedangkan pada _Collaborative Filtering_ hasil rekomendasinya berdasarkan _rating_ tinggi pada film yang pernah diberikan oleh pengguna sebelumnya. Pada _Content Based Filtering_ evaluasi metrik _precision_ yang didapat sebesar 100% dan evaluasi metrik pada _Collaborative Filtering_ didapati nilai error akhir (RMSE) 0.1939 dan error pada data validasinya sebesar 0.2064.


### Referensi :

[1] L. Lohar, G. Malve, T. Malviya, S. Sabnis, Movie Recommendation System. International Journal on Recent and Innovation Trends in Computing and Communication. 9, 13–16 (2021).

[2] F. H. Hamdanah and D. Fitrianah, “Analisis Performansi Algoritma Linear Regression dengan Generalized Linear Model untuk Prediksi Penjualan pada Usaha Mikra, Kecil, dan Menengah”, j. nas. pendidik. teknik. inform., vol. 10, no. 1, pp. 23–32, Apr. (2021).


