# containers06

docker run -d --name backend -v C:\Users\user\Desktop\containers06\mounts\site:/var/www/html --network internal php:7.4-fpm


docker run -d --name frontend -v C:\Users\user\Desktop\containers06\mounts\site:/var/www/html -v C:\Users\user\Desktop\containers06\nginx\default.conf:/etc/nginx/conf.d/default.conf -p 80:80 --network internal nginx:1.23-alpine
