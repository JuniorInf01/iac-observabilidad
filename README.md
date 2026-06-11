# Monitoreo

En este laboratorio exploraremos monitoreo con herramientas disponibles


## Aplicaciones
```bash
docker compose up -d --build
```

## Servicios y URLs
| Servicio       | URL                         | Notas                                  |
|----------------|-----------------------------|----------------------------------------|
| Frontend       | http://localhost:8080       | Hello World + botones de tráfico/carga |
| Backend (API)  | http://localhost:3001       | `/api/hello`, `/metrics`, `/load`      |
| Grafana        | http://localhost:3000       | admin / admin                          |
| Prometheus     | http://localhost:9090       | datasource ya provisionado             |
| Loki           | http://localhost:3100       | datasource ya provisionado             |
| Alloy (UI)     | http://localhost:12345      | estado del recolector de logs          |
| cAdvisor       | http://localhost:8081       | métricas por contenedor                |
| node-exporter  | http://localhost:9100/metrics | métricas del host                    |

## Configuraciones
- **Datasources** Prometheus y Loki (provisionados automáticamente).
- Logs etiquetados por Alloy con `tier=application` o `tier=infrastructure`.

## Actividad
- El **dashboard** (paneles de CPU + logs de app e infra).
- La **alarma** de CPU > 50%.

## Reset
```bash
docker compose down -v   # borra también dashboards/alarmas creados
```

> Nota de versiones: el tag `prom/prometheus:latest` apunta aún a la rama 2.x (LTS),
> por eso fijamos `v3.8.1`. Promtail EOL (2026-03-02); el recolector de logs
> es Grafana Alloy.

## Preguntas a responder:

1. ¿Por qué necesitamos Loki además de Prometheus si ya tenemos `/metrics`?

Porque Prometheus solo guarda números en el tiempo (métricas), lo que nos dice qué está pasando. Sin embargo, no guarda texto. Necesitamos Loki para leer los mensajes exactos de esos errores (logs) y encontrar el error.

2. ¿Qué ventaja aporta que las fuentes de datos de Grafana estén aprovisionadas como código y no creadas a mano?

Automatización y cero errores humanos. Si el servidor se cae o comparto el proyecto con alguien, Grafana ya arranca conectado a Prometheus y Loki al momento, sin necesidad de entrar a hacer configuraciones manuales.

3. El panel "CPU contenedor" y el panel "CPU host" pueden mostrar valores muy distintos. ¿Por qué? ¿Cuál usarías para alertar sobre una aplicación concreta?

El panel de CPU del host muestra el uso de toda la máquina, incluyendo el sistema operativo, otros contenedores y procesos. El panel del contenedor muestra solo lo que consume esa aplicación específica. Para alertar sobre una app, usaría la del contenedor para evitar falsas alarmas provocadas por la máquina.

4. ¿Qué diferencia hay entre el *evaluation interval* y el *pending period* de una alarma?

El "evaluation interval" define cada cuánto Grafana revisa si la condición se cumple. El "pending period" define cuánto tiempo debe cumplirse seguido antes de disparar la alarma. Por ejemplo: revisar cada 10s, pero solo disparar si lleva 30s por encima del umbral. Esto evita falsas alarmas por picos momentáneos.
