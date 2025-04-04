# Laboratorul 05: Interacțiunea între containere Docker

## Scopul lucrării

Scopul acestei lucrări este de a învăța cum să gestionăm interacțiunea între mai multe containere Docker. La finalul acestei lucrări, studentul va înțelege cum să creeze și să configureze containere pentru a lucra împreună, utilizând o rețea internă Docker și volume montate.

## Sarcina

Sarcina principală este de a crea o aplicație PHP pe baza a două containere Docker:
1. **Containerul backend** pe bază de PHP-FPM.
2. **Containerul frontend** pe bază de Nginx.

Aceste containere vor interacționa printr-o rețea internă Docker și vor monta directorul care conține site-ul PHP într-un volum.

## Descrierea efectuării lucrării

1. **Crearea directorului `containers05`:**
   - Am creat un director `containers05` pe care l-am copiat pe computerul local.

2. **Crearea directorului `mounts/site`:**
   - Am creat un subdirector `mounts/site` unde am copiat site-ul PHP creat în cadrul cursului de Programare PHP.

3. **Crearea fișierului `.gitignore`:**
   - Am creat fișierul `.gitignore` în rădăcina proiectului și am adăugat următoarele linii pentru a ignora fișierele din directorul `mounts/site`:
     ```
     # Ignore files and directories
     mounts/site/*
     ```

4. **Configurarea Nginx:**
   - Am creat fișierul `nginx/default.conf` în directorul `containers05` cu următoarea configurație:
     ```nginx
     server {
         listen 80;
         server_name _;
         root /var/www/html;
         index index.php;
         location / {
             try_files $uri $uri/ /index.php?$args;
         }
         location ~ \.php$ {
             fastcgi_pass backend:9000;
             fastcgi_index index.php;
             fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
             include fastcgi_params;
         }
     }
     ```

5. **Crearea rețelei interne:**
   - Am creat o rețea internă Docker pentru a permite comunicarea între containerele backend și frontend.
   ```
   docker network create internal
   ```
6. **Crearea containerului backend:**
   - Am creat containerul `backend` pe baza imaginii `php:7.4-fpm`, configurând directorul `mounts/site` să fie montat în `/var/www/html` și conectându-l la rețeaua internă.
```
docker run -d --name backend -v C:\Users\user\Desktop\containers06\mounts\site:/var/www/html --network internal php:7.4-fpm
```

7. **Crearea containerului frontend:**
   - Am creat containerul `frontend` pe baza imaginii `nginx:1.23-alpine`, configurând directorul `mounts/site` să fie montat în `/var/www/html`, fișierul `nginx/default.conf` să fie montat în `/etc/nginx/conf.d/default.conf`, și portul 80 al containerului să fie expus pe portul 80 al gazdei.
   ```
   docker run -d --name frontend -v C:\Users\user\Desktop\containers06\mounts\site:/var/www/html -v C:\Users\user\Desktop\containers06\nginx\default.conf:/etc/nginx/conf.d/default.conf -p 80:80 --network internal nginx:1.23-alpine
   ```

8. **Testarea site-ului:**
   - După pornirea containerelor, am deschis browserul și am accesat site-ul la adresa `http://localhost`. Dacă pagina de pornire Nginx era afișată, am reîncărcat pagina pentru a vedea aplicația PHP.

## Răspunsuri la întrebări

1. **În ce mod în acest exemplu containerele pot interacționa unul cu celălalt?**
   - Containerele pot interacționa între ele printr-o rețea internă Docker, care le permite să comunice prin adrese IP interne. În acest exemplu, containerul frontend (Nginx) poate trimite cereri către containerul backend (PHP-FPM) pe portul 9000.

2. **Cum văd containerele unul pe celălalt în cadrul rețelei interne?**
   - În cadrul rețelei interne Docker, containerele pot comunica între ele folosind numele containerului ca hostname. De exemplu, containerul frontend poate accesa containerul backend folosind hostname-ul `backend`.

3. **De ce a fost necesar să se suprascrie configurarea nginx?**
   - Configurarea Nginx a fost suprascrisă pentru a specifica cum Nginx ar trebui să trateze fișierele PHP. Fișierul `default.conf` configurează Nginx să trimită cererile pentru fișiere PHP către containerul backend (PHP-FPM) pe portul 9000.

## Concluzii

Prin finalizarea acestei lucrări, am învățat cum să configurăm și să gestionăm interacțiunea între containere Docker, să folosim volume montate și să creăm rețele interne. De asemenea, am dobândit experiență în configurarea serverului web Nginx pentru a lucra cu PHP-FPM într-un mediu multi-container.
