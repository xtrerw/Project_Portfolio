# TunkBook Frontend

Frontend de TunkBook, una plataforma web para lectores y autores construida con Vite, React y React Router. La aplicación permite descubrir libros, navegar por categorías, leer capítulos, iniciar sesión, gestionar el perfil de usuario, acceder al área de autor, publicar obras y preparar páginas optimizadas para SEO.

El backend se mantiene en un repositorio independiente:

- Sitio web: https://tunkbook.com/
- Backend: https://github.com/xtrerw/Project_TunkBook_Backend.git
- Frontend: https://github.com/xtrerw/Project_TunkBook_FrontEnd.git

## Stack tecnológico

- React 18
- Vite 6
- React Router 7
- Axios
- Google OAuth
- GSAP
- React Helmet Async
- Vercel Analytics
- ESLint
- Vercel Middleware

## Funcionalidades principales

- Página de inicio con navegación por categorías y subcategorías.
- Listado de libros por categoría.
- Página de detalle de libro.
- Lector de capítulos.
- Registro, inicio de sesión, verificación de email y recuperación de contraseña.
- Inicio de sesión con Google.
- Gestión de sesión con cookies, CSRF y renovación silenciosa del token.
- Perfil de usuario con favoritos, compras, historial y datos personales.
- Control de acceso para usuarios con rol de autor.
- Panel de autor, subida de libros y gestión de capítulos.
- Páginas de créditos y retorno de pago.
- Páginas legales: aviso legal, política de privacidad y política de cookies.
- SEO dinámico para libros y capítulos.
- Configuración de despliegue en Vercel con cabeceras de seguridad.

## Puntos destacados

- Arquitectura modular separada por dominios: autenticación, libros, autor, perfil, pagos, páginas legales y utilidades.
- Cliente HTTP centralizado con Axios para mantener una única lógica de API, cookies, CSRF y refresh token.
- Protección de rutas de autor mediante verificación contra el backend antes de permitir el acceso.
- Restauración automática de la sesión del usuario al recargar la página.
- Integración de Google OAuth dentro del flujo de autenticación.
- SEO preparado para una SPA mediante `react-helmet-async` y un middleware que inyecta metadatos dinámicos en rutas públicas de libros.
- Configuración de seguridad en producción con CSP, HSTS, `X-Frame-Options`, `Referrer-Policy` y otras cabeceras.
- Diseño responsive con navegación adaptada a escritorio y móvil.

## Retos técnicos

- Mantener una sesión segura usando cookies HttpOnly, CSRF y peticiones con credenciales entre frontend y backend.
- Evitar múltiples llamadas simultáneas de refresh token cuando varias peticiones reciben `401` al mismo tiempo.
- Resolver el SEO de páginas dinámicas en una aplicación React renderizada principalmente en cliente.
- Proteger el área de autor sin confiar únicamente en el estado del frontend.
- Coordinar rutas públicas, rutas privadas y rutas especiales como lectura de capítulos, pagos y páginas legales.
- Mantener una experiencia responsive en menús desplegables, cabecera, footer y páginas de lectura.
- Ajustar la Content Security Policy para permitir solo los recursos necesarios sin romper Google OAuth, Analytics, imágenes ni llamadas al backend.

## Instalación local

### 1. Instalar dependencias

```bash
npm install
```

### 2. Configurar variables de entorno

Copia el archivo de ejemplo:

```bash
cp .env.example .env.development.local
```

Edita `.env.development.local` según tu entorno:

```env
VITE_API_URL=http://localhost:5001
VITE_GOOGLE_CLIENT_ID=your-google-client-id
VITE_GA_MEASUREMENT_ID=G-XXXXXXX
VITE_FRONTEND_URL=http://localhost:5173
```

| Variable | Descripción |
| --- | --- |
| `VITE_API_URL` | URL base del backend |
| `VITE_GOOGLE_CLIENT_ID` | Client ID de Google OAuth |
| `VITE_GA_MEASUREMENT_ID` | ID de medición de Google Analytics |
| `VITE_FRONTEND_URL` | URL pública del frontend para canonical, Open Graph e imágenes absolutas |

### 3. Iniciar servidor de desarrollo

```bash
npm run dev
```

Dirección local por defecto:

```text
http://localhost:5173
```

El servidor de Vite está configurado con `host: 0.0.0.0`, por lo que también puede probarse desde otros dispositivos dentro de la misma red.

## Scripts disponibles

```bash
npm run dev
```

Inicia el entorno de desarrollo.

```bash
npm run build
```

Genera la versión de producción en la carpeta `dist`.

```bash
npm run preview
```

Previsualiza localmente el resultado de producción.

```bash
npm run lint
```

Ejecuta la revisión de código con ESLint.

## Estructura del proyecto

