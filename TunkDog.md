# TunkDog Backend

Backend de TunkDog , una API REST para una aplicacion movil enfocada en el cuidado de mascotas, registro de perros, autenticacion de usuarios y seguimiento de paseos.

El proyecto esta desarrollado con Spring Boot y combina autenticacion JWT, Redis, PostgreSQL/PostGIS, Google Sign-In, verificacion por email y subida de imagenes a Cloudinary.

## Descripcion Del Proyecto

TunkDog Backend proporciona los servicios necesarios para que un usuario pueda iniciar sesion, gestionar sus perros y registrar los paseos diarios con informacion de distancia, duracion y ruta.

El sistema esta pensado para una app movil donde cada usuario puede tener uno o varios perros, subir avatar para cada mascota, guardar historiales de paseo y consultar estadisticas diarias o mensuales.

## Funcionalidades Principales

- Login mediante codigo de verificacion por email.
- Login mediante Google ID Token.
- Generacion de Access Token y Refresh Token con JWT.
- Renovacion de tokens con rotacion de Refresh Token.
- Control de sesiones activas mediante Redis.
- Registro, consulta, actualizacion y eliminacion de perros.
- Subida de avatar de perro a Cloudinary.
- Conversion de imagenes a WebP antes de subirlas.
- Registro de paseos con distancia, duracion, hora de inicio, hora de fin y ruta.
- Almacenamiento de rutas usando GeoJSON y PostGIS.
- Consulta de estadisticas del dia: distancia total, duracion total, velocidad media y rutas.
- Consulta de estadisticas mensuales: distancia total, rango, imagen del rango y progreso.
- Eliminacion de usuario con limpieza de datos relacionados.

## Stack Tecnologico

| Area | Tecnologia |
| --- | --- |
| Lenguaje | Java 21 |
| Framework backend | Spring Boot 4.1.0 |
| API REST | Spring Web MVC |
| Seguridad | Spring Security, JWT, BCrypt |
| Login externo | Google ID Token |
| Persistencia | MyBatis-Plus |
| Base de datos | PostgreSQL |
| Datos geograficos | PostGIS |
| Cache y sesiones | Redis |
| Email | Spring Mail, Gmail SMTP |
| Imagenes | Cloudinary, cwebp, WebP |
| Utilidades | Lombok, Hutool |
| Build | Maven Wrapper |
| Deploy | Docker, Eclipse Temurin |

## Arquitectura General

El backend sigue una estructura por capas:

- `controller`: expone los endpoints REST.
- `service`: contiene la logica de negocio.
- `mapper`: accede a la base de datos mediante MyBatis-Plus.
- `dto`: define objetos de entrada y salida para la API.
- `pojo`: representa entidades principales y respuesta comun.
- `filter`: valida JWT antes de permitir acceso a endpoints protegidos.
- `config`: centraliza configuracion de seguridad y servicios externos.
- `utils`: agrupa utilidades de JWT, autenticacion, Google Token y validacion.

Flujo general de autenticacion:

```text
Cliente movil
    |
    | login por email o Google
    v
Auth Controller
    |
    | valida credenciales / codigo / token externo
    v
Service
    |
    | crea usuario si no existe
    | genera accessToken + refreshToken
    | guarda session y refresh token en Redis
    v
Cliente movil usa Authorization: Bearer <accessToken>
```

## Estructura Del Proyecto

