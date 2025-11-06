# Etapa 1: Build de la aplicación (asumiendo que es React/Angular/Vue/etc.)
# Uso de node como ejemplo. Si usas Flask para servir estáticos, ajusta la base.
FROM node:20-alpine AS builder

WORKDIR /app
COPY ./web/package*.json ./
RUN npm install
COPY ./web/ .
# Asumo que el frontend se construye aquí (ej: 'npm run build')
# Ajusta este comando a tu proyecto real si no usas 'build'
RUN npm run build 

# Etapa 2: Imagen final con Nginx
FROM nginx:alpine

# Instalar OpenSSL para asegurarse de que Nginx puede manejar SSL (aunque la imagen base ya podría tenerlo)
# y para que los comandos de verificación de certificados funcionen si se necesitan.
RUN apk add --no-cache openssl

# Configuración y Certificados
# 1. Copiar la configuración de Nginx (Ahora con SSL/TLS)
COPY ./web/nginx.conf /etc/nginx/conf.d/default.conf

# 2. Copiar los certificados SSL/TLS (auto-firmados en local)
# Debes ejecutar el comando 'openssl req...' antes de hacer el build.
COPY ./web/certs/nginx.crt /etc/nginx/certs/
COPY ./web/certs/nginx.key /etc/nginx/certs/

# 3. Copiar los archivos estáticos generados en la etapa de build
# Asumo que la carpeta de salida es 'build' o 'dist'.
COPY --from=builder /app/build /usr/share/nginx/html

# Ajuste: Exponer 443 (HTTPS) en el contenedor en lugar de 80
EXPOSE 443

# El comando por defecto de Nginx ya inicia el servidor
CMD ["nginx", "-g", "daemon off;"]