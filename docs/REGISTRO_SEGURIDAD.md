# Registro de seguridad · Sistema Alumnos

Estudiante: ____________________   Repositorio: https://github.com/catvstrofe/Parcial3-sistema-alumnos   Fecha: 2026-11-23

Completa este registro a medida que avanzas. Cada fila debe poder seguirse hasta un **build de Jenkins**
y un **commit** (usa el identificador `VULN-xx` en el mensaje del commit).

## 1. Revisiones de seguridad realizadas

| # | Etapa del SDLC | Herramienta / técnica | Build | Resultado (resumen) |
|---|---|---|---|---|
| R1 | Código (SAST) | Bandit en el pipeline | #2 | 2 High / 2 Medium / 1 Low (detección) |
| R2 | Dependencias (SCA) | pip-audit en el pipeline | #2 | 19 vulnerabilidades en 4 paquetes |
| R3 | Pruebas dinámicas (DAST) | OWASP ZAP sobre staging | #3 | 3 High / 3 Medium / 1 Low |
| R4 | Revisión manual | Lectura de `app.py` | — | SQLi, XSS, MD5, secreto hardcodeado, CSRF, debug |
| R5 | Producción | Grafana + Prometheus | — | Dashboard de producción y 2 alertas (fuerza bruta y peticiones sospechosas) |
| R6 | Verificación SAST | Bandit en el pipeline | #5 | 0 High / 0 Medium / 0 Low |
| R7 | Verificación DAST | OWASP ZAP sobre staging | #5 | 0 High / 0 Medium / 0 Low |
| R8 | Verificación post-mitigación | Grafana + logs de producción | #7 | 11 bloqueos (HTTP 429), 0 accesos exitosos, alertas en Firing |

## 2. Vulnerabilidades identificadas y corregidas

| ID | Vulnerabilidad (CWE) | Detectada por | Ubicación | Riesgo | Corrección aplicada | Commit | Build que lo verifica |
|---|---|---|---|---|---|---|---|
| VULN-01 | Inyección SQL (CWE-89) | Bandit B608 / ZAP | `app.py:205` | High | Consulta parametrizada `LIKE ?` | `8504066` | #5 |
| VULN-02 | XSS reflejado/DOM (CWE-79) | OWASP ZAP | `app.py:154` | High | Escape de `name` con `markupsafe` | `fa66794` | #5 |
| VULN-03 | Secreto hardcodeado (CWE-798) | Bandit B105 | `app.py:20` | Low | `SECRET_KEY` desde entorno o aleatoria | `be8364c` | #5 |
| VULN-04 | Hash MD5 de contraseña (CWE-916) | Bandit B324 | `app.py:22,221` | High | `generate_password_hash` / `check_password_hash` | `3295e3e` | #5 |
| VULN-05 | Fuga de información en errores (CWE-209) | Revisión manual | `app.py:209` | Medium | Error genérico sin SQL | `bd820ab` | #5 |
| VULN-06 | CSRF en `/login` (CWE-352) | OWASP ZAP | `app.py:69-72,213` | Medium | Token anti-CSRF en sesión | `fc52fe9` | #5 |
| VULN-07 | Debug y bind 0.0.0.0 (CWE-489/605) | Bandit B201/B104 | `app.py:241` | High/Medium | `debug=False` y host local por defecto | `993bd32` | #5 |
| VULN-08 | Cabeceras de seguridad faltantes (CWE-693) | OWASP ZAP | `after_request` | Medium | CSP, X-Frame-Options, X-Content-Type-Options, Referrer-Policy | `e832a51`, `7c3c3f1` | #5 |
| VULN-09 | Flags de cookie (CWE-1004) | Revisión / ZAP | configuración Flask | Low | `HttpOnly` + `SameSite=Lax` | `adf9725` | #5 |
| VULN-10 | Fuerza bruta / falta de limitación de intentos (CWE-307) | Simulación de ataque | `app.py:213-231` | High | Límite de 20 fallos por IP en 60 s (HTTP 429) | `3c028e4` | #7 |

## 3. Resultados de OWASP ZAP

