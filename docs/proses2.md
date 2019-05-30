# Proses Web Crawling

### **Halaman Web**

Untuk website yang akan di crawl datanya yaitu:

> <https://palominobag.com/best-seller>



### **Proses Crawling**

Untuk mengakses halaman web dan tag html digunakan library:

```python
import pandas as pd
import requests
from bs4 import BeautifulSoup
import networkx as nx
import matplotlib.pyplot as plt
```



1. Untuk mengambil semua link yang ada pada website:

```python
def getAllLinks(src):
        page = requests.get(src)
        soup = BeautifulSoup(page.content, 'html.parser')
        tags = soup.findAll("a")
        links = []
        for tag in tags:
            try:
                link = tag['href']
                if not link in links and 'http' in link:
                    links.append(link)
            except KeyError:
                pass
        return links
    except:
        return list()
```

Code diatas digunakan untuk mendapatkan semua link pada halaman web tersebut. 



2. Memfilter link:

```python
def simplifiedURL(url):
    # cek 1 : www
    if "www." in url:
        ind = url.index("www.")+4
        url = url[ind:]
    # cek 2 : http/https
    if not "http" in url:
        url = "http://"+url
    # cek 3 : tanda / di akhir
    if url[-1] == "/":
        url = url[:-1]
    return url
```
- filter 1

  Digunakan untuk menambahkan "http://" pada link dengan index awal "www."

- filter 2

  Digunakan untuk menambahkan "http://" pada link

- filter 3

  Digunakan untuk mengecek "/" di akhir link atau link[-1]="/", maka ambil link dari index link[-1] sampai index awal link atau link=link[:-1].



3. Crawler Data:

```python
def crawl(url, max_deep,  show=False, deep=0, done=[]):
    global edgelist
    deep += 1
    url = simplifiedURL(url)
    if not url in done:
        links = getAllLinks(url)
        done.append(url)
        if show:
            if deep == 1:
                print(url)
            else:
                print("|", end="")
                for i in range(deep-1): print("--", end="")
                print("(%d)%s" %(len(links),url))
            
        for link in links:
            link = simplifiedURL(link)
            edge = (url,link)
            if not edge in edgelist:
                edgelist.append(edge)
            if (deep != max_deep):
                crawl(link, max_deep, show, deep, done)
```



`if not url in cek` digunakan untuk mengecek apakah link tersebut sudah pernah dicrawl atau tidak, jika pernah maka link tidak perlu dimasukkan pada *edgelist*, jika belum maka masukkan link pada *edgelist*. Hasil dari proses *crawl* ini akan menghasilkan *edgelist* yang berisi link yang sudah difilter.



4. Menamanggil Crawler Data:

```python
root = "https://palominobag.com/best-seller"
nodelist = [root]
edgelist = []
crawl(root, 3, show=True)
edgelistFrame = pd.DataFrame(edgelist, None, ("From", "To"))
```

