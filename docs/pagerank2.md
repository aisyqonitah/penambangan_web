# Pagerank

### Pengertian

`PageRank (PR)` adalah algoritma yang digunakan oleh Google Search untuk memberi peringkat situs web dalam hasil mesin pencari mereka. PageRank dinamai Larry Page, salah satu pendiri Google. PageRank adalah cara mengukur pentingnya halaman situs web.

Menurut Google:

`PageRank` bekerja dengan menghitung jumlah dan kualitas tautan ke suatu halaman untuk menentukan perkiraan kasar seberapa penting situs web itu.Asumsi yang mendasarinya adalah bahwa situs web yang lebih penting cenderung menerima lebih banyak tautan dari situs web lain.

Untuk menghitung page rank kita gunakan code berikut :

```python
damping = 0.85
max_iterr = 100
error_toleransi = 0.0001
pr = nx.pagerank(g, alpha = damping, max_iter=max_iterr, tol=error_toleransi)
```

```python
nodelist = g.nodes
label= {}
data = []
for i, key in enumerate(nodelist):
    data.append((pr[key], key))
    label[key]=i
```

Pagerank yang sudah kita hitung ditampung dalam list data, dengan key adalah node (link) dari website yang di crawl.

Mengurutkan pagerank dari yang terbesar sampai yang terkecil:

```python
urut = data.copy()
for x in range(len(urut)):
    for y in range(len(urut)):
        if urut[x][0] > urut[y][0]:
            urut[x],urut[y] = urut[y],urut[x]
urut = pd.DataFrame(urut, None, ("PageRank", "Node"))
print(urut)
```


