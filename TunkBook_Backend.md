# TunkBook Backend

TunkBook Backend es el servicio principal de una plataforma digital de lectura y publicación de libros. El proyecto está desarrollado con Spring Boot y expone una API REST para lectores, autores y administradores.

El sistema cubre el ciclo completo de una plataforma de contenido: registro de usuarios, autenticación, gestión de libros, capítulos, categorías, lectura, favoritos, pedidos, pagos, panel de autor, panel de administración y generación de portadas con IA.

## Funcionalidades Principales

### Usuarios y autenticación

- Registro e inicio de sesión de usuarios.
- Inicio de sesión con Google OAuth.
- Verificación de correo electrónico.
- Recuperación y restablecimiento de contraseña.
- Gestión de sesión con Access Token y Refresh Token.
- Autenticación basada en cookies para facilitar la integración con el frontend.
- Roles diferenciados para lectores, autores y administradores.

### Catálogo de libros

- Consulta de libros recomendados y nuevos libros.
- Navegación por categorías y subcategorías.
- Búsqueda de libros por categoría.
- Visualización de detalle de libro.
- Comentarios de usuarios sobre libros.
- Gestión de portadas e imágenes asociadas.

### Capítulos y lectura

- Listado de capítulos por libro.
- Lectura de contenido de capítulos.
- Control de acceso a contenido gratuito o desbloqueado.
- Registro del progreso de lectura.
- Historial de lectura del usuario.
- Soporte para subida y extracción de contenido desde archivos como DOCX, PDF, EPUB, RTF u ODT mediante Apache Tika.

### Favoritos

- Creación de listas de favoritos.
- Consulta de listas personales.
- Añadir libros a una lista.
- Eliminar libros de una lista.
- Eliminación de listas de favoritos.

### Pedidos y economía interna

- Creación de pedidos asociados a libros o capítulos.
- Consulta de pedidos del usuario.
- Pago de pedidos.
- Eliminación de detalles de pedido.
- Sistema de paquetes de monedas para compras dentro de la plataforma.
- Registro de capítulos desbloqueados por usuario.

### Pagos con Stripe

- Creación de sesiones de pago.
- Verificación del estado de pago.
- Registro de cuentas Stripe para autores.
- Verificación de cuentas de autor.
- Solicitud de retiros.
- Registro de sesiones y estados relacionados con pagos.

### Panel de autor

- Consulta de información del autor autenticado.
- Conexión de datos de autor.
- Consulta de obras propias.
- Creación de libros.
- Subida de capítulos.
- Actualización de capítulos.
- Relación entre autores y libros publicados.

### Panel de administración

- Inicio de sesión específico para administradores.
- Consulta y mantenimiento de libros.
- Creación y actualización de libros.
- Consulta de capítulos por libro.
- Consulta del contenido de capítulos.
- Creación y actualización de capítulos desde el panel administrativo.
- Creación opcional de un administrador inicial al arrancar la aplicación.

### Inteligencia artificial

- Generación de portadas mediante Spring AI y OpenAI.
- Configuración del proveedor, modelo, tamaño de imagen, cantidad y tiempo máximo de generación.
- Almacenamiento temporal de resultados de portadas generadas.

### Recursos públicos

- Health check del servicio.
- Sitemap XML.
- Acceso público a imágenes subidas.
- Endpoint para obtener token CSRF.

## Stack Tecnológico

| Área | Tecnología |
| --- | --- |
| Lenguaje | Java 21 |
| Framework principal | Spring Boot 3.4.5 |
| API REST | Spring Web |
| Seguridad | Spring Security, JWT, CSRF, CORS |
| Persistencia | MyBatis-Plus, MyBatis XML Mapper |
| Base de datos | MySQL |
| Caché y sesiones | Redis |
| Pagos | Stripe Java SDK |
| Login externo | Google OAuth |
| Email | Spring Mail, Gmail SMTP |
| IA | Spring AI OpenAI |
| Procesamiento de archivos | Apache Tika |
| Build | Maven |
| Contenedores | Docker |
| Utilidades | Lombok, FastJSON, Hutool |

