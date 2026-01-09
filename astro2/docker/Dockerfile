# Usamos Node para construir el sitio
FROM node:lts-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Usamos un servidor ligero para servir el contenido estático
FROM busybox:1.35
WORKDIR /app
# Copiamos los archivos generados por Astro (por defecto en dist/)
COPY --from=build /app/dist .
# Servimos en el puerto 8080 (interno del contenedor)
CMD ["busybox", "httpd", "-f", "-v", "-p", "8080"]
