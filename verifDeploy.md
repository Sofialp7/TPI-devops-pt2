deberia llamarse este archivo health-checks

# 3. Verificación de Deploy

Este documento detalla los comandos necesarios para verificar el estado y la accesibilidad de cada microservicio una vez que el despliegue en el servidor remoto ha finalizado correctamente.

**Requisito HTTPS:** El sistema completo debe ser accesible a través de **HTTPS** en el puerto 8080 del host (`https://<IP_Servidor>:8080`).

---

## A. Verificación de Contenedores

**Comando en el servidor remoto (vía SSH):**

```bash
docker compose ps