## Arquitectura del Proyecto

```text
tunkbook-web/
├── src/main/java/com/tunkbook/
│   ├── config/        Configuración de seguridad, CORS, recursos, IA y bootstrap admin
│   ├── controller/    Controladores REST
│   ├── CSFR/          Endpoint de token CSRF
│   ├── service/       Interfaces de negocio
│   ├── service/impl/  Implementaciones de negocio
│   ├── mapper/        Acceso a datos con MyBatis-Plus
│   ├── pojo/          Entidades principales del dominio
│   ├── dto/           Objetos de transferencia de datos
│   ├── filter/        Filtros de JWT, rate limit y request body
│   ├── ratelimit/     Reglas y modelos de limitación de peticiones
│   ├── constants/     Constantes del sistema
│   ├── utils/         Utilidades para JWT, archivos y validaciones
│   └── exception/     Excepciones de negocio
└── src/main/resources/
    ├── application.yml
    └── com/tunkbook/mapper/
        ├── BookMapper.xml
        ├── CategoryMapper.xml
        └── OrderMapper.xml
```

La aplicación sigue una arquitectura por capas:

1. Los controladores reciben las peticiones HTTP.
2. Los servicios concentran la lógica de negocio.
3. Los mappers conectan con MySQL mediante MyBatis-Plus y XML Mapper.
4. Redis se utiliza para sesiones activas, control de autenticación y limitación de peticiones.
5. Los filtros de seguridad procesan JWT, CSRF y reglas de rate limit antes de llegar a los controladores.

## Modelo de Dominio

El proyecto incluye entidades para representar las partes principales de una plataforma de lectura:

- `User`, `UserRoles`: usuarios y permisos.
- `Book`, `Chapter`, `Comment`: contenido principal.
- `Categories`, `SubCategories`, `BooksSubcategories`: clasificación de libros.
- `Favorite`, `FavoriteBooks`: listas personales de favoritos.
- `Order`, `OrderDetail`: pedidos y detalles de compra.
- `UserChapterUnlocks`: capítulos desbloqueados por usuario.
- `ReadingProgress`, `ReadingHistory`: seguimiento de lectura.
- `Writers`, `WritersBooks`, `WriterIncomeRecord`: módulo de autores.
- `StripeWriter`, `StripeSessions`: integración con Stripe.
- `AiCoverTemp`: resultados temporales de portadas generadas por IA.
- `Materials`, `Plan`: recursos y planes internos de la plataforma.

## Seguridad y Control de Acceso

Uno de los puntos importantes del proyecto es la separación de permisos por rol:

- `ROLE_LECTOR`: usuario lector autenticado.
- `ROLE_AUTOR`: autor con permisos para gestionar sus obras.
- `ROLE_ADMIN`: administrador con acceso a las rutas administrativas.

La autenticación utiliza un sistema de doble token:

- `TB_ACCESS`: token de corta duración para autenticar peticiones.
- `TB_REFRESH`: token de mayor duración para renovar sesión.

Además, el backend valida la sesión contra Redis. Esto permite invalidar sesiones desde el servidor aunque el JWT todavía no haya expirado.

El proyecto también incorpora:

- Protección CSRF mediante cookie `XSRF-TOKEN`.
- Configuración CORS para el frontend.
- Cifrado de contraseñas con BCrypt.
- Rate limit por IP, email, usuario, sesión o token según el tipo de operación.
- Reglas específicas para login, registro, verificación de email, recuperación de contraseña, pagos y pedidos.

## Endpoints Relevantes

