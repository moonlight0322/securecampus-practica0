# **SC-LAB-004: Análisis de Seguridad Estático y Dinámico (SAST + DAST)**

**Equipo**:
Ariadna Itzel Alvarez,
Montserrat Hernandez,
Luis Antonio Rivera,
Carlos Ivan Santana García

**Fecha**:
23 de Septiembre de 2026

---

## 1. Propósito y Entorno de Laboratorio

El objetivo de esta práctica es analizar una miniaplicación del entorno SecureCampus utilizando herramientas estáticas (SAST) y dinámicas (DAST), interpretar sus hallazgos, aplicar correcciones técnicas y validar la remediación mediante reanálisis y retesting.

### Requisitos del Entorno:
* **SO / CLI**: Windows 11 / PowerShell
* **Entorno Virtual**: Python 3.14 (`.venv`)
* **Framework**: Flask 3.x
* **Contenedores**: Docker Desktop
* **Herramienta SAST**: Semgrep Community (Contenedor Docker)
* **Herramienta DAST**: OWASP ZAP (zap-baseline / zap-full-scan vía Docker)

---

## 4. Parte A · SAST con Semgrep

### 4.1 Analiza primero como desarrollador (`src/app.py`)

| Pregunta | Respuesta del equipo |
| --- | --- |
| **¿Qué dato controla el usuario?** | El parámetro `nombre`, recibido a través de la función `input()`. |
| **¿A dónde llega ese dato?** | Se pasa como argumento a la función `buscar_estudiante(nombre)`. |
| **¿Qué riesgo observas?** | Se concatena directamente en la cadena SQL, permitiendo Inyección SQL (SQLi). |
| **¿Qué control propondrías?** | Uso de consultas parametrizadas (Prepared Statements) en lugar de concatenación. |

---

### 4.2 Ejecución e Interpretación de Semgrep

Se ejecutó el análisis estático sobre el directorio `src/`:

```
docker run --rm -v "${PWD}:/src" semgrep/semgrep semgrep scan --config auto /src/src
```

| Evidencia SAST | Registro|
| --- | --- |
| **Regla/hallazgo** | flask.render_template_string / Construcción insegura de HTML (XSS Reflejado) y concatenación de cadenas SQL. |
| **Archivo/línea** | src/webapp.py (Líneas 32-41) y src/app.py |
| **¿Qué evidencia aporta?** | Construcción manual de HTML mediante f-strings insertando la variable "nombre" sin escape de salida, lo que permite inyección de scripts (XSS). |
| **¿Coincide con tu análisis humano?** | Sí, la herramienta identificó el punto exacto de entrada y renderizado inseguro que revisamos. |

---

### 4.3 Corrige y reanaliza

Se sustituyó la concatenación directa en la consulta SQL de `src/app.py` por una consulta parametrizada:

```python
consulta = (
    "SELECT id, nombre, correo "
    "FROM estudiantes "
    "WHERE nombre = ?"
)
cursor.execute(consulta, (nombre,))
```

## 5. Parte B · DAST con OWASP ZAP

### 5.1 Baseline (observación pasiva)
```
docker run --rm -t ghcr.io/zaproxy/zaproxy:stable zap-baseline.py -t http://host.docker.internal:5000
```

| Hallazgo baseline | Observación | ¿Requiere análisis? |
| --- | --- | --- |
| Missing Anti-clickjacking Header | Falta X-Frame-Options o CSP frame-ancestors. | Sí |
| X-Content-Type-Options Header Missing | Ausencia de nosniff. | Sí |
| CSP Header Not Set | Falta política Content-Security-Policy. | Sí |

---

### 5.2 Active Scan (interacción deliberada)
```powerShell
docker run --rm -t ghcr.io/zaproxy/zaproxy:stable zap-full-scan.py -t http://host.docker.internal:5000
```
| Vulnerabilidad | Endpoint | Observación |
| --- | --- | --- |
| Cross Site Scripting (Reflected) [40012] | /buscar?nombre=... | El parámetro se reflejó directamente en el HTML. |

---

### 5.3 Validación manual
```
http://127.0.0.1:5000/buscar?nombre=<script>alert(1)</script>
```
Se ejecutó alert(1), confirmando el XSS reflejado.

---

### 5.4 Corrección aplicada
```python
@app.route("/buscar")
def buscar():
    nombre = request.args.get("nombre", "")
    resultado_html = """
    Resultado - SecureCampus
    Resultado de búsqueda
    Estudiante buscado: {{ nombre }}
    Regresar
    """
    return render_template_string(resultado_html, nombre=nombre)
```
---

### 5.5 Retesting

| Prueba | Resultado |
| --- | --- |
| Búsqueda legítima (ej. María) | Funciona correctamente. |
| Inyección <script>alert(1)</script> | Se muestra como texto plano. |
| Re-escaneo con ZAP | Hallazgo XSS pasó de WARN → PASS. |
| Advertencias restantes | Persisten alertas de cabeceras HTTP. |

---

## 6. Comparación SAST vs DAST

| Criterio | SAST | DAST |
| --- | --- | --- |
| Objeto | Código fuente | Aplicación en ejecución |
| Necesita ejecutar app | No | Sí |
| Perspectiva | Interna/estática | Externa/dinámica |
| Evidencia del lab | Construcción SQL insegura | XSS y configuración HTTP |
| Fortaleza | Detecta patrones/rutas en código | Observa comportamiento real expuesto |
| Límite | No garantiza lógica de negocio | No ve todo el código ni todas las rutas 
