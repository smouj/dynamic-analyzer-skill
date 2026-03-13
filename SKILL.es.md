## Propósito

La habilidad Analizador Dinámico proporciona capacidades de análisis dinámico impulsadas por IA para la ejecución de software en tiempo real, monitoreo de rendimiento y detección de anomalías. Permite el análisis automatizado de aplicaciones en ejecución, flujos de logs y comportamientos del sistema para identificar problemas, optimizar el rendimiento y asegurar la confiabilidad.

### Casos de Uso Reales

1. **Perfilado de Código en Tiempo de Ejecución**: Analizar aplicaciones Python durante la ejecución para detectar fugas de memoria, cuellos de botella de CPU y algoritmos ineficientes en entornos similares a producción.
2. **Análisis de Flujo de Logs**: Procesar archivos de log en vivo de servidores web (ej. Nginx, Apache) para identificar patrones de error, amenazas de seguridad y degradación de rendimiento en tiempo real.
3. **Monitoreo de Endpoints de API**: Probar dinámicamente APIs RESTful contra escenarios predefinidos para detectar retrasos en respuestas, anomalías en códigos de estado y problemas de integridad de datos.
4. **Optimización de Consultas de Base de Datos**: Perfilar consultas SQL en una base de datos MySQL/PostgreSQL en ejecución para sugerir índices, reescrituras de consultas y ajustes de pool de conexiones.
5. **Verificaciones de Salud de Contenedores**: Analizar contenedores Docker durante el tiempo de ejecución para picos de uso de recursos, latencia de red y fallos de dependencias de servicios.
6. **Validación de Pipeline CI/CD**: Integrarse con GitHub Actions/Jenkins para analizar dinámicamente artefactos de construcción y scripts de despliegue en busca de vulnerabilidades de seguridad y regresiones de rendimiento.

## Alcance

Esta habilidad maneja tareas de análisis dinámico que requieren ejecución en tiempo de ejecución y recopilación de datos en tiempo real. No realiza análisis de código estático (usa static-analyzer para eso).

### Comandos Específicos

- `/dynamic-analyze profile --target <app> --duration 60s --output profile.json`: Perfilar el rendimiento de la aplicación por 60 segundos y guardar resultados.
- `/dynamic-analyze logs --stream /var/log/app.log --pattern \"ERROR|WARN\" --ai-insights`: Analizar flujo de log en vivo para patrones y generar insights de IA.
- `/dynamic-analyze api-test --url https://api.example.com --scenarios load_test.json --concurrency 10`: Probar endpoints de API con escenarios de carga concurrentes.
- `/dynamic-analyze db-query --dsn mysql://user:pass@host/db --query \"SELECT * FROM users\" --explain`: Perfilar y explicar la ejecución de consultas de base de datos.
- `/dynamic-analyze container --name myapp --metrics cpu,memory,network --alert-threshold 80`: Monitorear métricas de contenedor y alertar en umbrales.
- `/dynamic-analyze pipeline --repo smouj/myrepo --branch main --checks security,performance`: Validar pipeline CI/CD con verificaciones dinámicas.

## Proceso de Trabajo Detallado

### Paso 1: Configuración del Entorno
- Asegurar que las dependencias estén instaladas: `openclaw doctor --check-dependencies`
- Establecer variables de entorno si es necesario (ej. `export DYNAMIC_ANALYZER_LOG_LEVEL=DEBUG`)
- Verificar la accesibilidad del sistema objetivo (ej. SSH a servidor remoto si se analizan apps distribuidas)

### Paso 2: Configuración del Objetivo
- Identificar el objetivo de análisis (binario de aplicación, ruta de archivo de log, endpoint de API, cadena de conexión de DB)
- Configurar parámetros de análisis (duración, tasa de muestreo, formato de salida)
- Para análisis mejorado con IA, especificar modelo y profundidad de insights (ej. `--ai-model grok-code-fast-1 --insights deep`)

### Paso 3: Ejecución y Recopilación de Datos
- Lanzar comando de análisis con banderas apropiadas
- Monitorear progreso en tiempo real (usar `--verbose` para salida detallada)
- Recopilar datos en tiempo de ejecución: uso de CPU, asignación de memoria, I/O de red, estados de hilo

### Paso 4: Análisis Impulsado por IA
- Alimentar datos recopilados al motor de IA para reconocimiento de patrones
- Generar insights: detección de anomalías, sugerencias de causa raíz, recomendaciones de optimización
- Referenciar cruzadamente con datos históricos si están disponibles (vía `--history-db /path/to/db`)

### Paso 5: Generación de Reporte
- Salida de resultados al formato especificado (JSON, CSV, Markdown)
- Incluir visualizaciones si se usa la bandera `--chart` (requiere dependencia matplotlib)
- Enviar notificaciones vía Slack/Email si se establece `--notify`

### Paso 6: Verificación
- Ejecutar verificaciones: `openclaw verify dynamic-analyzer --session-id <id>`
- Comparar resultados contra baselines (ej. métricas de rendimiento esperadas)
- Registrar sesión de análisis para auditoría

## Reglas de Oro

