# SC-LAB-001
## Consulta de perfiles
Equipo:
Ariadna Itzel Alvarez
Montserrat Hernandez
Luis Antonio Rivera
Carlos Santana

Fecha:
11 de Septiembbre de 2026

| Elemento       | Respuesta del equipo | Justificación |
|----------------|----------------------|----------------|
| **Activo** | La información personal y académica de los estudiantes almacenada en los perfiles | Es el recurso de valor que el sistema debe proteger; en este caso, los datos de identidad y desempeño de cada alumno. |
| **Amenaza** | Un usuario autenticado que accede intencional o accidentalmente a información de otros usuarios. | La amenaza es la posibilidad de que alguien con acceso legítimo al sistema abuse de ese acceso para ver datos que no le corresponden. |
| **Vulnerabilidad** | Falta de validación de autorización en el servidor, el sistema confía en el número de perfil que viene en la URL sin verificar si pertenece al usuario que hizo la solicitud. | El cambio manual de `/perfil/125` a `/perfil/126` no debería funcionar si el backend verificara que el usuario autenticado solo puede consultar su propio perfil (o perfiles autorizados). |
| **Ataque** | IDOR (Insecure Direct Object Reference). | María modifica directamente un parámetro visible (el ID del perfil) para intentar acceder a un recurso distinto al suyo, sin necesidad de credenciales adicionales. |
| **Impacto** | Exposición no autorizada de datos personales de otro estudiante; posible violación de confidencialidad y de normativas de protección de datos). | Cualquier estudiante podría ver información sensible de sus compañeros simplemente cambiando un número en la URL, lo cual compromete la privacidad de todos los usuarios del sistema. |
| **Riesgo** | Alto, probabilidad alta (el ataque es fácil de ejecutar) combinada con impacto medio-alto. | No requiere conocimientos técnicos avanzados, solo editar la URL, por lo que cualquier usuario del sistema podría explotarlo, y el número de perfiles afectados podría ser todo el estudiantado. |
| **Control** | Implementar control de acceso a nivel de objeto (autorización) en el servidor: verificar que el `id` solicitado pertenezca al usuario autenticado (o a un rol con permiso), usar identificadores no predecibles (UUID en vez de IDs secuenciales), y registrar/auditar accesos a perfiles. | Estas medidas atacan la causa raíz (falta de verificación de autorización) en vez de solo ocultar el problema, y dificultan además la enumeración de recursos. |
