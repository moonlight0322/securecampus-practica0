# SC-LAB-001
## Consulta de perfiles
Equipo:
Ariadna Itzel Alvarez, 
Montserrat Hernandez, 
Luis Antonio Rivera, 
Carlos Santana

Fecha:
11 de Septiembre de 2026

| Elemento       | Respuesta del equipo | Justificación |
|----------------|----------------------|----------------|
| **Activo** | La información personal y académica de los estudiantes almacenada en los perfiles | Es el recurso de valor que el sistema debe proteger; en este caso, los datos de identidad y desempeño de cada alumno. |
| **Amenaza** | Un usuario autenticado que accede intencional o accidentalmente a información de otros usuarios. | La amenaza es la posibilidad de que alguien con acceso legítimo al sistema abuse de ese acceso para ver datos que no le corresponden. |
| **Vulnerabilidad** | Falta de validación de autorización en el servidor, el sistema confía en el número de perfil que viene en la URL sin verificar si pertenece al usuario que hizo la solicitud. | El cambio manual de `/perfil/125` a `/perfil/126` no debería funcionar si el backend verificara que el usuario autenticado solo puede consultar su propio perfil (o perfiles autorizados). |
| **Ataque** | IDOR (Insecure Direct Object Reference). | María modifica directamente un parámetro visible (el ID del perfil) para intentar acceder a un recurso distinto al suyo, sin necesidad de credenciales adicionales. |
| **Impacto** | Exposición no autorizada de datos personales de otro estudiante; posible violación de confidencialidad y de normativas de protección de datos). | Cualquier estudiante podría ver información sensible de sus compañeros simplemente cambiando un número en la URL, lo cual compromete la privacidad de todos los usuarios del sistema. |
| **Riesgo** | Alto, probabilidad alta (el ataque es fácil de ejecutar) combinada con impacto medio-alto. | No requiere conocimientos técnicos avanzados, solo editar la URL, por lo que cualquier usuario del sistema podría explotarlo, y el número de perfiles afectados podría ser todo el estudiantado. |
| **Control** | Implementar control de acceso a nivel de objeto (autorización) en el servidor: verificar que el `id` solicitado pertenezca al usuario autenticado (o a un rol con permiso), usar identificadores no predecibles (UUID en vez de IDs secuenciales), y registrar/auditar accesos a perfiles. | Estas medidas atacan la causa raíz (falta de verificación de autorización) en vez de solo ocultar el problema, y dificultan además la enumeración de recursos. |

## 5. Reto por equipo: Escenarios

| Escenario | Activo | Amenaza | Vulnerabilidad | Ataque | Impacto | Control |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **1. Calificaciones**| Historial académico. | Alumno o profesor no asignado. | Ausencia de validación. | Alteración de los parámetros de la solicitud. | Pérdida de seguridad en las calificaciones. | Validar relación profesor-grupo antes de aceptar la modificación. |
| **2. Documentos** | Archivos subidos al sistema. | Atacante externo. | Almacenamiento público con nombres de archivo predecibles. | Descarga forzada. | Fuga de documentos sensibles. | Usar almacenamiento privado con validación de sesión. |
| **3. Autenticación** | Cuentas de usuario. | Atacante automatizado. | Falta de límite de peticiones en login. | Fuerza bruta. | Compromiso total de la cuenta. | Bloqueo temporal de cuenta tras 5 intentos fallidos y uso de CAPTCHA. |
| **4. Logs de Auditoría** | Registros del sistema. | Administrador comprometido. | Logs almacenados en texto plano con permisos de escritura. | Borrado o alteración del archivo de logs. | Pérdida de trazabilidad para investigar incidentes. | Envío de logs a un SIEM externo de solo lectura. |


## 3. Preguntas de Reflexión

1. **¿Una amenaza y una vulnerabilidad son lo mismo? Explica con un ejemplo de SecureCampus.**

   > **No, son conceptos distintos pero complementarios:**
   >
   > - **Vulnerabilidad:** Es una _debilidad interna_ existente en el diseño, código o configuración del sistema. _Ejemplo en SecureCampus:_ Un endpoint de la API que no verifica el rol del usuario antes de ejecutar una acción.
   > - **Amenaza:** Es un _factor o actor externo/interno_ que tiene el potencial de explotar esa debilidad para causar daño. _Ejemplo en SecureCampus:_ Un estudiante que busca modificar sus calificaciones de manera malintencionada.

2. **¿Puede existir una vulnerabilidad aunque todavía nadie la haya explotado?**

   > **Sí.** Una vulnerabilidad es un defecto de seguridad intrínseco en el software que existe desde el momento en que se escribe o configura el código con errores. Que no haya sido descubierta o explotada por un atacante (_Zero-day_) no significa que la falla no esté presente

3. **¿Un usuario autenticado está automáticamente autorizado para cualquier recurso?**

   > **No.**
   >
   > - **Autenticación** responde a la pregunta _¿Quién eres?_ (Verifica la identidad mediante usuario y contraseña)
   > - **Autorización** responde a la pregunta _¿Qué tienes permitido hacer?_ (Verifica permisos específicos).
   > - _Ejemplo:_ En SecureCampus, un estudiante autenticado no debe tener autorización para modificar calificaciones ni para ver los perfiles de otros estudiantes.

4. **¿Qué control de los propuestos debería definirse desde requisitos o diseño? ¿Por qué?**

   > **El control de acceso basado en roles (RBAC) e identidades (Autorización en Backend).**  
   > _Razonamiento:_ El control de acceso no se puede agregar como un "parche" al final del desarrollo. Debe modelarse desde la arquitectura de software y la especificación de requisitos (Security by Design). Si no se define desde el diseño qué roles existen y qué endpoints puede consultar cada uno, la reestructuración del backend en etapas posteriores resultará costosa y propensa a fallos de seguridad.

5. **¿Qué activo consideran más crítico y por qué?**
   > **La base de datos de credenciales y tokens de sesión (Autenticación), seguida por los Registros Académicos / Calificaciones.**  
   > _Razonamiento:_ Si el activo de autenticación se compromete, un atacante obtiene el control de cuentas de profesores o administradores, lo que le permitiría acceder y modificar implícitamente todos los demás activos del sistema sin levantar sospechas inmediatas.

---
