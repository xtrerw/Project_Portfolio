# TunkDog

TunkDog es una aplicacion movil creada con Expo y React Native para gestionar perfiles de perros, registrar paseos al aire libre y consultar datos de actividad. La app permite iniciar sesion con email o Google, crear fichas de perros, grabar rutas GPS, visualizar mapas, revisar la ruta del dia, cambiar idioma y gestionar la cuenta del usuario.

El nombre configurado de la app movil es `TunkDog` y la version se mantiene en `app.json`.

## Funcionalidades principales

- Registro de paseos: permite elegir un perro y registrar ruta, distancia, duracion y velocidad en tiempo real.
- Mapas y rutas: usa MapLibre para mostrar el mapa, la ubicacion actual, la ruta activa y la ruta diaria del perro.
- Ubicacion en segundo plano: con permisos concedidos, puede continuar registrando un paseo aunque la app este bloqueada o en segundo plano.
- Perfiles de perros: permite crear, listar, consultar, editar nombre y cambiar foto de cada perro.
- Datos de actividad: muestra distancia diaria, tiempo, velocidad media, rango mensual y ruta de hoy.
- Autenticacion: soporta login con codigo por email, Google Sign-In, restauracion de sesion, cierre de sesion y eliminacion de cuenta.
- Multidioma: incluye espanol, ingles y chino, con opcion de seguir el idioma del sistema.
- Informacion legal y ayuda: incluye pantallas de privacidad, terminos de uso, ayuda y preguntas frecuentes.

## Stack tecnico

- Expo SDK 54
- React Native 0.81
- React 19
- TypeScript
- Expo Router
- Axios
- MapLibre React Native
- Expo Location y Expo Task Manager
- Expo SecureStore y AsyncStorage
- Expo Image Picker
- Google Sign-In
- Lucide React Native
- EAS Build

