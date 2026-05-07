# --- ETAPA 1: Construcción (Builder) ---
FROM node:20-alpine AS builder

WORKDIR /app

# Copiamos los archivos de dependencias primero para aprovechar la caché
COPY package.json package-lock.json ./

# Instalamos las dependencias
RUN npm install

# Copiamos el resto del código (componentes, configuraciones de Tailwind/Vite, etc.)
COPY . .

# Compilamos la aplicación para producción (esto genera la carpeta /dist)
RUN npm run build

# --- ETAPA 2: Producción con Nginx ---
FROM nginx:alpine

# Copiamos los archivos compilados desde la etapa anterior hacia la carpeta pública de Nginx
COPY --from=builder /app/dist /usr/share/nginx/html

# Exponemos el puerto 80 (por defecto de Nginx)
EXPOSE 80

# Iniciamos Nginx
CMD ["nginx", "-g", "daemon off;"]