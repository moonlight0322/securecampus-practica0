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


## 3. Preguntas de Reflexión[cite: 1]

1. **¿Una amenaza y una vulnerabilidad son lo mismo? Explica con un ejemplo de SecureCampus.**[cite: 1]

   > **No, son conceptos distintos pero complementarios:**[cite: 1]
   >
   > - **Vulnerabilidad:** Es una _debilidad interna_ existente en el diseño, código o configuración del sistema[cite: 1]. _Ejemplo en SecureCampus:_ Un endpoint de la API que no verifica el rol del usuario antes de ejecutar una acción[cite: 1].
   > - **Amenaza:** Es un _factor o actor externo/interno_ que tiene el potencial de explotar esa debilidad para causar daño[cite: 1]. _Ejemplo en SecureCampus:_ Un estudiante que busca modificar sus calificaciones de manera malintencionada[cite: 1].

2. **¿Puede existir una vulnerabilidad aunque todavía nadie la haya explotado?**[cite: 1]

   > **Sí.** Una vulnerabilidad es un defecto de seguridad intrínseco en el software que existe desde el momento en que se escribe o configura el código con errores[cite: 1]. Que no haya sido descubierta o explotada por un atacante (_Zero-day_) no significa que la falla no esté presente[cite: 1].

3. **¿Un usuario autenticado está automáticamente autorizado para cualquier recurso?**[cite: 1]

   > **No.**[cite: 1]
   >
   > - **Autenticación** responde a la pregunta _¿Quién eres?_ (Verifica la identidad mediante usuario y contraseña)[cite: 1].
   > - **Autorización** responde a la pregunta _¿Qué tienes permitido hacer?_ (Verifica permisos específicos)[cite: 1].
   > - _Ejemplo:_ En SecureCampus, un estudiante autenticado no debe tener autorización para modificar calificaciones ni para ver los perfiles de otros estudiantes[cite: 1].

4. **¿Qué control de los propuestos debería definirse desde requisitos o diseño? ¿Por qué?**[cite: 1]

   > **El control de acceso basado en roles (RBAC) e identidades (Autorización en Backend)[cite: 1].**  
   > _Razonamiento:_ El control de acceso no se puede agregar como un "parche" al final del desarrollo. Debe modelarse desde la arquitectura de software y la especificación de requisitos (Security by Design)[cite: 1]. Si no se define desde el diseño qué roles existen y qué endpoints puede consultar cada uno, la reestructuración del backend en etapas posteriores resultará costosa y propensa a fallos de seguridad[cite: 1].

5. **¿Qué activo consideran más crítico y por qué?**[cite: 1]
   > **La base de datos de credenciales y tokens de sesión (Autenticación), seguida por los Registros Académicos / Calificaciones[cite: 1].**  
   > _Razonamiento:_ Si el activo de autenticación se compromete, un atacante obtiene el control de cuentas de profesores o administradores, lo que le permitiría acceder y modificar implícitamente todos los demás activos del sistema sin levantar sospechas inmediatas[cite: 1].

---
