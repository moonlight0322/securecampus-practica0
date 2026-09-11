# SC-LAB-002
## Calificaciones
<Desarrollo>

## Reto por equipo
| Escenario | Situación |
| :--- | :--- |
| A · Documentos | Un estudiante intenta descargar el documento de otro usuario modificando un identificador. |
| B · Token | Un desarrollador intenta incluir un token dentro de un commit. |
| C · Profesor | Un profesor intenta modificar calificaciones de un grupo no asignado. |
| D · Login | Una cuenta registra 100 intentos fallidos de autenticación en 10 minutos. |


| Esc. | Requisitos | Diseño | Desarrollo | Pruebas | Despliegue | Operación |
|------|-----------|--------|------------|---------|------------|-----------|
| A | Requisito de validar propiedad/autorización en todo acceso a documentos | IDs no predecibles (UUID) + esquema de permisos (ACL) | Verificación de autorización a nivel de objeto en cada endpoint | Pruebas automatizadas y pentesting tipo IDOR | Escaneo SAST/DAST en CI/CD | Monitoreo de accesos anómalos + logs de auditoría |
| B | Política de no hardcodear información sensible en código | Arquitectura con gestor (Vault, varibles env) | `.gitignore` + capacitación del equipo | Pre-commit hooks (gitleaks, git-secrets) | Secret scanning en el pipeline de CI/CD | Rotación inmediata + purga del historial si hay filtración |
| C | Matriz de roles y permisos por profesor/grupo | RBAC con relación explícita profesor-grupo | Validación de asignación de grupo antes de modificar calificaciones | Casos de prueba negativos (acceso no autorizado) | Revisión de permisos por ambiente antes de producción | Auditoría periódica de cambios + alertas por modificaciones fuera de alcance |
| D | Requisito de mitigar fuerza bruta | Rate limiting + CAPTCHA progresivo | Límite de intentos, backoff exponencial, bloqueo temporal | Pruebas de carga/seguridad simulando ataques | WAF o servicio anti-bot en producción | Monitoreo en tiempo real (SIEM) + notificación al usuario |

## Clasificación conceptual

## Reflexión
