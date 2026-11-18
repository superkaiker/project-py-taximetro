# TaxiTech Solutions 🚕

**Proyecto:** TTX-247  
**Desarrollo:** Taxímetro digital para flota de taxis  
**Ubicación:** Madrid, España

![Python](https://img.shields.io/badge/Python-3.10-blue)
![Fases](https://img.shields.io/badge/Fases-4%20implementadas-success)
![API](https://img.shields.io/badge/API-FastAPI-009688)
![Database](https://img.shields.io/badge/Database-SQLite-blue)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED)

## 1. Descripción del proyecto

TaxiTech Solutions es una aplicación de taxímetro digital desarrollada en Python para gestionar carreras, calcular tarifas, conservar el historial de servicios y facilitar la supervisión del sistema.

El proyecto evoluciona desde una primera versión funcional de consola hasta una arquitectura modular con interfaz gráfica, autenticación, API REST, persistencia de datos y despliegue mediante Docker.

## 2. Tarifas

| Estado del vehículo | Tarifa |
|---|---:|
| Parado | 0,02 €/segundo |
| En movimiento | 0,05 €/segundo |

Las tarifas se configuran externamente mediante `config.json`. El cambio entre estados se realiza desde las interfaces disponibles; el proyecto no utiliza un sensor de velocidad.

## 3. Desarrollo por fases

### Fase 1. MVP funcional

- Inicio y finalización de carreras.
- Cambio entre estados parado y movimiento.
- Cálculo automático de tarifas.
- Encadenamiento de múltiples carreras.
- Interfaz de consola.

**Estado: completada.**

### Fase 2. Observabilidad y persistencia

- Registro de eventos mediante logs.
- Persistencia del historial en JSON.
- Conservación de fecha, duración e importe.
- Configuración externa de tarifas.
- Pruebas automatizadas.

**Estado: completada.**

### Fase 3. Arquitectura y experiencia de usuario

- Programación orientada a objetos.
- Separación de responsabilidades en módulos.
- Interfaz gráfica.
- Autenticación mediante contraseña.
- Organización del código para facilitar su mantenimiento.

**Estado: completada.**

### Fase 4. Preparación para producción

- Base de datos relacional SQLite para la API.
- API REST desarrollada con FastAPI.
- Panel web de administración del historial.
- Migración inicial del historial JSON a SQLite.
- Despliegue mediante Docker Compose.
- Persistencia de datos mediante montajes de directorios del anfitrión.

**Estado: implementada y verificada funcionalmente.**

## 4. Tecnologías utilizadas

- Python 3.10.
- FastAPI.
- Uvicorn.
- Pydantic.
- SQLite.
- JSON.
- HTML y JavaScript.
- Docker y Docker Compose.
- Git y GitHub.
- unittest (pruebas automatizadas).

## 5. Estructura del proyecto

```text
project-py-taximetro/
│
├── main.py
├── taximeter.py
├── api.py
├── config.json
├── requirements.txt
├── Dockerfile
├── compose.yaml
├── .dockerignore
├── .gitignore
├── README.md
│
├── taximetro/
│   ├── __init__.py
│   ├── carrera.py
│   ├── taximetro.py
│   ├── configuracion.py
│   ├── persistencia.py
│   ├── persistencia_sqlite.py
│   ├── autenticacion.py
│   └── logging_config.py
│
├── interfaces/
│   ├── __init__.py
│   ├── consola.py
│   └── grafica.py
│
├── templates/
│   └── admin.html
│
├── tests/
│   ├── test_taximeter.py
│   └── test_autenticacion.py
│
├── data/
│   ├── historial.json
│   └── taxitech.db
│
└── logs/
    └── taximetro.log
```

Los directorios `data/` y `logs/` contienen información persistente y se excluyen del control de versiones. Se crean o utilizan durante la ejecución.

## 6. Instalación y ejecución local

### Requisitos

- Python 3.10 o superior.
- Git.

### Clonar el repositorio

```powershell
git clone https://github.com/superkaiker/project-py-taximetro.git
cd project-py-taximetro
```

### Crear y activar entorno virtual

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

### Instalar dependencias

```powershell
pip install -r requirements.txt
```

### Ejecutar la aplicación

Interfaz gráfica (por defecto):

```powershell
python .\main.py
```

Consola:

```powershell
python .\main.py --consola
```

API REST:

```powershell
uvicorn api:app --reload
```

### Ejecutar las pruebas

Las pruebas están escritas con el módulo `unittest`, incluido en Python:

```powershell
python -m unittest discover -s tests -v
```

## 7. API REST

La API permite gestionar carreras y consultar el historial.

| Método | Endpoint | Funcionalidad |
|---|---|---|
| GET | `/health` | Comprobar estado del servicio |
| POST | `/api/carreras` | Iniciar carrera |
| PATCH | `/api/carreras/estado` | Cambiar estado |
| POST | `/api/carreras/finalizar` | Finalizar y guardar carrera |
| GET | `/api/carreras` | Consultar historial |

Las operaciones de gestión de carreras y consulta del historial están protegidas mediante autenticación HTTP Basic. El endpoint `/health` es público para facilitar la comprobación del servicio. El panel web (`/admin`) entrega la página y solicita credenciales cuando se consulta el historial protegido.

### Documentación interactiva

Con el servidor iniciado:

- Swagger UI: `http://localhost:8000/docs`
- OpenAPI: `http://localhost:8000/openapi.json`
- Panel web: `http://localhost:8000/admin`
- Estado del servicio: `http://localhost:8000/health`

## 8. Persistencia de datos

El proyecto utiliza dos mecanismos de persistencia según la interfaz:

- **Consola y gráfica:** almacenan y consultan el historial en `data/historial.json`.
- **API:** utiliza SQLite, en `data/taxitech.db`, para guardar y consultar las carreras.

Al iniciar la API, se inicializa SQLite y se intenta migrar el historial JSON existente. Esta migración se realiza como parte del arranque de la API. Después de la migración, la API trabaja con SQLite; las interfaces de consola y gráfica continúan trabajando con JSON.

Cada carrera conserva:

- Fecha y hora de inicio.
- Duración en segundos.
- Importe calculado en euros.

El archivo JSON original se conserva como historial de las interfaces de escritorio y como fuente de la migración inicial de la API. Los historiales de ambas vías no se sincronizan automáticamente después de esa migración.

## 9. Sistema de logs

Los registros de operación se almacenan en:

```text
logs/taximetro.log
```

Se registran eventos de operación y errores, incluyendo el inicio del servicio y las operaciones de persistencia.

## 10. Despliegue con Docker

Docker Compose permite construir y ejecutar la API con un único comando.

### Iniciar la aplicación

```powershell
docker compose up -d --build
```

### Consultar el estado

```powershell
docker compose ps
```

### Consultar los logs

```powershell
docker compose logs -f
```

### Reiniciar el servicio

```powershell
docker compose restart
```

### Detener el servicio

```powershell
docker compose down
```

El archivo `compose.yaml` monta `./data` en `/app/data` y `./logs` en `/app/logs`. Son montajes de directorios del anfitrión (bind mounts), no volúmenes Docker nombrados. Permiten conservar los datos y logs del proyecto al reiniciar o recrear el contenedor.

## 11. Pruebas automatizadas

Las pruebas automatizadas están implementadas con `unittest`.

Ejecución:

```powershell
python -m unittest discover -s tests -v
```

**Último resultado ejecutado y verificado: 26 pruebas superadas.**

```text
Ran 26 tests
OK
```

Las pruebas cubren, entre otros aspectos:

- Cálculo de tarifas en parado y movimiento.
- Cambios de estado y estados inválidos.
- Independencia y encadenamiento de carreras.
- Gestión de una carrera activa en el taxímetro.
- Guardado y formato JSON del historial.
- Configuración ausente o con valores inválidos.
- Creación y verificación de credenciales.

También se realizaron pruebas funcionales de la API y del despliegue Docker, incluyendo el inicio, cambio de estado y finalización de una carrera, consulta del historial y comprobación de la persistencia tras reiniciar el contenedor.

## 12. Demostración funcional

Durante la prueba funcional del despliegue Docker se verificó:

- Servicio operativo en el puerto 8000.
- API REST accesible.
- Panel web de administración operativo.
- Inicio, cambio de estado y finalización de una carrera mediante la API.
- Registro de la carrera en SQLite y consulta posterior del historial.
- Persistencia del historial después de reiniciar el contenedor.

En la última demostración registrada, la carrera finalizada tenía un importe de 7,38 € y figuraba en el historial después del reinicio del contenedor.

## 13. Entregables

Cada fase contempla:

- Código fuente actualizado en GitHub.
- Demostración funcional.
- Pruebas correspondientes.
- Actualización del tablero Kanban de GitHub Projects.

## 14. Repositorio y documentación

- [Repositorio TaxiTech](https://github.com/superkaiker/project-py-taximetro)
- [Documentación Python](https://docs.python.org/3/)
- [Documentación FastAPI](https://fastapi.tiangolo.com/)
- [Documentación Docker](https://docs.docker.com/)

---

**TaxiTech Solutions | Proyecto TTX-247**

Desarrollo incremental de un taxímetro digital en Python.