| Alerta de ZAP | Riesgo | URL de ejemplo | VULN relacionada | Cómo se mitiga | ¿Mitigada? Build |
|---|---|---|---|---|---|
| Cross Site Scripting (DOM Based) | High | `http://jenkins:5001/hello?name=...` | VULN-02 | Escape de la salida con `markupsafe` | Sí, #5 |
| Cross Site Scripting (Reflected) | High | `http://jenkins:5001/hello?name=...` | VULN-02 | Escape de la salida con `markupsafe` | Sí, #5 |
| SQL Injection | High | `http://jenkins:5001/alumnos/buscar?nombre=%27` | VULN-01 | Consulta parametrizada | Sí, #5 |
| Absence of Anti-CSRF Tokens | Medium | `http://jenkins:5001` | VULN-06 | Token anti-CSRF en sesión | Sí, #5 |
| Content Security Policy (CSP) Header Not Set | Medium | `http://jenkins:5001` | VULN-08 | Cabecera `Content-Security-Policy` | Sí, #5 |
| Missing Anti-clickjacking Header | Medium | `http://jenkins:5001` | VULN-08 | `X-Frame-Options` y `frame-ancestors` | Sí, #5 |
| CSP: Failure to Define Directive with No Fallback | Medium | `http://jenkins:5001` | VULN-08 | `form-action 'self'` | Sí, #5 |
| X-Content-Type-Options Header Missing | Low | `http://jenkins:5001` | VULN-08 | `X-Content-Type-Options: nosniff` | Sí, #5 |

## 4. Monitoreo e incidentes

| Hora | Panel / métrica de Grafana | Qué se observó | Entorno | Clasificación | Acción de respuesta | Verificación |
|---|---|---|---|---|---|---|
| 18:59 local / 21:59 UTC | `login_fallidos_total`, `login_exitosos_total`, `peticiones_sospechosas_total{tipo}` | Fuerza bruta: 30 logins fallidos y 1 exitoso con la clave por defecto `admin`; 1 SQLi y 1 XSS detectados. Alertas "Fuerza bruta en login" y "Peticiones sospechosas" en Firing. | Producción | Alta (acceso no autorizado por fuerza bruta + intento de SQLi/XSS) | Se revisó el log, se confirmó la causa (contraseña por defecto), se verificó que SQLi/XSS no tuvieron efecto (consulta parametrizada y escape) y se documentó la recomendación de rotar credenciales y limitar intentos. | Alertas en Firing durante el ataque y vuelta a Normal al cesar el tráfico; `login_exitosos_total` evidencia el acceso. |
| 19:38 local / 22:38 UTC | `login_fallidos_total`, `login_bloqueados_total`, `login_exitosos_total` | Ataque repetido tras VULN-10: 31 intentos, 11 bloqueados (HTTP 429), 0 accesos exitosos; SQLi y XSS sin efecto. Alertas en Firing. | Producción | Alta (verificación de mitigación) | Se aplicó la limitación de intentos (VULN-10) y se verificó el bloqueo; se mantiene la recomendación de rotar credenciales. | `login_exitosos_total=0`; 11 respuestas 429; alertas en Firing; build #7 SUCCESS. |

## 5. Gestión de dependencias

| Paquete | Versión inicial | Vulnerabilidades (IDs) | Versión final | Origen del cambio (PR de Dependabot / manual) | ¿Pruebas, pip-audit y ZAP siguen OK? Build |
|---|---|---|---|---|---|
| Flask | 2.2.2 | | | | |
| Werkzeug | 2.2.2 | | | | |
| Jinja2 | 3.1.2 | | | | |
| gunicorn | 21.2.0 | | | | |
| prometheus-client | 0.17.1 | | | | |

## 6. Trazabilidad

Pega aquí (o adjunta) el `TRAZABILIDAD.md` del último build y completa un recorrido por cada actividad del pipeline:

| Actividad | Hallazgo | Build que lo detectó | Commit / PR que lo resolvió | Build que lo verificó | Versión en producción |
|---|---|---|---|---|---|
| Revisión de seguridad (Bandit) | | #__ | | #__ | |
| Prueba automatizada (OWASP ZAP) | | #__ | | #__ | |
| Gestión de dependencias (Dependabot) | | #__ | PR #__ | #__ | |
