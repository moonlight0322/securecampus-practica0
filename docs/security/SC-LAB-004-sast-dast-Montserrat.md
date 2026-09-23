# Laboratorio 4 - SAST y DAST

* **Proyecto:** 23 de septiembre del 2026
* **Práctica:** SC-LAB-004
 Montserrat Hernandez Fabian

---

## 2. Análisis Estático 
* **Herramienta utilizada:** Semgrep (vía Docker).
* **Hallazgo detectado:** Inyección SQL  en el archivo src/app.py por una concatenacion de variables en la consulta 
* **Correccion:** Se hizo una consulta parametrizada:
  ```python
  consulta = (
      "SELECT id, nombre, correo "
      "FROM estudiantes "
      "WHERE nombre = ?"
  )
  cursor.execute(consulta, (nombre,)) ```

##3. Análisis Dinámico

* **Herramienta utilizada:** OWASP ZAP
* **Hallazgo detectado:** Vulnerabilidad reflejando el endpoint de webapp.py 
* **Correccion:** Se hizo una modificación usando una doble llave:
 ``` @app.route("/buscar")
def buscar():
    nombre = request.args.get("nombre", "")
    resultado_html = """
    <html>
        <head><title>Resultado - SecureCampus</title></head>
        <body>
            <h1>Resultado de búsqueda</h1>
            <p>Estudiante buscado: {{ nombre }}</p>
            <a href="/">Regresar</a>
        </body>
    </html>
    """
    return render_template_string(resultado_html, nombre=nombre)  ```


