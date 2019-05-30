# Import Data ke CSV

```
def write_csv(nama_file, isi, tipe='w'):
	'tipe=w; write; tipe=a; append;'
	with open(nama_file, mode=tipe) as tbl:
		tbl_writer = csv.writer(tbl, delimiter=',', quotechar='"', quoting=csv.QUOTE_MINIMAL)
		for row in isi:
			tbl_writer.writerow(row)
            
```

Disini kita membuat fungsi write csv agar fungsinya dapat digunakan berulang-ulang dan juga data yang kita crawl dapat disimpan dalam bentuk csv agar lebih memudkan jika ingin diakses.

