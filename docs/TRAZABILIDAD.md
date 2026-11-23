# Registro de trazabilidad - Sistema Alumnos

Cada fila la agrega Jenkins al terminar un build (`scripts/trazabilidad.py`).

| Build | Fecha (UTC) | Versión | Commit | Mensaje del commit | Pruebas | Bandit H/M/L | pip-audit | ZAP H/M/L | Resultado | Producción |
|---|---|---|---|---|---|---|---|---|---|---|
| 1 | 2026-11-23 19:49 | 1.0.1+feda40f | `feda40f` | chore: version inicial Sistema Alumnos | 9/9 OK | n/a | n/a | n/a | SUCCESS | 1.0.1+feda40f |
| 2 | 2026-11-23 20:10 | 1.0.2+f265651 | `f265651` | chore: agregar SAST (Bandit) y SCA (pip-audit) al pipeline | 9/9 OK | 2/2/1 | 19 vuln. | n/a | SUCCESS | 1.0.2+f265651 |
| 3 | 2026-11-23 20:31 | 1.0.3+7d6c0b1 | `7d6c0b1` | feat: integrar OWASP ZAP (DAST) sobre staging | 9/9 OK | 2/2/1 | 19 vuln. | 3/3/1 | UNSTABLE | 1.0.3+7d6c0b1 |
| 4 | 2026-11-23 20:58 | 1.0.4+3833d5a | `3833d5a` | chore: fallar SAST en Medium/High y proteger produccion | 9/9 OK | 0/0/0 | 19 vuln. | 0/1/0 | UNSTABLE | no desplegado |
| 5 | 2026-11-23 21:04 | 1.0.5+7c3c3f1 | `7c3c3f1` | VULN-08: definir form-action en la CSP | 9/9 OK | 0/0/0 | 19 vuln. | 0/0/0 | SUCCESS | 1.0.5+7c3c3f1 |
| 6 | 2026-11-23 21:11 | 1.0.6+b822969 | `b822969` | docs: registrar revisiones, vulnerabilidades y resultados ZAP | 9/9 OK | 0/0/0 | 19 vuln. | 0/0/0 | SUCCESS | 1.0.6+b822969 |
| 7 | 2026-11-23 22:37 | 1.0.7+3c028e4 | `3c028e4` | VULN-10: limitar intentos de login fallidos | 9/9 OK | 0/0/0 | 19 vuln. | 0/0/0 | SUCCESS | 1.0.7+3c028e4 |
| 8 | 2026-11-23 22:43 | 1.0.8+3864812 | `3864812` | docs: registrar VULN-10 y verificacion del incidente | 9/9 OK | 0/0/0 | 19 vuln. | 0/0/0 | SUCCESS | 1.0.8+3864812 |
| 9 | 2026-11-23 22:58 | 1.0.9+7fa74a6 | `7fa74a6` | chore: configurar Dependabot para dependencias Python | 9/9 OK | 0/0/0 | 19 vuln. | 0/0/0 | SUCCESS | 1.0.9+7fa74a6 |
| 10 | 2026-11-23 23:01 | 1.0.10+2033b97 | `2033b97` | chore(deps): bump the python-dependencies group with 6 updates | 9/9 OK | 0/0/0 | 0 vuln. | 0/0/0 | SUCCESS | 1.0.10+2033b97 |
| 11 | 2026-11-23 23:04 | 1.0.11+aea7def | `aea7def` | docs: registrar gestion de dependencias (Dependabot) | 9/9 OK | 0/0/0 | 0 vuln. | 0/0/0 | SUCCESS | 1.0.11+aea7def |
| 12 | 2026-11-23 23:16 | 1.0.12+2f5ab79 | `2f5ab79` | Update student name in security log | 9/9 OK | 0/0/0 | 0 vuln. | 0/0/0 | SUCCESS | 1.0.12+2f5ab79 |
| 13 | 2026-11-23 23:22 | 1.0.13+4ddf4cb | `4ddf4cb` | docs: agregar trazabilidad completa y seccion 6 | 9/9 OK | 0/0/0 | 0 vuln. | 0/0/0 | SUCCESS | 1.0.13+4ddf4cb |