1. **Nunca analizar sistemas de producción sin acceso de solo lectura**: Usar la bandera `--read-only` para prevenir modificaciones durante el perfilado.
2. **Establecer timeouts para prevenir procesos descontrolados**: Siempre especificar `--timeout` para evitar ejecución indefinida.
3. **Aislar entorno de análisis**: Ejecutar en contenedores sandboxed o VMs para prevenir interferencia con sistemas objetivo.
4. **Respetar límites de tasa**: Para pruebas de API, usar `--rate-limit 100req/min` para evitar sobrecargar servicios.
5. **Los insights de IA son sugerencias, no absolutos**: Siempre verificar manualmente las recomendaciones generadas por IA antes de la implementación.
6. **Respaldar datos antes del análisis destructivo**: Si se necesita `--destructive` (raro), crear snapshots primero.
7. **Registrar todas las sesiones**: Habilitar `--audit-log` para mantener trazabilidad para cumplimiento.

## Ejemplos

### Ejemplo 1: Perfilado de una App Flask
**Prompt de Entrada:**
```
/dynamic-analyze profile --target python app.py --duration 120s --output flask_profile.json --ai-insights --verbose
```

**Salida Esperada:**
```
[INFO] Starting dynamic profiling of python app.py
[DEBUG] Sampling CPU every 1s, memory every 5s
[PROGRESS] 30s elapsed: CPU avg 45%, Memory peak 512MB
[AI INSIGHT] Detected memory leak in route /api/users - potential unbounded list growth
[PROGRESS] 120s elapsed: Analysis complete
[REPORT] Results saved to flask_profile.json
Recommendations:
- Implement pagination for /api/users endpoint
- Add garbage collection hints in hot loops
```

### Ejemplo 2: Análisis de Logs para Patrones de Error
**Prompt de Entrada:**
```
/dynamic-analyze logs --stream /var/log/nginx/error.log --pattern \"5[0-9][0-9]\" --ai-insights --alert-email admin@example.com
```

**Salida Esperada:**
```
[INFO] Monitoring log stream /var/log/nginx/error.log
[ALERT] Pattern match: 502 Bad Gateway at 2026-03-13 14:30:15
[AI INSIGHT] Cluster of 502 errors indicates upstream service failure
[RECOMMENDATION] Check backend health at http://backend:8080/health
[EMAIL SENT] Alert dispatched to admin@example.com
```

### Ejemplo 3: Pruebas de Carga de API
**Prompt de Entrada:**
```
/dynamic-analyze api-test --url https://api.example.com/v1/products --scenarios scenarios.json --concurrency 50 --duration 300s --output api_report.json
```

**Salida Esperada:**
```
[INFO] Starting API load test with 50 concurrent users
[METRICS] Response time avg: 245ms, p95: 1200ms
[ERROR] 3% requests failed with 429 (Rate Limited)
[AI INSIGHT] Rate limiting triggered at 100 req/s - consider increasing limits or implementing backoff
[REPORT] Detailed results in api_report.json with latency charts
```

## Comandos de Reversión

### Reversión General
- Detener análisis en curso: `/dynamic-analyze stop --session-id <id>`
- Limpiar archivos temporales: `rm -rf $DYNAMIC_ANALYZER_OUTPUT_DIR/*`
- Revertir cambios del sistema (si los hay): `git checkout -- .` (para archivos de config modificados durante el análisis)

### Reversión Específica
- Para perfilado: `pkill -f \"dynamic-analyzer profile\"` y remover artefactos de perfil: `rm -f profile_*.json`
- Para análisis de logs: Terminar stream: `kill $(pgrep -f \"tail -f /var/log/app.log\")`
- Para pruebas de API: Cancelar carga: `/dynamic-analyze api-test --cancel <session-id>` y restaurar configs de API: `cp backup/api_config.json api_config.json`
- Para análisis de DB: Desconectar: `mysqladmin -u user -p shutdown` (si DB de prueba local) y restaurar esquema: `mysql -u user -p db < backup_schema.sql`
- Para monitoreo de contenedor: Detener monitoreo: `docker exec myapp pkill -f dynamic-monitor` y resetear métricas: `docker stats --no-stream myapp`

### Pasos de Verificación
- Confirmar no hay procesos en background: `ps aux | grep dynamic-analyzer`
- Verificar uso de disco: `du -sh $DYNAMIC_ANALYZER_OUTPUT_DIR`
- Validar integridad del sistema: `openclaw verify system --post-analysis`
- Revisar logs por errores: `grep \"ERROR\|WARN\" /var/log/openclaw/dynamic-analyzer.log`

### Solución de Problemas de Problemas Comunes
- **Timeout excedido**: Incrementar valor de `--timeout` o reducir `--concurrency`.
- **Permiso denegado**: Ejecutar con `sudo` o ajustar permisos de archivo (ej. `chmod 644 /var/log/app.log`).
- **Insights de IA no se generan**: Verificar estado de `ai-engine`: `openclaw status ai-engine`; reiniciar si es necesario: `openclaw restart ai-engine`.
- **Uso alto de recursos**: Reducir tasas de muestreo (ej. `--sample-rate 0.1`) o usar modo `--lightweight`.
- **Resultados inconsistentes**: Asegurar que el objetivo esté en estado estable; evitar ejecutar durante horas pico.
- **Errores de red en pruebas de API**: Usar `--proxy http://proxy.company.com:8080` o verificar resolución DNS.