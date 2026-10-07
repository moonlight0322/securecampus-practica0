# SC-LAB-004: Análisis SAST y DAST en Aplicación Web Segura

**Equipo**:
Ariadna Itzel Alvarez,
Montserrat Hernandez,
Luis Antonio Rivera,
Carlos Santana

**Fecha**:
23 de Septiembre de 2026

## 1. Objetivo

Implementar y documentar un ciclo de análisis de seguridad de software utilizando herramientas de Análisis Estático de Código (SAST con Semgrep) y Análisis Dinámico de Aplicaciones (DAST con OWASP ZAP), aplicando prácticas de remediación para vulnerabilidades comunes (Inyección SQL y XSS) en una aplicación desarrollada en Flask.

## 2. Entorno

- **Lenguaje:** Python 3.13[cite: 4]
- **Framework Web:** Flask 3.1.3[cite: 4]
- **Entorno Virtual:** `.venv` (excluido mediante `.gitignore`)[cite: 4]
- **Dependencias registradas:** `requirements.txt` (incluyendo Werkzeug 3.1.8, Jinja2 3.1.6, etc.)[cite: 4]
- **Estructura del proyecto:** Código fuente alojado en el directorio raíz `src/` (`webapp.py`)[cite: 4].

## 3. Análisis Humano

Durante la revisión manual del código fuente inicial, se identificaron puntos críticos de entrada de datos proporcionados por el usuario (parámetros GET en la ruta de búsqueda) y su posterior concatenación directa en cadenas de consulta SQL, lo que representaba un riesgo latente de Inyección SQL.

## 4. Comando y Hallazgo SAST

Se ejecutó el análisis estático para detectar patrones inseguros en el código base.

- **Herramienta:** Semgrep
- **Hallazgo principal:** Uso de consultas SQL inseguras mediante concatenación de variables en lugar de sentencias preparadas.

## 5. Corrección y Reanálisis

- **Mitigación:** Se rediseñó la lógica de la base de datos en `src/webapp.py` implementando consultas SQL parametrizadas con tuplas (`cursor.execute(consulta, (nombre,))`).
- **Reanálisis:** Tras aplicar la corrección, el análisis estático arrojó **0 hallazgos** de vulnerabilidades críticas.

## 6. Baseline DAST y Active Scan

- **Despliegue local:** El servidor web Flask se configuró y ejecutó exitosamente en el entorno de desarrollo local (`http://127.0.0.1:5000/`)[cite: 3, 4].
- **Escaneo Dinámico:** Se utilizó OWASP ZAP para realizar un escaneo activo sobre las rutas de la aplicación web, evaluando el comportamiento ante peticiones automatizadas y validando los encabezados de respuesta HTTP.

## 7. Validación Manual

Se realizaron pruebas funcionales directamente en la interfaz web local (`http://127.0.0.1:5000/`)[cite: 3, 4] ingresando diversos parámetros de búsqueda para corroborar el correcto flujo de datos y la correcta sanitización de la salida.

## 8. Corrección XSS y Retesting

- **Mitigación:** Se aseguró el motor de plantillas y el renderizado dinámico utilizando funciones seguras (`render_template_string`), previniendo la ejecución de scripts maliciosos en el lado del cliente (Cross-Site Scripting).
- **Retesting:** Las pruebas posteriores confirmaron que los caracteres especiales ingresados en los formularios son neutralizados de forma adecuada.

## 9. Comparación Final

| Fase / Componente             | Estado Inicial (Inseguro)        | Estado Final (Mitigado)               |
| :---------------------------- | :------------------------------- | :------------------------------------ |
| **Consultas a Base de Datos** | Concatenación directa de strings | Consultas SQL parametrizadas          |
| **Análisis Estático (SAST)**  | Alertas de vulnerabilidades SQLi | 0 vulnerabilidades detectadas         |
| **Análisis Dinámico (DAST)**  | Exposición a fallos de inyección | Control de entradas y rutas validadas |
