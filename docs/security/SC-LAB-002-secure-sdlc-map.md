# SC-LAB-002: Mapa de Seguridad a lo largo del SDLC
**Equipo:** 
Ariadna Itzel Alvarez
Montserrat Hernandez
Luis Antonio Rivera
Carlos Santana

**Fecha:**
11 de Septiembre 2026  

---

## Calificaciones
**Caso:** Cambio de ID para consultar calificaciones ajenas.

| Fase | ¿Qué debería hacerse? | Control / evidencia |
| :--- | :--- | :--- |
| **Requisitos** | Definir que un alumno solo puede ver sus propias calificaciones. | Historias de usuario con criterios de aceptación de seguridad |
| **Diseño** | Diseñar la arquitectura con autorización | Diagrama de flujo indicando la verificación |
| **Desarrollo** | Implementar la lógica que compare la sesion del id del usuario con el id de la calificacion del usuario | Código del controlador validando la sesión activa |
| **Pruebas** | Ejecutar pruebas unitarias intentando acceder con ID de otro alumno | Reporte de pruebas confirmando un código de error 403 |
| **Despliegue** | Asegurar que el servidor no exponga cabeceras ni rutas de depuración | Configuración de producción limpia |
| **Operación** | Monitorear peticiones anómalas o bloqueos masivos. | Logs centralizados con alertas de intentos |

