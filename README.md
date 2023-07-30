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
- Pada file Movies terdapat 9742 baris dan 3 kolom
  
Tabel 1. Sample Dataset Movies

| **movieId**  | **title**  | **genres** |
|--------------|------------|------------|
|   1|   Toy Story(1995)|   Adventure, Animation, Children, Fantasy|
|   2|   Jumanji(1995)|   Adventure, Children, Fantasy|
|   3|   Grumpier Old Men(1995)|   Comedy, Romance|

- Pada file Tags terrdapat 3683 baris dan 4 kolom

Tabel 2. Sample Dataset Tags

| **userId** | **movieId** | **tag** | **timestamp** |
|------------|-------------|---------|---------------|
|   2|   60756|   funny|   1445714994|
|   2|   60756|   Highly quoteable|   1445714996|
|   2|   89774|   Boxing story|   1445715207|
  
- Pada file Ratings terdapat 101000 baris dan 4 kolom

Tabel 3. Sample Dataset Ratings  

| **userId** | **movieId** | **rating** | **timestamp** |
|------------|-------------|---------|---------------|
|   1|   1|   4.0|   964982703|
|   1|   3|   4.0|   964981247|
|   1|   47|   5.0|   964983815|
  
- Pada file Links terdapat 9742 baris dan 3 kolom

Tabel 4. Sample Dataset Links

| **movieId**  | **imdbId**  | **tmdbId** |
|---|---|---|
|   1|   114709|    862.0|
|   2|   113497|   8844.0|
|   3|   113228|   15602.0|

## Data Preparation
Tahapan _data preparation_ yang dilakukan pada  _Content Based Filtering_ , yaitu sebagai berikut:
- Mengubah dataset menjadi _dataframe_ dengan menggunakan pandas.
- Menggabungkan beberapa file dengan fungsi _concatenate_
  
        users = np.concatenate((
        tags.userId.unique(),
        ratings.userId.unique(),
        ))
                                    
        users = np.sort(np.unique(users))
        print('number of all users: ', len(users))
  
Menggabungkan semua userId pada _users_, dalam hal ini userId yang ada pada file _tags_ dan _ratings_, kemudian mengambil nilai unik pada _users_ yang diurutkan. Maka didapati jumlah seluruh _users_ sebesar 610.

- Menggabungkan data dengan fungsi _merge_

      movies_names = pd.merge(ratings, movies[['movieId','title','genres']], on='movieId', how='left')

Melakukan _merge_(penggabungan) file _ratings_ dan file _movies_ dengan _left join_ (dalam hal ini mengambil data _ratings_(df kiri(_left_)) dan membuang baris dari df kanan yang tidak memiliki kecocokan dikolom kunci df kiri(_left_)) dalam hal ini berdasarkan _movieId_ dan disimpan di df _movies_names_.

Tabel 5. Hasil _Merge_ file _ratings dan file _movies_  

|userId|movieId|rating|timestamp|title|genres|
|---|---|---|---|---|---|
| 1|1|4.0|964982703|Toy Story (1995)|Adventure, Animation, Children, Comedy, Fantasy|
| 1|50|5.0|964982931|Usual Suspects, The (1995)	|Crime, Mystery, Thriller |
|...|...| ...|...|...|...|
|610|166534|4.0|1493848402|Split (2017)|Drama, Horror, Thriller|
|610|168250|5.|1494273047|Get Out (2017)|	Horror|

      movies_merge = pd.merge(movies_names, tags[['movieId','tag']], on='movieId', how='left')
      movies_merge

Kemudian dilanjutkan dengan menggabungkan _movies_names_ dan _tags_ dengan _left join_ berdasarkan _movieId_. Untuk hasinya bisa dilihat apada tabel dibawah ini.

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