```text
src/
  api/                  Instancia de Axios, CSRF y refresh token
  app/                  Entrada principal de React y rutas raíz
  componentes/          Componentes globales reutilizables
  context/              Contexto de usuario y restauración de sesión
  img/                  Recursos gráficos del proyecto
  style/                Estilos globales y responsive
  usuario/
    auth/               Login, registro, verificación y recuperación
    autor/              Panel de autor, subida de obras y capítulos
    credits/            Página de créditos
    layout/             Home, navegación, footer y 404
    legal/              Aviso legal, privacidad y cookies
    libros/             Categorías, detalle de libro y lector
    payment/            Páginas de retorno y éxito de pago
    perfil/             Perfil de usuario
  utils/                SEO, validaciones, fechas y componentes auxiliares
```

Archivos importantes en la raíz:

| Archivo | Descripción |
| --- | --- |
| `vite.config.js` | Configuración de Vite |
| `vercel.json` | Rewrites, fallback SPA y cabeceras de seguridad |
| `middleware.js` | Inyección dinámica de SEO para libros y capítulos |
| `.env.example` | Ejemplo de variables de entorno |
| `eslint.config.js` | Configuración de ESLint |

## API y autenticación

La comunicación con el backend se centraliza en `src/api/api.js`:

- `baseURL` se obtiene desde `VITE_API_URL`.
- Las peticiones envían cookies con `withCredentials: true`.
- El token CSRF se lee desde la cookie `XSRF-TOKEN`.
- El token CSRF se envía en la cabecera `X-XSRF-TOKEN`.
- Al cargar la aplicación se inicializa CSRF y se comprueba la sesión con `/auth/check`.
- Si una petición recibe `401`, se intenta renovar la sesión mediante `/auth/refresh`.
- Mientras hay una renovación en curso, las demás peticiones quedan en cola y se reintentan después.
- Si la renovación falla, el usuario es redirigido a `/login`.

Para que este flujo funcione correctamente, el backend debe tener configurados CORS, cookies, CSRF y refresh token de forma compatible con el dominio del frontend.

## Rutas principales

| Ruta | Página |
| --- | --- |
| `/` | Inicio |
| `/:categoriaName/:subcategoriaName` | Libros por categoría y subcategoría |
| `/login` | Login y registro |
| `/verify-email` | Verificación de email |
| `/reset-password` | Recuperación de contraseña |
| `/perfil` | Perfil de usuario |
| `/autor` | Panel de autor |
| `/autor/libro/nuevo` | Subir nuevo libro |
| `/autor/libro/rapido` | Escritura rápida |
| `/autor/mis-libros/:bookId/capitulos` | Gestión de capítulos |
| `/libros/:id` | Detalle de libro |
| `/libros/:bookId/capitulos/:contenidoId` | Lectura de capítulo |
| `/credits` | Créditos |
| `/success` | Pago completado |
| `/stripe/return` | Retorno de Stripe |
| `/aviso-legal` | Aviso legal |
| `/politica-privacidad` | Política de privacidad |
| `/cookies` y `/cookie-policy` | Política de cookies |

Las rutas del área de autor verifican el permiso de escritor antes de renderizar el contenido.

## SEO y despliegue

El proyecto trabaja el SEO en dos niveles:

- En el frontend, `react-helmet-async` permite definir metadatos desde los componentes.
- En Vercel, `middleware.js` genera HTML con title, description, canonical, Open Graph y Twitter Card para `/libros/:id` y `/libros/:bookId/capitulos/:contenidoId`.

Variables recomendadas para producción:

```env
VITE_API_URL=https://xxx.com
VITE_FRONTEND_URL=https://tunkbook.com
VITE_GOOGLE_CLIENT_ID=your-production-google-client-id
VITE_GA_MEASUREMENT_ID=G-XXXXXXX
```

`vercel.json` incluye:

- Fallback de SPA hacia `index.html`.
- Reescritura de `/sitemap.xml` hacia el backend.
- Content Security Policy.
- Cabeceras de seguridad como HSTS, `X-Frame-Options`, `X-Content-Type-Options` y `Referrer-Policy`.

Si cambia el dominio del backend, el dominio de imágenes o los servicios externos, también debe actualizarse la CSP en `vercel.json`.

## Buenas prácticas de desarrollo

- No subir archivos `.env` reales ni claves privadas.
- Reutilizar la instancia de Axios de `src/api/api.js` para nuevas llamadas al backend.
- Validar en backend los permisos sensibles, aunque el frontend también proteja rutas.
- Revisar SEO, navegación y responsive al añadir páginas nuevas.
- Antes de publicar, ejecutar:

```bash
npm run lint
npm run build
```

## Estado del proyecto

El proyecto continúa en desarrollo activo. Este README refleja la estructura y funcionalidades actuales del frontend, y debe actualizarse cuando se añadan nuevos módulos, variables de entorno, rutas o cambios relevantes de despliegue.