Expo SDK 54 requiere como minimo Node.js `20.19.x` y esta alineado con React Native `0.81` y React `19.1.0`. Mas informacion en la [documentacion oficial de Expo SDK 54](https://docs.expo.dev/versions/v54.0.0/).

## Estructura del proyecto

```text
Dog-app-movil/
├─ app/                         Paginas y rutas de Expo Router
│  ├─ _layout.tsx               Layout raiz con auth, i18n y sesion de paseo
│  ├─ (tabs)/                   Navegacion inferior
│  │  ├─ index.tsx              Pantalla principal de paseo
│  │  ├─ profile.tsx            Seccion Mi Perro
│  │  ├─ account.tsx            Seccion Mi Cuenta
│  │  └─ login.tsx              Login por email
│  ├─ dogs/
│  │  ├─ register.tsx           Registro de perro
│  │  └─ [id].tsx               Detalle de perro
│  ├─ account-settings.tsx      Ajustes de cuenta
│  ├─ help.tsx                  Ayuda
│  ├─ privacy.tsx               Politica de privacidad
│  └─ terms.tsx                 Terminos de uso
├─ src/
│  ├─ context/                  Estado global de auth y paseo
│  ├─ screens/                  Pantallas de negocio
│  │  ├─ gps/                   GPS, mapa y registro de paseo
│  │  ├─ dog/                   Lista, registro y detalle de perros
│  │  └─ user/                  Login, cuenta, ajustes e informacion
│  ├─ lib/                      API, storage, Google auth, background location y metricas
│  ├─ i18n/                     Traducciones y preferencia de idioma
│  └─ theme/                    Fuentes y tema visual
├─ assets/                      Imagenes, iconos y fuentes
├─ plugins/                     Plugins de configuracion de Expo
├─ android/                     Proyecto nativo Android
├─ app.json                     Configuracion estatica de Expo
├─ app.config.js                Configuracion dinamica de Expo
├─ eas.json                     Configuracion de EAS Build
├─ package.json                 Dependencias y scripts
└─ tsconfig.json                Configuracion de TypeScript
```

## Variables de entorno

Configura las variables en `.env.local` para desarrollo local o en el entorno de EAS para builds. No subas valores reales al repositorio. Esta tabla solo indica nombre y uso.

| Variable | Uso |
| --- | --- |
| `EXPO_PUBLIC_API_URL` | URL base del backend usada por Axios para autenticacion, perros y paseos. |
| `EXPO_PUBLIC_GOOGLE_CLIENT_ID` | Web Client ID utilizado por Google Sign-In. |
| `GOOGLE_MAPS_API_KEY` | API Key de Google Maps para la configuracion nativa de Android, inyectada desde `app.config.js`. |

Importante: las variables con prefijo `EXPO_PUBLIC_` se incluyen en el cliente final. No deben contener secretos de servidor, tokens privados ni credenciales que no puedan exponerse. Las claves de mapas y login deben restringirse desde las consolas de cada proveedor.

Ejemplo local:

```env
EXPO_PUBLIC_API_URL=<url-base-del-backend>
EXPO_PUBLIC_GOOGLE_CLIENT_ID=<google-web-client-id>
GOOGLE_MAPS_API_KEY=<google-maps-api-key-android>
```

## Instalacion y ejecucion

Instalar dependencias:

```bash
npm install
```

Iniciar el servidor de desarrollo:

```bash
npm run start
```

Ejecutar en Android con development build:

```bash
npm run android
```

Ejecutar en iOS con development build:

```bash
npm run ios
```

Ejecutar en Web:

```bash
npm run web
```

Revisar lint:

```bash
npm run lint
```

Como el proyecto usa capacidades nativas como Google Sign-In, MapLibre y ubicacion en segundo plano, se recomienda probar la funcionalidad completa con una development build. Expo Go puede servir para vistas basicas, pero no cubre todo el comportamiento nativo.

## Builds con EAS

`eas.json` define dos perfiles principales:

- `preview`: build interna para pruebas. En Android genera un APK.
- `production`: build para entorno de produccion.

Antes de construir, confirma que las variables de entorno necesarias esten configuradas en EAS, especialmente la URL del backend, el Client ID de Google y la clave de mapas.

## Dependencias del backend

La app movil depende del backend para:

- Comprobar autenticacion y refrescar tokens.
- Enviar y verificar codigos de login por email.
- Verificar el token de Google Sign-In.
- Listar, registrar, editar y eliminar perros.
- Guardar paseos y obtener datos del detalle de perro.
- Eliminar la cuenta del usuario.

Los tokens de autenticacion se guardan en almacenamiento seguro del dispositivo. Cuando una peticion recibe `401`, el cliente intenta refrescar el access token de forma automatica.

## Permisos

- Ubicacion: se usa para iniciar paseos, centrar el mapa y calcular distancia, tiempo y velocidad.
- Ubicacion en segundo plano: se usa para continuar registrando la ruta mientras el paseo sigue activo.
- Fotos: se usa para seleccionar o cambiar la foto de perfil de un perro.

La app incluye textos de permisos y politica de privacidad. Antes de publicar, conviene revisar que `app.json`, las pantallas legales y la informacion enviada a las tiendas sean consistentes.

## Dificultades y puntos destacados

- Control de calidad GPS: filtra puntos con baja precision, distancias demasiado pequenas y velocidades anormales para reducir ruido en la ruta.
- Registro continuo en primer y segundo plano: usa Expo Location y Expo Task Manager para mantener el estado del paseo y seguir capturando puntos.
- Visualizacion de rutas: convierte puntos GPS en GeoJSON LineString para renderizar rutas en MapLibre.
- Gestion segura de sesion: guarda tokens con SecureStore y centraliza el refresco de access token mediante interceptores de Axios.
- Arquitectura multidioma: detecta idioma del sistema, permite preferencia manual y persiste la seleccion del usuario.
- Organizacion con Expo Router: separa tabs, pantallas de detalle, ajustes e informacion usando rutas basadas en archivos.
- Experiencia orientada al usuario: combina perfil del perro, datos diarios, rango mensual y mapa para dar valor despues de cada paseo.

## Notas de desarrollo

- Si cambias configuracion de Expo o capacidades nativas, normalmente necesitas regenerar o reinstalar la development build.
- La ubicacion en segundo plano debe validarse en dispositivo real.
- La subida de avatar usa `multipart/form-data`; el backend debe soportar el campo esperado.
- `EXPO_PUBLIC_API_URL` debe usar HTTPS en produccion.
- Al cambiar textos de permisos, revisa tambien la politica de privacidad y los metadatos de publicacion.