- Mengecek _missing value_ dengan fungsi _isnull_.
  
  ![image](https://github.com/zashnf/Recommendation-System/assets/89719711/e778d21c-8d19-4860-b8e0-f92089ec6593)

  Gambar 1. Fungsi _isnull_

  Dapat dilihat pada gambar 1 bahwa terdapat _missing values_ sekitar 52549 pada kolom _tags_, maka bisa menggunakan _dropna_ untuk menghapus _missing value_.
- Membersihkan data dari nilai _null_ dengan menggunakan fungsi _dropna_.

        movies_merge = movies_merge.dropna()
  
  ![image](https://github.com/zashnf/Recommendation-System/assets/89719711/e1a8af7a-6729-45c2-a7c7-c53bbc7cec88)
 
  Gambar 2. Hasil setelah menggunakan dropna

  Dari gambar 2 sudah dapat dilihat tidak ada _missing valuenya_. Kemudian urutkan berdasarkan _moviesId_ setelah diurutkan terdapat sekitar 233213 baris dan 7 kolom

        movies_merge = movies_merge.sort_values('movieId', ascending=True)
  
- Membersihkan data dari duplikasi menggunakan fungsi _drop duplicates_.

        movies_merge = movies_merge.drop_duplicates('movieId')

Membuang data duplikat dengan _drop_duplicates_ pada _movieId_,sehingga _movieId_ tidak ada yang sama dan didapati data sebanyak 1554 baris dan 7 kolom.

- Mengkonversi _data series_ menjadi bentuk list, dengan _tolist_.

      id = movies_merge['movieId'].tolist()
      names = movies_merge['title'].tolist()
      genres = movies_merge['genres'].tolist()
  
- Membuat _dictionary_ untuk menentukan pasangan _key-value_ pada data yang telah disiapkan.

        new_movies = pd.DataFrame({
             'id': id,
             'names': names,
             'genres': genres
         })
         new_movies.head()

Tabel 7. Data _new_movies_

| **id**	|**names**|	**genres**|
|---|---|---|
| 1|Toy Story (1995)|Adventure, Animation, Children, Comedy, Fantasy|
| 2|Jumanji (1995)|Adventure Children, Fantasy  |
| 3|Grumpier Old Men (1995)| Comedy, Romance|
| 5|Father of the Bride Part II (1995)|	Comedy|
| 7|Sabrina (1995)|Comedy, Romance|

- Menggunakan TfidfVectorizer untuk melakukan perhitungan idf pada data genres.

      tf = TfidfVectorizer()
      tf.fit(new_movies['genres'])
      tf.get_feature_names_out()

  ![image](https://github.com/zashnf/Recommendation-System/assets/89719711/f0bbeedf-6b93-4ed2-8d9f-dc07a9ecb442)

 Gambar 3. Tfidf pada _genres_
 
- Melakukan _fit_ dan _transform_ TfidfVectorizer ke dalam bentuk matriks menggunaan fungsi _todense_.
  
          tfidf_matrix = tf.fit_transform(new_movies['genres'])
          tfidf_matrix.shape

Matriks yang dimiliki berukuran (1554,24) Dimana nilai 1554 merupakan ukuran data dan 24 adalah banyaknya kategori genres. Selanjutnya jika ingin melihat matriks tfidf untuk beberapa nama film (disini saya menampilkan 10 nama film)berdasarkan _genres_

         pd.DataFrame(
             tfidf_matrix.todense(),
             columns=tf.get_feature_names_out(),
             index=new_movies.names
         ).sample(22, axis=1).sample(10, axis=0)
         
Tabel 8. Matriks tfidf berdasarkan _genres_

|**names**|**crime**|**musical**|**war**|**drama**|**film**|...|**horror**|**documentary**|**action**|**comedy**|
|---|---|---|---|---|---|---|---|---|---|---|
|Igby Goes Down (2002)|0.000000|0.0|0.000000|0.600342|0.000000|...|0.0|0.0|0.000000|0.799743|
|Blood Simple (1984)|0.396795|0.0|0.000000|0.206813|0.632369|...|0.0|0.0|0.000000|0.000000|
|...|...|...|...|...|...|...|...|...|...|...|
|Apocalypse Now (1979)|0.000000|0.0|0.772897|0.304359|0.000000|...|0.0|0.0|0.556774|0.000000|
|Celebration, The (Festen) (1998)|0.000000|0.0|0.000000|1.000000|0.000000|...|0.0|0.0|0.000000|0.000000|

- Menggunakan _cosine similarity_  untuk menghitung derajat kesamaan antar film.
  
      cosine_sim = cosine_similarity(tfidf_matrix)
      cosine_sim_data = pd.DataFrame(cosine_sim, index=new_movies['names'], columns=new_movies['names'])
      print('Shape:', cosine_sim_data.shape)
      cosine_sim_data.sample(5, axis=1).sample(10, axis=0)
  
Tabel 9. Sample Data _cosine_

|names|Da Vinci Code, The (2006)|Lost Weekend, The (1945)|City of God (Cidade de Deus) (2002)|Chain Reaction (1996)|Love Me If You Dare (Jeux d'enfants) (2003)|
|---|---|---|---|---|---|
|Life Is Beautiful (La Vita è bella) (1997)|0.098907|0.291622|0.076756|0.000000|0.548325|
|Hurricane, The (1999)|0.339161|1.000000|0.263204|0.000000|0.531842|
|...|...|...|...|...|...|
|Devil's Rejects, The (2005)|0.000000|0.000000|0.514006|0.297762|0.000000|
|Good Copy Bad Copy (2007)|0.000000|0.000000|0.000000|0.000000|0.000000|

- Membuat fungsi rekomendasi dengan parameter:
  
        def movrecomm(movies_name, similarity_data=cosine_sim_data, items=new_movies[['names', 'genres']], k=3):
          index = similarity_data.loc[:,movies_name].to_numpy().argpartition(
              range(-1, -k, -1))
          closest = similarity_data.columns[index[-1:-(k+2):-1]]
          closest = closest.drop(movies_name, errors='ignore')
       return pd.DataFrame(closest).merge(items).head(k)

  dimana:
  - _movies_name_ = berisikan judul film.
   - _similarity_data_ = berisi tentang hasil perhitungan cosine.
   - _Items_ = fitur yang digunakan untuk kemiripan (disini menggunakan _names_ dan _genres_).
   - k = banyak rekomendasi yang ingin diberikan(menggunakan k sebanyak 3).
   

Tahapan _data preparation_ yang dilakukan pada  _Collaborative Filtering_ , yaitu sebagai berikut:
- Mengimport semua library yang dibutuhkan.
- Melakukan _encode_ fitur _user_ dan _movies_ kedalam indeks integer.

      userid = dataf['userId'].unique().tolist()
      user_to_user_encoded = {x: i for i, x in enumerate(userid)}
      user_encoded_to_user = {i: x for i, x in enumerate(userid)}
      
      movieid = dataf['movieId'].unique().tolist()
      movie_to_movie_encoded = {x: i for i, x in enumerate(movieid)}
      movie_encoded_to_movie = {i: x for i, x in enumerate(movieid)}

- Mapping userid dan moviesid ke dataframe.
  
      dataf['users'] = dataf['userId'].map(user_to_user_encoded)
      dataf['movies'] = dataf['movieId'].map(movie_to_movie_encoded)
  
- Mengubah nilai ratings menjadi float.
  
      dataf['ratings'] = dataf['rating'].values.astype(np.float32)
  
- Mengacak dataset agar distribusinya menjadi _random_.

      dataf = dataf.sample(frac=1, random_state=42)
  
Tabel 10. Sample dataf

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
  
- _Mapping_ data _user_ dan _movies_ menjadi satu _value_ pada variabel x, membuat vaiabel y untk membuat rating dari hasil.  Kemudian membagi data untuk training dan validasi, disini saya membagi data training sebanyak 70% dan data validasi sebanyak 30%.

      x = dataf[['users', 'movies']].values
      y = dataf['ratings'].apply(lambda x: (x - min_rating) / (max_rating - min_rating)).values
      train_indexes = int(0.7 * dataf.shape[0])
      x_train, x_val, y_train, y_val = (
          x[:train_indexes],
          x[train_indexes:],
          y[:train_indexes],
          y[train_indexes:]
      )
      print(x, y)

![image](https://github.com/zashnf/Recommendation-System/assets/89719711/5da1d722-c01e-4a0a-8dd1-1c81959e8fca)


Gambar 4. Hasil variabel x dan y

- Menghitung skor kecocokan dengan teknik _embedding_, kemudian melakukan compile terhadap model.
  
      model = RecommenderNet(num_users, num_movie, 50)
      model.compile(
          loss = tf.keras.losses.BinaryCrossentropy(),
          optimizer = keras.optimizers.Adam(learning_rate=0.001),
          metrics=[tf.keras.metrics.RootMeanSquaredError()]
      )
  
- Melakukan proses training dengan batch size 64 serta epoch 100 kali
  
      history = model.fit(
          x = x_train,
          y = y_train,
          batch_size = 64,
          epochs = 100,
          validation_data = (x_val, y_val)
      )
  
- Membuat visualisasi metrik dengan plot.
- Membuat rekomendasi _movies_.


## Modeling and Result
Pada proyek ini saya melakukan modeling dengan menggunakan algoritma _Content Based Filtering_ dan _Collaborative Filtering_. 

 _Content Based Filtering_ akan memberikan rekomendasi berdasarkan kemiripan _item_ yang dianalisis dari fitur yang dikandung oleh _item_ sebelumnya. Sedangkan _Collaborative Filtering_ merupakan metode yang memprediksi kegunaan _item_ yang dilihat dari _user_ sebelumnya.

### _Content Based Filtering_ 
Pada _Content Based Filtering_ rekomendasi yang dihasilkan itu berdasarkan dengan _item_ atau dalam hal ini film yang disukai sebelumnya, sehingga hasilnya akan menampilkan beberapa film yang memiliki _genres_ mirip dengan yang sebelumnya. Cara kerja dari model ini untuk mendapatkan hasil rekomendasi yaitu dengan mencari kemiripan bobot dari _term_ pada _bag of words_ hasil pre-processing _genres_ film dan nama film. Pembobotan dilakukan menggunakan metode TF-IDF yang telah dinormalisasi. Kemudian hasil pembobotan akan melalui tahap _cosine similarity_ untuk mencari kemiripan berdasarkan bobot dan diakhiri dengan _filtering_ berdasarkan _genre_. Misal disini saya memasukan nama film yang ada pada tabel 11. maka ia akan mencari _genres_ yang serupa dengan film yang dimasukan itu. Untuk hasil rekomendasi dari _Content Based Filtering_ dapat dilihat pada Tabel 12.

Tabel 11. Film yang disukai sebelumnya

|  **id** | **names** | **genres** |
|---------|-----------|------------|
|  168252 |Logan(2017)| Action, Sci-fi|

Dapat dilihat bahwa pada tabel 11 _user_ menyukai atau pernah menonton film Logan yang ber _genres_ _Action_ dan _Sci-fi_  . Maka sistem akan merekomendasikan beberapa film berdasarkan _genres_ yang disukai oleh _user_ yaitu _Action_ dan _Scifi_.

Tabel 12. Hasil Rekomendasi Film Logan(2017)

| **names** | **genres** | 
|-----------|------------|
| Superman II(1980)| Action, Sci-fi|
| Terminator : Judgement Day(1991)| Action, Sci-fi|  
| Planet of the apes(1968)| Action, Drama, Sci-fi|

Pada tabel 12 dapat dilihat bahwa _genres_ yang direkomendasikan oleh sistem tidak jauh berbeda dengan _genres_ film yang pernah ditonton sebelumnya.

###  _Collaborative Filtering_
Pada _Collaborative Filtering_ rekomendasi yang dihasilkan itu berdasarkan dari _rating_ yang diberikan oleh _user_ pada film film yang sebelumnya pernah dinilai(_rating_). Cara kerja dari  _Collaborative Filtering_ ini menggunakan _keras RecommerderNet model_ berbasis TensorFlow untuk mempelajari pola preferensi _user_ dan interaksi mereka dengan _movies_ . Model ini menggunakan _embedding_ untuk merepresentasikan _user_ dan _movies_. Dengan menggabungkan _embedding_ tersebut, maka dapat memprediksi preferensi _user_ terhadap _movies_ tertentu. Untuk hasilnya dapat dilihat pada gambar dibawah ini.

Tabel 13. Movies with high rating from user 599

| **names** | **genres**|
|-----------|----------|
|His Girl Friday(1940)| Comedy, Romance|
|Magnolia (1999)| Drama|
|Lost in Translation (2003) | Comedy, Drama, Romance| 
|Kill Bill: Vol 1 (2003)| Action, Crime, Thriller|

Pada tabel 13 diatas memuat beberapa film yang pernah dirating oleh _user_, _genres_ yang disukai-pun cukup beragam sehingga hasil rekomendasi yang didapat bisa dilihat pada Tabel 13. 

Tabel 14. Top 5 Movies Recommendation

| **names** | **genres**|
|-----------|----------|
|   More(1998)|   Animation, Drama, Sci-fi, IMAX|
|   Woman Under the Influence, A(1974)|   Drama|
|   Adam's Rib(1949)|   Comedy, Romance|
|   Into the Woods (1991)|   Comedy, Fantasy, Musical|
|   Wild Parrots of Telegraph Hill, The (2003)|   Documentary|  

Bisa dilihat pada tabel 15 diatas bahwa hasil rekomendasi film yang diberikan oleh sistem memiliki _genres_ yang tidak terlalu jauh berbeda dengan _genres_ film yang pernah dinilai(_rating_) sebelumnya.


## Evaluation
Metrik evaluasi yang digunakan pada _Content Based Filtering_ adalah metrik _precision_. Rumus dari metrik _precision_ dapat dilihat pada gambar

![precision](https://github.com/zashnf/Recommendation-System/assets/89719711/77468252-cf7b-441b-8a33-c6312deb8067)

Gambar 5. Rumus _Precision_

Mengacu pada contoh rekomendasi film di Tabel 11, diketahui bahwa Logan(2017) termasuk ke dalam kategori _Action_ dan _Sci-fi_. Maka dari 3 item yang direkomendasikan di Tabel 2, 3 item memiliki kategori _Action_, _Sci-fi_(_similar_). Artinya jika menggunakan rumus _precision_ hasil yang didapat sebesar 3/3 atau 100%.

Pada _Collaborative Filtering_ metrik evaluasi yang digunakan adalah RMSE (_Root Mean Squared Error_). RMSE (_Root Mean Squared Error_) merupakan metode pengukuran dengan mengukur perbedaan nilai dari prediksi sebuah model sebagai estimasi atas nilai yang diobservasi.Untuk menerapkannya bisa dengan menggunakan: 

 		metrics=[tf.keras/metrics.RootMeanSquaredError()]

Visualisasi model evaluasi metrik dapat dilihat pada gambar 6 dibawah ini.

![model](https://github.com/zashnf/Recommendation-System/assets/89719711/e605444b-3504-49f9-bf58-4b5af992a169)

Gambar 6. model evaluasi

Pada gambar 6. proses training model dengan batch size 64 serta epoch 100  diperoleh nilai error akhir (RMSE) di sekitar 0.1900 dan error pada data validasi sebesar 0.2081. Nilai tersebut cukup bagus untuk sistem rekomendasi.

### Kesimpulan
Kesimpulan dari pembuatan sistem rekomendasi film ini adalah baik secara _Content Based Filtering_ dan _Collaborative Filtering_ masing masing dapat merekomendasikan film berdasarkan kriterianya, dimana pada _Content Based Filtering_ rekomendasi yang dihasilkan berdasarkan film yang disukai oleh _user_ sedangkan pada _Collaborative Filtering_ hasil rekomendasinya berdasarkan _rating_ tinggi pada film yang pernah diberikan sebelumnya. Pada _Content Based Filtering_ evaluasi metrik _precision_ yang didapat sebesar 100% dan pada evaluasi metrik pada _Collaborative Filtering_ didapati nilai error akhir (RMSE) 0.1900 dan error pada data validasinya sebesar 0.2081.


### Referensi :

[1] 1. L. Lohar, G. Malve, T. Malviya, S. Sabnis, Movie Recommendation System. International Journal on Recent and Innovation Trends in Computing and Communication. 9, 13–16 (2021).