| Módulo | Endpoints |
| --- | --- |
| Salud | `GET /`, `GET /health` |
| CSRF | `GET /csrf` |
| Autenticación | `POST /register`, `POST /login`, `POST /auth/refresh`, `GET /auth/check`, `POST /logout` |
| Google OAuth | `POST /google/auth` |
| Email | `POST /verify-email/me`, `POST /verify-email/resend` |
| Password reset | `POST /forgot-password/send-code`, `POST /forgot-password/verify-code`, `POST /forgot-password/reset` |
| Usuarios | `GET /users/info`, `PUT /users/info`, `PUT /users/info/avatar` |
| Categorías | `GET /categories`, `GET /categories/{categoriaName}/{subcategoriaName}` |
| Libros | `GET /books/recommen`, `GET /books/nuevo`, `GET /books/{bookId}` |
| Capítulos | `GET /books/{bookId}/chapters`, `GET /books/{bookId}/chapters/{id}/content` |
| Favoritos | `GET /favorites`, `POST /favorites`, `DELETE /favorites/{listId}` |
| Lectura | `GET /reading/me/{bookId}/{chapterId}`, `PUT /reading/update/{bookId}/{chapterId}` |
| Historial | `GET /reading/history/me`, `PUT /reading/history/{bookId}/{chapterId}` |
| Pedidos | `GET /order/me`, `POST /order/new/{bookId}`, `PUT /order/pay/{orderId}` |
| Stripe | `POST /stripe/pay`, `PUT /stripe/pay/verify`, `POST /stripe/pay/status` |
| Autor | `GET /writer/me`, `POST /writer/connect`, `GET /books/mis-obras` |
| Admin | `POST /admin/login`, `GET /admin/books`, `POST /admin/books`, `PUT /admin/books` |
| IA | `POST /ai/covers/generate` |
| SEO | `GET /sitemap.xml` |

## Puntos Destacados y Retos Técnicos

### 1. Autenticación con doble token y sesión en Redis

El proyecto no depende únicamente del JWT. Aunque el token sea válido, la sesión debe existir en Redis para que el usuario sea autenticado. Este diseño permite cerrar sesiones desde el backend, controlar sesiones activas y reducir riesgos en caso de tokens antiguos.

### 2. Separación clara de roles

Las rutas están organizadas según el tipo de usuario. Los lectores pueden leer, comprar, comentar y guardar favoritos; los autores pueden publicar y administrar sus obras; los administradores pueden gestionar contenido desde rutas protegidas.

### 3. Integración de pagos y economía interna

El backend combina pedidos internos, paquetes de monedas, capítulos desbloqueados y Stripe. Esto requiere coordinar estado de pago, verificación, sesiones externas y registros propios de la plataforma.

### 4. Subida y procesamiento de contenido

El sistema permite trabajar con archivos de capítulos y utiliza Apache Tika para extraer texto de formatos variados. Esto facilita que los autores suban contenido sin depender de un único formato rígido.

### 5. Rate limit por contexto

No todas las rutas se limitan igual. El sistema aplica reglas según IP, email, usuario, sesión o reset token. Esto es especialmente útil para login, registro, recuperación de contraseña y pagos.

### 6. Generación de portadas con IA

El módulo de portadas usa Spring AI con OpenAI y guarda resultados temporales. Es una funcionalidad diferenciadora porque conecta el flujo editorial con generación visual automática.

### 7. Backend preparado para frontend con cookies

El uso de cookies, CSRF y CORS está pensado para una aplicación web frontend separada. Esto permite mantener una integración más segura que guardar tokens manualmente en el cliente.

## Formato General de Respuesta

La mayoría de endpoints devuelve una estructura común:

```json
{
  "code": 1,
  "msg": "success",
  "data": {}
}
```

En errores, `code` cambia y `msg` contiene el motivo del fallo.

## Notas del Proyecto

- El backend está dentro del módulo `tunkbook-web`.
- El proyecto utiliza MyBatis-Plus junto con XML Mapper para consultas más específicas.
- La configuración principal está centralizada en `application.yml`.
- Los archivos subidos se exponen mediante rutas bajo `/uploads`.
- El repositorio contiene recursos de subida usados en desarrollo.
- Las credenciales reales de servicios externos no deben subirse al repositorio.
