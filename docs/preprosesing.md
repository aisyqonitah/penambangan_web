# Preprocessing

### Pengertian

`Text Preprocessing` adalah tahapan dimana kita melakukan seleksi data agar data yang akan kita olah menjadi lebih terstruktur. Tahap Text Preprocessing adalah tahapan dimana aplikasi melakukan seleksi data yang akan diproses pada setiap dokumen. Proses preprocessing ini meliputi :

1. Case Folding yaitu mengkonversi keseluruhan teks dalam dokumen menjadi suatu bentuk standar (biasanya huruf kecil atau lowercase). 
2. Tokenisasi yaitu pemotongan string input berdasarkan tiap kata yang menyusunnya.
3. Filtering yaitu tahap mengambil kata-kata penting dari hasil token. Bisa menggunakan algoritma **stoplist** (membuang kata kurang penting) atau **wordlist** (menyimpan kata penting).
4. Stemming yaitu memperkecil jumlah indeks yang berbeda dari suatu dokumen, juga untuk melakukan pengelompokan kata-kata lain yang memiliki kata dasar dan arti yang serupa namun memiliki bentuk atau form yang berbeda karena mendapatkan imbuhan yang berbeda.



### **Tokenisasi**

Digunakan untuk memecah suatu kalimat menjadi beberapa kata. Jika ada kalimat "nama saya adalah sisi" ingin dipecah menjadi 2 suku kata maka hasilnya "nama saya" "adalah aisy" dan jika ingin dipecah menjadi 1 suku kata akan menjadi "nama" , "saya" , "adalah" , "aisy". Ini merupakan code yang dapat digunakan jika menggunakan tokenisasi

```python
def tokenisasi (txt,ngram=1):
    token=[]
    start=0
    end=ngram
    txtSplit=txt.split() 
    while end <=len (txtSplit):
        tmp=txtSplit[start:end]
        frase=''
        for i in tmp:
            frase += i+' '
        token.append(frase)
        end+=1;start+=1;
    return token
```



### **Filtering** dan Stemming

Code dibawah ini digunakan untuk membuang kata yang tidak penting dan  mengelompokan kata-kata yang memiliki kata dasar dan arti yang serupa namun memiliki bentuk atau form yang berbeda karena mendapatkan imbuhan yang berbeda.

```python
def preprosesing(txt):
    SWfactory = StopWordRemoverFactory()
    stopword = SWfactory.create_stop_word_remover()
    Sfactory = StemmerFactory()
    stemmer = Sfactory.create_stemmer()
    hasil = ''
    for i in txt.split():
        if i[:-2].isalpha():
            stop = stopword.remove(i)
            stem = stemmer.stem(stop)
            hasil += stem  + ' '
    return hasil

```

```python
SWfactory = StopWordRemoverFactory()
stopword = SWfactory.create_stop_word_remover()
```

Code diatas digunakan memanggil StopWordRemoverFactory yang ada pada library Sastrawi untuk membuang kata yang tidak penting yang sudah didefinisakan di library Sastrawi.

```python
Sfactory = StemmerFactory()
stemmer = Sfactory.create_stemmer()
```

Code diatas digunakan untuk mengambil StemmerFactory yang ada pada library Sastrawi yang digunakan untuk mengambil kata dasar yang sudah didefinisikan  pada library Sastrawi.

```python
for i in txt.split():
        if i[:-2].isalpha():
            stop = stopword.remove(i)
            stem = stemmer.stem(stop)
            hasil += stem  + ' '
    return hasil
```

Code diatas digunakan untuk nembuang semua karakter selain alfabet.

