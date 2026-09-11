# SC-LAB-002

## Integrantes

Ariadna Itzel Alvarez,
Montserrat Hernandez,
Luis Antonio Rivera,
Carlos Santana

## Calificaciones

<Desarrollo>

## Reto por equipo

| Escenario      | Situación                                                                                  |
| :------------- | :----------------------------------------------------------------------------------------- |
| A · Documentos | Un estudiante intenta descargar el documento de otro usuario modificando un identificador. |
| B · Token      | Un desarrollador intenta incluir un token dentro de un commit.                             |
| C · Profesor   | Un profesor intenta modificar calificaciones de un grupo no asignado.                      |
| D · Login      | Una cuenta registra 100 intentos fallidos de autenticación en 10 minutos.                  |

| Esc. | Requisitos                                                              | Diseño                                                | Desarrollo                                                          | Pruebas                                          | Despliegue                                            | Operación                                                                    |
| ---- | ----------------------------------------------------------------------- | ----------------------------------------------------- | ------------------------------------------------------------------- | ------------------------------------------------ | ----------------------------------------------------- | ---------------------------------------------------------------------------- |
| A    | Requisito de validar propiedad/autorización en todo acceso a documentos | IDs no predecibles (UUID) + esquema de permisos (ACL) | Verificación de autorización a nivel de objeto en cada endpoint     | Pruebas automatizadas y pentesting tipo IDOR     | Escaneo SAST/DAST en CI/CD                            | Monitoreo de accesos anómalos + logs de auditoría                            |
| B    | Política de no hardcodear información sensible en código                | Arquitectura con gestor (Vault, varibles env)         | `.gitignore` + capacitación del equipo                              | Pre-commit hooks (gitleaks, git-secrets)         | Secret scanning en el pipeline de CI/CD               | Rotación inmediata + purga del historial si hay filtración                   |
| C    | Matriz de roles y permisos por profesor/grupo                           | RBAC con relación explícita profesor-grupo            | Validación de asignación de grupo antes de modificar calificaciones | Casos de prueba negativos (acceso no autorizado) | Revisión de permisos por ambiente antes de producción | Auditoría periódica de cambios + alertas por modificaciones fuera de alcance |
| D    | Requisito de mitigar fuerza bruta                                       | Rate limiting + CAPTCHA progresivo                    | Límite de intentos, backoff exponencial, bloqueo temporal           | Pruebas de carga/seguridad simulando ataques     | WAF o servicio anti-bot en producción                 | Monitoreo en tiempo real (SIEM) + notificación al usuario                    |

## Clasificación conceptual

| Decisión tomada en el mapa                                                                               | Concepto que representa | Justificación                                                                                                                                                                            |
| :------------------------------------------------------------------------------------------------------- | :---------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1. Uso de pre-commit hooks (`gitleaks`) para evitar subir tokens al repositorio (Escenario B).**       | **Shift Left**          | Traslada la detección de la vulnerabilidad al punto más temprano posible del ciclo de vida (el entorno local del desarrollador), impidiendo que el secreto llegue al repositorio remoto. |
| **2. Aplicación estricta de RBAC y denegación implícita de acceso a grupos no asignados (Escenario C).** | **Security by Default** | El sistema se entrega configurado por defecto en un estado restrictivo (sin permisos), exigiendo la verificación explícita de la relación profesor-grupo para conceder acceso.           |

---

## 4. Reflexión

1. **¿Qué riesgo de SC-LAB-001 necesitó controles en más fases?**

   > **La autorización inadecuada y el acceso no autorizado a recursos (IDOR / Escalamiento de permisos en Documentos y Calificaciones).** Requiere especificación desde Requisitos, modelado en Diseño, programación defensiva en Desarrollo, pruebas automatizadas en Pruebas, escaneo en Despliegue y monitoreo/auditoría activa en Operación.

2. **¿Qué habría ocurrido si el equipo hubiera esperado hasta pruebas?**

   > Descubrir fallas estructurales de autorización en la fase de pruebas obliga a rediseñar modelos de datos o reescribir controladores completos. Esto incrementa drásticamente los costos de corrección, retrasa el proyecto y genera soluciones improvisadas que suelen introducir nuevas vulnerabilidades.

3. **¿Qué control depende de una regla de negocio y cuál puede automatizarse?**
   > - **Regla de negocio:** La validación de que un profesor sólo modifique notas de sus grupos asignados (requiere contexto del dominio académico).
   > - **Automatizable:** El escaneo de secretos con _pre-commit hooks_ y el bloqueo automático de IP por intentos masivos de inicio de sesión (_Rate Limiting_).

---

## 5. Pregunta de cierre

> **¿Por qué la seguridad no es una fase final?**  
> Porque la seguridad es un atributo de calidad transversal (_Secure SDLC_). Si se aborda como una revisión final, solo se detectan síntomas cuando el software ya está construido, resultando en parches costosos; si se integra desde los requisitos, previene las vulnerabilidades por diseño.
