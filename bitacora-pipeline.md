deberia llamarse solo pipeline

# 2. Bitácora de Pipeline Ejecutado

Este archivo documenta la ejecución exitosa de nuestro pipeline de CI/CD implementado en GitLab. La finalidad es demostrar la correcta automatización de los procesos de seguridad, build y deploy, tal como lo exige la consigna.

Las imágenes (capturas de pantalla de GitLab CI/CD) deben colocarse en la carpeta `imgs/` en la raíz del repositorio.

---

## Pipeline Exitoso

A continuación, se adjunta una captura de la vista general del pipeline ejecutado con éxito en la rama `main`, demostrando que todas las stages obligatorias completaron su ejecución sin fallos.



---

## Detalle de Jobs por Stage

### Stage: `secrets_scan` (Punto 1.1)
* **Job:** `secrets_scan`
* [cite_start]**Propósito:** Escaneo de secretos sensibles (con `detect-secrets`) en el código fuente, excluyendo archivos como `.env` [cite: 14] y el certificado privado (`web/certs/nginx.key`).
* **Artefactos Generados:** `detect-secrets-report.txt`
* 

### Stage: `linting` (Punto 1.2 y 1.4 - IaC)
* **Job:** `checkov_docker_lint`
* [cite_start]**Propósito:** Análisis estático de Infraestructura como Código (IaC) en `Dockerfiles` y `docker-compose.yml` utilizando **Checkov** [cite: 1][cite_start], verificando buenas prácticas de seguridad (ej. `HEALTHCHECK` [cite: 1] [cite_start]y uso de usuarios sin privilegios [cite: 1]).
* **Artefactos Generados:** `docs/checkov-report.json`
* 

* **Jobs:** `lint_api_code`, `lint_dataapi_code`
* **Propósito:** Escaneo y linting del código fuente de las APIs de Python utilizando **Flake8** para mantener la calidad y el estilo del código.
* 

### Stage: `build` (Punto 1.3 y 1.4 - Vulnerabilidades)
* **Jobs:** `build_db`, `build_api`, `build_data_api`, `build_frontend`
* **Propósito:**
    1.  **Construcción** de la imagen Docker de cada microservicio.
    2.  [cite_start]**Escaneo de Vulnerabilidades** con **Trivy** [cite: 1] sobre la imagen recién construida.
    3.  **Push** de la imagen (con tag `$CI_COMMIT_SHORT_SHA` y `latest`) al Registry del LabSis.
* **Artefactos Generados:** `docs/trivy-*-report.json`
* 

### Stage: `deploy` (Punto 1.5)
* **Job:** `deploy_server`
* **Propósito:** Despliegue continuo en el servidor remoto (SSH). Realiza `docker compose pull` y luego `docker compose up -d --force-recreate` para actualizar los contenedores.
* [cite_start]**Verificación Clave:** El log del job debe mostrar la salida de `docker compose ps` en el servidor remoto, confirmando que los contenedores están `Up` [cite: 1, 2] [cite_start]y que el proxy expone **443 (HTTPS)** a través del puerto 8080[cite: 1].
*