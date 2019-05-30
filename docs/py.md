# Code Lengkap Python

Dibawah ini merupakan code lengkap crawler data

```python
import requests 
from bs4 import BeautifulSoup
import sqlite3
import csv
from Sastrawi.Stemmer.StemmerFactory import StemmerFactory
from Sastrawi.StopWordRemover.StopWordRemoverFactory import StopWordRemoverFactory
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.cluster import KMeans
from sklearn.metrics import silhouette_samples, silhouette_score
import numpy as np
import skfuzzy as fuzz

def crawl(src):
    global c
    try :
        page = requests.get(src)
        soup = BeautifulSoup(page.content, 'html.parser')
        links = soup.findAll(class_='product-name')
        

for i in links:
        try :
            url = i.find ('a')['href']
            print(url)
            #src = each_link['href']
            page = requests.get(url)
            soup = BeautifulSoup(page.content, 'html.parser')
            konten = soup.find(class_='grid_12 alpha omega')
            judul = konten.find(class_='product-name no-rel').getText()
            harga = konten.find(class_='price').getText()
            detil = konten.find(class_='box-collateral').getText(' ')

        conn.execute("INSERT INTO BOOK \
                        VALUES (?, ?, ?)", (judul, harga, detil));
    except AttributeError:
        print("")
conn.commit()

except ValueError:
    print('Download selesai')

def write_csv(nama_file, isi, tipe='w'):
    'tipe=w; write; tipe=a; append;'
    with open(nama_file, mode=tipe) as tbl:
        tbl_writer = csv.writer(tbl, delimiter=',', quotechar='"', quoting=csv.QUOTE_MINIMAL)
        for row in isi:
            tbl_writer.writerow(row)
            
def preprosesing(txt):
    SWfactory = StopWordRemoverFactory()
    stopword = SWfactory.create_stop_word_remover()
    Sfactory = StemmerFactory()
    stemmer = Sfactory.create_stemmer()
    hasil = ''
    for i in txt.split():
        if i[:-2].isalpha():
            # Menghilangkan Kata tidak penting
            stop = stopword.remove(i)
            stem = stemmer.stem(stop)
            hasil += stem  + ' '
    return hasil

#VSM

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

def countWord(txt):
    d = dict()
    for i  in txt.split():
        if d.get(i) == None:
            d[i] = txt.count(i)
    return d

def add_row_VSM(d):
    VSM.append([])
    for i in VSM[0]:
        if d.get(i) == None:
            VSM[-1].append(0)
        else :
            VSM[-1].append(d.pop(i));
    for i in d:
        VSM[0].append(i)
        for j in range(1, len(VSM)-1):
            VSM[j].insert(-2,0)
        VSM[-1].append(d.get(i))

def pearsonCalculate(data, u,v):
    "i, j is an index"
    atas=0; bawah_kiri=0; bawah_kanan = 0
    for k in range(len(data)):
        atas += (data[k,u] - meanFitur[u]) * (data[k,v] - meanFitur[v])
        bawah_kiri += (data[k,u] - meanFitur[u])**2
        bawah_kanan += (data[k,v] - meanFitur[v])**2
    bawah_kiri = bawah_kiri ** 0.5
    bawah_kanan = bawah_kanan ** 0.5
    return atas/(bawah_kiri * bawah_kanan)

def meanF(data):
    meanFitur=[]
    for i in range(len(data[0])):
        meanFitur.append(sum(data[:,i])/len(data))
    return np.array(meanFitur)

def seleksiFiturPearson(katadasar,data, threshold):
    global meanFitur
    meanFitur = meanF(data)
    u=0
    while u < len(data[0]):
        dataBaru=data[:, :u+1]
        meanBaru=meanFitur[:u+1]
        kataBaru=katadasar[:u+1]
        v = u
        while v < len(data[0]):
            if u != v:
                value = pearsonCalculate(data, u,v)
                if value < threshold:
                    dataBaru = np.hstack((dataBaru, data[:, v].reshape(data.shape[0],1)))
                    meanBaru = np.hstack((meanBaru, meanFitur[v]))
                    kataBaru = np.hstack((kataBaru, katadasar[v]))
            v+=1
        data = dataBaru
        meanFitur=meanBaru
        katadasar=kataBaru
        if u%50 == 0 : print("proses : ", u, data.shape)
        u+=1
    return data,kataBaru

conn = sqlite3.connect('ba.db')
c = 1
choice = input("Update data? Y/N").lower()
if choice == 'y':
    conn.execute('drop table if exists BOOK')
    conn.execute('''CREATE TABLE BOOK
                 (JUDUL     TEXT     NOT NULL,
                 HARGA  TEXT     NOT NULL,
                 DESKRIPSI  TEXT     NOT NULL);''')
    url=[str(i) for i in range(0,5)]
    for a in url:
        src= ('https://palominobag.com/best-seller?limit=36&p='+a)
        crawl (src)
        conn.commit()
        

print("Building VSM...")
cursor = conn.execute("SELECT * from BOOK")
cursor = cursor.fetchall()
#cursor = cursor[:10]
pertama = True
corpus = list()
c=1
for row in cursor:
    print ('Proses : %.2f' %((c/len(cursor))*100) + '%'); c+=1
    txt = row[2]
    token = tokenisasi(txt,1)
    cleaned=' ' 
    for i in token:
        cek= preprosesing(i)
        if cek !=None:
            cleaned+=cek+' '
    cleaned = cleaned[:-1] 
corpus.append(cleaned)
d = countWord(cleaned)
if pertama:
    pertama = False
    VSM = list((list(), list()))
    for key in d:
        VSM[0].append(key)
        VSM[1].append(d[key])
else:
    add_row_VSM(d)

write_csv("bow_manual.csv", VSM)

vectorizer = CountVectorizer(min_df=1, ngram_range=(1,1))
BoW_matrix = vectorizer.fit_transform(corpus)
write_csv("bow_lib1.csv", [vectorizer.get_feature_names()])
write_csv("bow_lib1.csv", BoW_matrix.toarray())

#TF-IDF
vectorizer = TfidfVectorizer()
tfidf_matrix = vectorizer.fit_transform(corpus)
feature_name = vectorizer.get_feature_names()
write_csv("tfidf1.csv", [feature_name])
write_csv("tfidf1.csv", tfidf_matrix.toarray(), 'a')

#Seleksi Fitur
batas=0.8
fiturBaru,katabaru = seleksiFiturPearson(feature_name,tfidf_matrix.toarray(), batas)
write_csv("Seleksi_Fitur.csv",[katabaru])
write_csv("Seleksi_Fitur.csv",fiturBaru,'a')

#Cluster
kmeans = KMeans(n_clusters=5, random_state=0).fit(fiturBaru)
write_csv("Kluster_label.csv", [kmeans.labels_])
s_avg = silhouette_score(tfidf_matrix, kmeans.labels_, random_state=10)
print(s_avg)
for i in range(len(kmeans.labels_)):
    print("Doc %d =>> cluster %d" %(i+1, kmeans.labels_[i]))
```

