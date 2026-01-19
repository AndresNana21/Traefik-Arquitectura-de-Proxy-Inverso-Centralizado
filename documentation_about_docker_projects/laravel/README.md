---

# 🐘 Despliegue de Laravel con Traefik

Esta guía detalla los pasos para crear un proyecto de **Laravel 12** y configurarlo para funcionar bajo la arquitectura de proxy inverso centralizado con **Traefik**.

---

## 1. Creación del Proyecto
Utilizaremos una imagen temporal de Composer para generar el proyecto sin necesidad de instalar dependencias locales en el sistema.

```bash
docker run --rm \
    -u $(id -u):$(id -g) \
    -v $(pwd):/app \
    composer:latest \
    composer create-project laravel/laravel:^12.0 mi-proyecto-laravel

```

---

## 2. Configuración de Permisos (Linux)

Dado que los archivos son creados por el contenedor, debemos ajustar los permisos para que Laravel pueda escribir en el almacenamiento y las bases de datos SQLite:

```bash
sudo chmod -R 777 storage bootstrap/cache
sudo chmod -R 777 database/

```

---

## 3. Configuración de Docker

Crea una carpeta llamada `Docker` en la raíz de tu proyecto para organizar los archivos de configuración:

```bash
mkdir Docker && cd Docker
touch docker-compose.yml Dockerfile nginx.conf

```

### 3.1 Docker Compose (`Docker/docker-compose.yml`)

Define los servicios de la aplicación y la conexión con Traefik.

```yaml
name: laravel-2

services:
  # El "motor" de PHP-FPM
  app:
    build:
      context: ..
      dockerfile: Docker/Dockerfile
    container_name: laravel-2-app
    volumes:
      - ..:/var/www
    networks:
      - web

  # El servidor Web (Nginx)
  webserver:
    image: nginx:alpine
    container_name: laravel-2-web
    volumes:
      - ..:/var/www
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
    networks:
      - web
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.laravel-2.rule=Host(`laravel-2.localhost`)"
      - "traefik.http.routers.laravel-2.entrypoints=web"
      - "traefik.http.services.laravel-2.loadbalancer.server.port=80"
      - "traefik.docker.network=web"

networks:
  web:
    external: true

```

> **Nota:** Cambia `laravel-2` por el nombre de tu proyecto. Asegúrate de que el Host coincida con el dominio que deseas usar.

### 3.2 Dockerfile (`Docker/Dockerfile`)

Personalizamos la imagen de PHP para incluir las extensiones necesarias de Laravel.

```dockerfile
FROM php:8.4-fpm

# Instalar dependencias del sistema
RUN apt-get update && apt-get install -y \
    git curl libpng-dev libonig-dev libxml2-dev zip unzip

# Instalar extensiones de PHP
RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd

WORKDIR /var/www
COPY . /var/www

# Ajustar propiedad de carpetas críticas
RUN chown -R www-data:www-data /var/www/storage /var/www/bootstrap/cache

EXPOSE 9000
CMD ["php-fpm"]

```

### 3.3 Configuración de Nginx (`Docker/nginx.conf`)

Configura cómo Nginx procesa las peticiones PHP y las comunica con el contenedor `app`.

```nginx
server {
    listen 80;
    index index.php index.html;
    root /var/www/public;

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass laravel-2-app:9000; # Debe coincidir con 'container_name' de la app
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
}

```

---

## 4. Despliegue

Una vez configurados los archivos, sitúate dentro de la carpeta `Docker` y levanta los servicios:

```bash
docker compose up -d

```

---

**⚠️ Importante:** El nombre definido en `fastcgi_pass` dentro de `nginx.conf` debe ser exactamente igual al `container_name` del servicio `app` en tu `docker-compose.yml`.


---

