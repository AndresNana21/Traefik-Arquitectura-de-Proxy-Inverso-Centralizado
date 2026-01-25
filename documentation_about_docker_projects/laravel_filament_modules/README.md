# Documentación sobre como configurar un proyecto de laravel con modules filament desde 0


## tomar en cuneta que 
* se tiene una carpeta llamada Docker en el mismo nivel de este README.md para que utilices estos archivos y no tengas que crearlos desde 0.

* en la documentaión se encuneta que palabras claves tienes que cambiar para que tus contenedores permitan desplegar tu proyecto.

* puedes encontrar comandos que puedes reutilizar en ocaciones especificas cuando estes programando en tu proyecto.

---

## pasos realizados

## (1) crear un proyecto de laravel

para crear el proyecto utilizamos una imagen de composer de la cual sacaremos un contenedor temporal que nos permitira especifcar que queremos crea.

Tomar en cuneta que puedes modificar la verción del proyecto en ^12.0 o puedes modificar el nombre del proyecto en mi-proyecto-laravel.

```Comand

docker run --rm \
    -u $(id -u):$(id -g) \
    -v $(pwd):/app \
    composer:latest \
    composer create-project laravel/laravel:^12.0 mi-proyecto-laravel

```
### (1.1 ingresar al proyecto

en los siguentes pasos necesitamos estar en la raiz del proyecto por lo que necesitas usar este comando para ingresar en el.

toma en cuneta que si cambiaste el nombre del proyecto tambien necesitas modificar el comando.

```Comand
cd mi-proyecto-laravel 
```

## ( 2 )  crear los archivos para Dockerfile , docker-compose.yml y nginx.conf

en la raiz de el proyecto de documentación encontraras una carpeta para que la copies y la reutilices en este proyecto nuevo de laravel.

### (2.1) copiar la carpeta Docker desde tu hubicacíon actual.

recuerda cambia la direccion de el proyecto de documentación.

```Comand
cp -r ~/Traefik-projects-docs/Docker .
```
### (2.2) crear los archivos de manera manual


Crear la carpeta Docker

```Comand
  mkdir Docker
```

Crear los archivos

* Dockerfile
```Comand
cat <<'EOF' > Dockerfile
FROM php:8.4-fpm

# Instalar dependencias del sistema y librerías necesarias para extensiones de PHP
RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip \
    libicu-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    libzip-dev && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# --- Instalar Composer ---
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Configurar e instalar extensiones de PHP necesarias para Laravel y Filament 4
RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install \
    pdo_mysql \
    mbstring \
    exif \
    pcntl \
    bcmath \
    gd \
    intl \
    opcache \
    zip

# Directorio de trabajo
WORKDIR /var/www

# Copiar el proyecto
COPY . /var/www

# Dar permisos a la carpeta storage y bootstrap/cache
RUN mkdir -p /var/www/storage /var/www/bootstrap/cache \
    && chown -R www-data:www-data /var/www/storage /var/www/bootstrap/cache

EXPOSE 9000
CMD ["php-fpm"]
EOF
```