```text
DogApp_Backend
|-- Dockerfile
|-- pom.xml
|-- mvnw / mvnw.cmd
|-- README.md
|-- src
|   |-- main
|   |   |-- java/com/tunkbook/dogapp
|   |   |   |-- config
|   |   |   |   |-- SecurityConfig.java
|   |   |   |   `-- CloudinaryConfig.java
|   |   |   |-- constant
|   |   |   |   |-- AuthConstant.java
|   |   |   |   |-- JwtClaimKeys.java
|   |   |   |   `-- RedisConstants.java
|   |   |   |-- controller
|   |   |   |   |-- AuthEmail.java
|   |   |   |   |-- AuthGoogle.java
|   |   |   |   |-- AuthTokenController.java
|   |   |   |   |-- DogController.java
|   |   |   |   |-- HealthController.java
|   |   |   |   `-- UserController.java
|   |   |   |-- dto
|   |   |   |-- exception
|   |   |   |-- filters
|   |   |   |   `-- JwtAuthenticationFilter.java
|   |   |   |-- mapper
|   |   |   |-- pojo
|   |   |   |-- service
|   |   |   |-- utils
|   |   |   `-- DogAppBackendApplication.java
|   |   `-- resources
|   |       `-- application.yml
|   `-- test
`-- target
```

## Modulos Principales

### Autenticacion

El proyecto implementa dos formas de login:

- Email: el usuario solicita un codigo, el backend lo envia por correo y lo guarda temporalmente en Redis.
- Google: el frontend envia un Google ID Token y el backend lo verifica con la libreria oficial de Google.

Despues de un login correcto, ambos flujos generan la misma estructura de respuesta:

- `accessToken`
- `refreshToken`
- `tokenType`
- `expireTime`
- informacion basica del usuario

### Seguridad Con JWT Y Redis

La seguridad no depende solo de validar la firma del JWT. Cada token contiene informacion como:

- `id`: identificador del usuario.
- `email`: correo del usuario.
- `sid`: identificador de sesion.
- `jti`: identificador unico del token.
- `tokenType`: tipo de token, `access` o `refresh`.

Redis se usa para comprobar que la sesion sigue activa y que el Refresh Token esta en la lista permitida. Esto permite invalidar sesiones, controlar expiraciones y evitar que un Refresh Token antiguo siga siendo valido despues de una renovacion.

### Gestion De Perros

Cada perro se guarda en la tabla `dogs`, y la relacion entre usuario y perro se guarda en `dog_members`. Esto permite separar la informacion del perro de la propiedad o acceso del usuario.

Funciones disponibles:

- Registrar perro.
- Listar perros del usuario autenticado.
- Consultar detalle de perro.
- Actualizar nombre o avatar.
- Eliminar perro si el usuario tiene rol `OWNER`.

### Subida Y Optimizacion De Imagenes

El backend valida el archivo recibido antes de subirlo:

- Tamano maximo permitido.
- Formatos soportados: JPEG, JPG, PNG y WebP.
- Conversion a WebP con `cwebp`.
- Redimensionado a 512px de ancho.
- Eliminacion de metadatos.
- Subida final a Cloudinary.

Este flujo reduce el peso de las imagenes y mejora el rendimiento en mobile.

### Paseos Y Rutas

Cada paseo incluye:

- perro asociado
- usuario que registra el paseo
- hora de inicio
- hora de fin
- distancia en metros
- duracion calculada
- lista de puntos de ruta

La lista de puntos se transforma en un GeoJSON `LineString` y se guarda con PostGIS usando `ST_GeomFromGeoJSON`. Para consultar rutas del dia, se convierte de nuevo con `ST_AsGeoJSON`.

### Estadisticas

El backend calcula estadisticas de paseo en dos niveles:

- Hoy: distancia total, duracion total, velocidad media y rutas del dia.
- Mes actual: distancia total, rango alcanzado, imagen del rango y porcentaje de progreso.

Las estadisticas usan la zona horaria `Europe/Madrid`, evitando errores cuando el servidor esta desplegado en otra region.

## Endpoints Principales

### Publicos

| Metodo | Endpoint | Descripcion |
| --- | --- | --- |
| GET | `/health` | Health check del servicio |
| POST | `/auth/email-code/request` | Enviar codigo de verificacion por email |
| POST | `/auth/email-code/verify` | Verificar codigo e iniciar sesion |
| POST | `/auth/google/verify` | Login con Google ID Token |
| POST | `/auth/refresh` | Renovar Access Token y Refresh Token |

### Protegidos

Estos endpoints requieren:

```http
Authorization: Bearer <accessToken>
```

| Metodo | Endpoint | Descripcion |
| --- | --- | --- |
| GET | `/dogs` | Listar perros del usuario |
| GET | `/dogs/{id}` | Obtener detalle de un perro |
| POST | `/dogs/register` | Registrar un nuevo perro |
| PATCH | `/dogs/{id}` | Actualizar perro |
| DELETE | `/dogs/{id}` | Eliminar perro |
| POST | `/dogs/{dogId}/walks` | Registrar un paseo |
| GET | `/dogs/{dogId}/walks/today` | Obtener resumen diario |
| GET | `/dogs/{dogId}/walks/month` | Obtener resumen mensual |
| DELETE | `/users/me` | Eliminar cuenta actual |

## Ejemplos De Requests

### Solicitar Codigo Por Email

```json
{
  "email": "user@example.com"
}
```

### Verificar Codigo

```json
{
  "email": "user@example.com",
  "code": "1234"
}
```

### Login Con Google

```json
{
  "idToken": "google_id_token"
}
```

### Registrar Perro

Endpoint:

```http
POST /dogs/register
Content-Type: multipart/form-data
```

Campos:

```text
dogName=Lucky
breed=Shiba Inu
gender=MALE
dogType=SMALL
ageMonths=18
avatarImage=<file>
```

### Registrar Paseo

```json
{
  "startedAt": "2026-08-16T09:00:00+02:00",
  "endedAt": "2026-08-16T09:30:00+02:00",
  "distanceMeters": 2500,
  "trackLine": [
    {
      "longitude": -3.7038,
      "latitude": 40.4168
    },
    {
      "longitude": -3.7045,
      "latitude": 40.4172
    }
  ]
}
```

## Respuesta Comun

La API usa un formato de respuesta unificado:

```json
{
  "code": 1,
  "msg": "success",
  "data": {}
}
```

En caso de error:

```json
{
  "code": 0,
  "msg": "ERROR_MESSAGE",
  "data": null
}
```

## Ejecucion Local

Compilar el proyecto:

```powershell
.\mvnw.cmd clean package
```

Ejecutar en local:

```powershell
.\mvnw.cmd spring-boot:run
```

Tambien se puede ejecutar directamente la clase:

```text
DogAppBackendApplication
```

## Docker

Construir imagen:

```powershell
docker build -t dogapp-backend .
```

Ejecutar contenedor:

```powershell
docker run -p 8080:8080 dogapp-backend
```

El Dockerfile usa multi-stage build:

- build stage con `eclipse-temurin:21-jdk`
- runtime stage con `eclipse-temurin:21-jre`
- instalacion de `webp` para poder ejecutar `cwebp`

## Dificultades Y Puntos Destacados

### 1. Autenticacion Con Doble Token

El sistema usa Access Token para peticiones normales y Refresh Token para renovar sesion. Cada Refresh Token queda registrado en Redis y se elimina al renovarse, evitando reutilizacion de tokens antiguos.

### 2. Sesiones Controladas Aunque JWT Sea Stateless

Aunque JWT normalmente es stateless, el backend guarda un `sid` en Redis. Asi se puede comprobar si una sesion sigue activa y bloquear accesos cuando la sesion expira o se elimina.

### 3. Integracion De Login Por Email Y Google

El proyecto soporta dos metodos de autenticacion distintos, pero ambos terminan usando la misma tabla de usuarios, la misma estructura JWT y el mismo modelo de sesion en Redis.

### 4. Rutas Geograficas Con PostGIS

Las rutas de paseo no se guardan como texto simple. El backend transforma puntos de latitud y longitud a GeoJSON LineString y los almacena como geometria PostGIS, dejando preparada la base para futuras funciones de mapa, distancia y busqueda geografica.

### 5. Estadisticas Con Zona Horaria De Negocio

Las consultas de hoy y del mes actual se calculan con `Europe/Madrid`, evitando errores de fecha cuando el servidor corre en otra zona horaria.

### 6. Procesamiento Real De Imagenes

Antes de subir imagenes, el backend valida formato y tamano, convierte a WebP, redimensiona y limpia archivos temporales. Esto mejora rendimiento y evita subir archivos innecesariamente pesados.

### 7. Control De Permisos Por Relacion Usuario-Perro

Las operaciones sensibles validan que el usuario tenga acceso al perro mediante `dog_members`. Para eliminar, ademas se exige rol `OWNER`.

## Posibles Mejoras Futuras

- Crear un handler global de excepciones para reducir `try/catch` repetidos en controladores.
- Anadir validaciones con Bean Validation en todos los DTO.
- Incorporar Flyway o Liquibase para versionar la base de datos.
- Agregar tests de integracion para autenticacion, perros y paseos.
- Implementar logout explicito e invalidacion de sesion.
- Unificar idioma de mensajes de error.
- Documentar la API con OpenAPI/Swagger.
