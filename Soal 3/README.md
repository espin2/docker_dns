# konfigurasi Wordpress dan web sederhana
pada soal ini membuat wordpress menggunakan docker-compose dan juga membuat sebuah web sederhana yang nantinya akan dijadian iamge docker lalu dijalankan menjadi container.
## Docker Server
### Wordpress

1.buat folder dan masuk ke folder wordpress
```mkdir wordpress && cd wordpress``` <br>

2.edit file docker-compose.yaml<br>
```nano docker-compose.yaml```

```

services:
   db:
     image: mysql:latest
     volumes:
       - db:/var/lib/mysql
     restart: always
     environment:
         MYSQL_ROOT_PASSWORD: admin123
         MYSQL_DATABASE: wordpress
         MYSQL_USER: wordpress
         MYSQL_PASSWORD: admin123
   wordpress:
     depends_on:
       - db
     image: wordpress
     ports:
       - "8080:80"
     volumes:
       - wordpress:/var/www/html
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: admin123
       WORDPRESS_DB_NAME: wordpress

volumes:
  db:
  wordpress:

```
3.jalankan docker-compose agar container terbuat. <br>
```docker-compose up -d```<br>

### Web Sederhana
1.buat folder dan masuk ke folder myweb1 <br>
```mkdir myweb1 && cd myweb1```<br>

2.buat tulisan web sederhana di index.html <br>
```echo “Selamat Datang di Website Saya” >> index.html```<br>

3.edit file Dockerfile <br>
```nano Dockerfile``` 
```
FROM httpd:latest
WORKDIR /usr/local/apache2/htdocs
COPY index.html /usr/local/apache2/htdocs
```
4.build image dari Dockerfile yang telah dibuat <br> 
```docker image build -t myweb1 .``` <br>

5.verifikasi images telah terbuat<br>
```docker images ls``` <br>

6.jalankan container menggunakan image yang telah dibuat<br>
```docker run -d -p 8081:80 myweb1``` <br>
