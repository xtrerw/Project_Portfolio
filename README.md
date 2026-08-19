# Wei Xu --- Software Developer Portfolio

Software developer focused on **Java, Spring Boot, React, React Native,
database systems, security, and production deployment**.

I build applications from scratch, from backend architecture and
database design to frontend/mobile development, authentication,
security, integrations, and deployment.

------------------------------------------------------------------------

# Featured Projects

## TunkBook

**TunkBook** is a web platform for readers and authors. It provides a
complete content platform covering user accounts, authentication, books,
chapters, reading, favorites, comments, author publishing,
administration, payments, and AI-powered cover generation.

The application is built as separate frontend and backend services and
is deployed in a production environment.

### Project Value

TunkBook covers the complete lifecycle of a content platform:

-   User registration and authentication
-   Reader, author, and administrator roles
-   Book and chapter management
-   Reading and reading history
-   Favorites and comments
-   Author publishing workflow
-   Orders and chapter unlocking
-   Stripe payments and author payment accounts
-   Email verification and password recovery
-   Secure session management
-   Rate limiting
-   Dynamic SEO
-   AI-generated book covers
-   Production deployment

The project combines application development with real-world concerns
such as authentication, authorization, security, payment processing,
session management, SEO, file processing, and deployment.

### Frontend

The frontend is built with React and Vite and provides the user-facing
experience for readers and authors.

**Technology Stack**

-   React 18
-   Vite 6
-   React Router 7
-   Axios
-   Google OAuth
-   GSAP
-   React Helmet Async
-   Vercel Analytics
-   ESLint
-   Vercel Middleware

**Main Features**

-   Homepage with categories and subcategories
-   Book listings
-   Book detail pages
-   Chapter reader
-   User registration and login
-   Email verification
-   Password recovery
-   Google login
-   Cookie-based session management
-   CSRF protection
-   Silent token refresh
-   User profile
-   Favorites
-   Purchases
-   Reading history
-   Author access control
-   Author dashboard
-   Book and chapter publishing
-   Payment return pages
-   Legal pages
-   Dynamic SEO for books and chapters
-   Responsive design
-   Production security headers

### Frontend Architecture

The frontend is organized into separate areas for authentication, books,
authors, profiles, payments, legal pages, utilities, and shared
components.

``` text
src/
├── api/                  Axios, CSRF and refresh-token logic
├── app/                  Application entry point and root routes
├── componentes/          Shared reusable components
├── context/              User context and session restoration
├── img/                  Project assets
├── style/                Global and responsive styles
├── usuario/
│   ├── auth/             Login, registration and account recovery
│   ├── autor/            Author dashboard and publishing
│   ├── credits/          Credits
│   ├── layout/           Home, navigation and footer
│   ├── legal/            Legal pages
│   ├── libros/           Categories, books and reader
│   ├── payment/          Payment pages
│   └── perfil/           User profile
└── utils/                SEO, validation, dates and utilities
```

### Frontend Security and Authentication

The API communication is centralized through Axios.

-   Requests use `withCredentials: true`
-   CSRF is initialized and sent through the appropriate cookie/header
    flow
-   The application checks the user session through `/auth/check`
-   `401` responses trigger an automatic refresh flow
-   Multiple simultaneous refresh requests are coordinated
-   Failed refresh operations return the user to the login page
-   Author routes verify permissions with the backend instead of relying
    only on frontend state

### Frontend SEO and Deployment

The application uses `react-helmet-async` for component-level metadata.

Vercel middleware dynamically generates SEO information for public book
and chapter pages, including:

-   Title
-   Description
-   Canonical URL
-   Open Graph metadata
-   Twitter Card metadata

The Vercel deployment also includes:

-   SPA fallback
-   Sitemap rewrite
-   Content Security Policy
-   HSTS
-   `X-Frame-Options`
-   `X-Content-Type-Options`
-   `Referrer-Policy`

**Production:** https://tunkbook.com/

### Backend

The TunkBook backend is a REST API developed with Spring Boot.

It provides services for readers, authors, and administrators and
handles authentication, authorization, books, chapters, reading,
favorites, orders, payments, AI cover generation, and administration.

**Technology Stack**

  Area               Technology
  ------------------ ----------------------------------
  Language           Java 21
  Framework          Spring Boot 3.4.5
  API                Spring Web
  Security           Spring Security, JWT, CSRF, CORS
  Persistence        MyBatis-Plus, MyBatis XML Mapper
  Database           MySQL
  Cache & Sessions   Redis
  Payments           Stripe Java SDK
  External Login     Google OAuth
  Email              Spring Mail, Gmail SMTP
  AI                 Spring AI / OpenAI
  File Processing    Apache Tika
  Build              Maven
  Containers         Docker

### Backend Architecture

The backend follows a layered architecture:

``` text
src/main/java/com/tunkbook/
├── config/        Security, CORS, resources, AI and admin configuration
├── controller/    REST controllers
├── CSFR/          CSRF token endpoint
├── service/       Business interfaces
├── service/impl/  Business implementations
├── mapper/        MyBatis-Plus data access
├── pojo/          Domain entities
├── dto/           Data transfer objects
├── filter/        JWT, rate-limit and request filters
├── ratelimit/     Rate-limit rules and models
├── constants/     System constants
├── utils/         JWT, file and validation utilities
└── exception/     Business exceptions
```

The application follows a layered flow:

1.  Controllers receive HTTP requests.
2.  Services contain business logic.
3.  Mappers access MySQL through MyBatis-Plus and XML mappers.
4.  Redis manages active sessions and request rate limiting.
5.  Security filters process JWT, CSRF, and rate-limit rules before
    requests reach controllers.

### Backend Functional Modules

#### Authentication

-   User registration and login
-   Google OAuth
-   Email verification
-   Password recovery and reset
-   Access Token and Refresh Token
-   Cookie-based authentication
-   Role-based authorization

#### Book Catalog

-   Recommended books
-   New books
-   Categories and subcategories
-   Book search
-   Book details
-   User comments
-   Book cover and image management

#### Chapters and Reading

-   Chapter listing
-   Chapter content
-   Free and unlocked content access
-   Reading progress
-   Reading history
-   Content extraction from DOCX, PDF, EPUB, RTF, and ODT using Apache
    Tika

#### Favorites

-   Create favorite lists
-   Manage personal lists
-   Add and remove books
-   Delete favorite lists

#### Orders and Internal Economy

-   Book and chapter orders
-   Order management
-   Payment processing
-   Internal coin packages
-   Chapter unlocking
-   User purchase records

#### Stripe

-   Payment session creation
-   Payment verification
-   Stripe accounts for authors
-   Author account verification
-   Withdrawal requests
-   Payment session status management

#### Author System

-   Author profile
-   Author connection
-   Author's books
-   Book creation
-   Chapter upload and editing
-   Author/book relationships

#### Administration

-   Administrator authentication
-   Book management
-   Chapter management
-   Content management
-   Administrative book and chapter creation/update

#### Artificial Intelligence

The backend integrates Spring AI with OpenAI for book cover generation,
including configurable provider, model, image size, quantity, and
generation timeout.

### Backend Security

TunkBook separates permissions into three main roles:

-   `ROLE_LECTOR`
-   `ROLE_AUTOR`
-   `ROLE_ADMIN`

Authentication uses two tokens:

-   `TB_ACCESS` --- short-lived access token
-   `TB_REFRESH` --- longer-lived refresh token

Redis is used to validate active sessions. This allows sessions to be
invalidated server-side even if the JWT itself has not expired.

Additional security mechanisms include:

-   CSRF protection
-   CORS configuration
-   BCrypt password hashing
-   Rate limiting by IP, email, user, session, or token
-   Specific protection rules for login, registration, email
    verification, password recovery, payments, and orders

### Key Engineering Challenges

#### 1. Token Authentication with Redis Sessions

The system does not rely only on JWT validation. A valid token must also
correspond to an active Redis session, allowing server-side session
invalidation and better control over authentication.

#### 2. Role-Based Access Control

Readers, authors, and administrators have different permissions and
access to different protected routes.

#### 3. Payment and Internal Economy

The backend coordinates internal orders, coin packages, unlocked
chapters, Stripe sessions, payment verification, and author payment
accounts.

#### 4. File Processing

Authors can upload chapter content in multiple formats, with Apache Tika
used to extract text.

#### 5. Context-Aware Rate Limiting

Different operations use different rate-limit contexts, including IP,
email, user, session, and reset token.

#### 6. AI Cover Generation

Spring AI and OpenAI are integrated into the editorial workflow to
generate book covers.

#### 7. Secure Frontend Integration

Cookies, CSRF, CORS, and refresh tokens are designed to work with a
separately deployed frontend application.

### Main API Areas

The backend exposes REST endpoints for:

-   Authentication
-   Google OAuth
-   Email verification
-   Password reset
-   Users
-   Categories
-   Books
-   Chapters
-   Favorites
-   Reading progress
-   Reading history
-   Orders
-   Stripe
-   Authors
-   Administration
-   AI cover generation
-   Sitemap

------------------------------------------------------------------------

# TunkDog

**TunkDog** is a mobile application built with Expo and React Native for
dog owners. It allows users to create dog profiles, record outdoor walks
with GPS, view maps and routes, and review daily and monthly activity
data.

The application includes authentication, background location tracking,
geospatial data processing, secure token storage, image management, and
multilingual support.

### Project Value

TunkDog combines mobile development with backend services and geospatial
technologies.

The project involves:

-   Real-time GPS tracking
-   Background location
-   Map rendering
-   Route processing
-   Geospatial database storage
-   Authentication
-   Secure token management
-   Image processing
-   Activity statistics
-   Multilingual application design
-   Mobile production builds

### Mobile Application

**Technology Stack**

-   Expo SDK 54
-   React Native 0.81
-   React 19
-   TypeScript
-   Expo Router
-   Axios
-   MapLibre React Native
-   Expo Location
-   Expo Task Manager
-   Expo SecureStore
-   AsyncStorage
-   Expo Image Picker
-   Google Sign-In
-   Lucide React Native
-   EAS Build

### Main Features

#### Dog Profiles

-   Create dog profiles
-   List dogs
-   View dog details
-   Edit dog names
-   Change dog photos

#### GPS Walk Recording

Users can select a dog and record:

-   Route
-   Distance
-   Duration
-   Real-time speed
-   Start and end time

#### Maps and Routes

MapLibre is used to display:

-   Current location
-   Active route
-   Daily route
-   Map-based walking data

#### Background Location

With the required permissions, the application can continue recording a
walk while the application is locked or running in the background.

#### Activity Statistics

The application displays:

-   Daily distance
-   Daily walking time
-   Average speed
-   Monthly range
-   Today's route
-   Monthly progress

#### Authentication

The application supports:

-   Email verification-code login
-   Google Sign-In
-   Session restoration
-   Logout
-   Account deletion
-   Automatic access-token refresh

Authentication tokens are stored using secure device storage.

#### Multilingual Support

The application supports:

-   Spanish
-   English
-   Chinese

The application can also follow the device language and allows users to
select a preferred language.

### Mobile Architecture

``` text
Dog-app-movil/
├── app/                         Expo Router pages and routes
│   ├── _layout.tsx              Root layout and session management
│   ├── (tabs)/                  Main navigation
│   ├── dogs/                    Dog registration and details
│   ├── account-settings.tsx     Account settings
│   ├── help.tsx                 Help
│   ├── privacy.tsx              Privacy policy
│   └── terms.tsx                Terms of use
├── src/
│   ├── context/                 Authentication and walk state
│   ├── screens/                 Business screens
│   │   ├── gps/                 GPS, map and walking
│   │   ├── dog/                 Dog management
│   │   └── user/                Login and account
│   ├── lib/                     API, storage, Google auth and GPS
│   ├── i18n/                    Translations
│   └── theme/                   Theme and fonts
├── assets/
├── plugins/
├── android/
├── app.json
├── app.config.js
├── eas.json
└── package.json
```

### TunkDog Backend

The backend is a REST API built with Spring Boot.

It provides authentication, dog management, walking records, statistics,
geospatial storage, image uploads, and account management.

**Technology Stack**

  Area               Technology
  ------------------ ------------------------------
  Language           Java 21
  Framework          Spring Boot 4.1.0
  API                Spring Web MVC
  Security           Spring Security, JWT, BCrypt
  External Login     Google ID Token
  Persistence        MyBatis-Plus
  Database           PostgreSQL
  Geospatial Data    PostGIS
  Cache & Sessions   Redis
  Email              Spring Mail, Gmail SMTP
  Images             Cloudinary, cwebp, WebP
  Build              Maven Wrapper
  Deployment         Docker, Eclipse Temurin

### Backend Architecture

The backend follows a layered architecture:

-   `controller` --- REST endpoints
-   `service` --- business logic
-   `mapper` --- MyBatis-Plus database access
-   `dto` --- API input and output objects
-   `pojo` --- domain entities
-   `filter` --- JWT authentication
-   `config` --- security and external services
-   `utils` --- JWT, authentication and validation utilities

### Authentication Flow

``` text
Mobile Client
     |
     | Email / Google login
     v
Auth Controller
     |
     | Validate credentials / code / external token
     v
Service
     |
     | Create user if necessary
     | Generate accessToken + refreshToken
     | Store session and refresh token in Redis
     v
Mobile Client
     |
     | Authorization: Bearer <accessToken>
     v
Protected API
```

### Backend Functional Modules

#### Authentication

Two authentication methods are supported:

-   Email verification code
-   Google ID Token

Both flows use the same user model and generate:

-   Access Token
-   Refresh Token
-   Token Type
-   Expiration Time
-   Basic user information

#### JWT and Redis Session Management

The JWT contains information such as:

-   User ID
-   Email
-   Session ID
-   Token ID
-   Token type

Redis is used to verify active sessions and allowed refresh tokens. This
allows sessions to be invalidated and prevents old refresh tokens from
remaining usable after rotation.

#### Dog Management

The backend separates dog data from user access using `dogs` and
`dog_members`.

Supported operations:

-   Register a dog
-   List authenticated user's dogs
-   View dog details
-   Update name or avatar
-   Delete a dog when the user has the `OWNER` role

#### Image Processing

Before uploading an image, the backend:

-   Validates file size
-   Validates supported formats
-   Converts images to WebP
-   Resizes images to 512px width
-   Removes metadata
-   Uploads the optimized image to Cloudinary

This reduces image size and improves mobile performance.

#### Walking and Routes

Each walk contains:

-   Associated dog
-   User
-   Start time
-   End time
-   Distance
-   Duration
-   Route points

Route points are converted into GeoJSON `LineString` data and stored
using PostGIS.

This allows the backend to retrieve routes using geographic data rather
than storing the route only as plain text.

#### Statistics

The backend calculates:

**Today** - Total distance - Total duration - Average speed - Today's
routes

**Current month** - Total distance - Achievement range - Range image -
Progress percentage

Statistics use the `Europe/Madrid` business timezone to avoid date
calculation errors when the server runs in another region.

### Key Engineering Challenges

#### 1. Dual-Token Authentication

Access Tokens are used for normal requests while Refresh Tokens renew
sessions. Refresh Tokens are registered in Redis and removed after
rotation to prevent reuse of old tokens.

#### 2. Stateful Session Control with JWT

Although JWT is normally stateless, the system stores a session ID in
Redis and checks whether the session remains active. This allows
sessions to be invalidated server-side.

#### 3. Multiple Authentication Providers

Email verification-code login and Google login eventually use the same
user, JWT, and Redis session model.

#### 4. Geospatial Data with PostGIS

Walking routes are transformed into GeoJSON `LineString` data and stored
as PostGIS geometry. This provides a foundation for future geographic
queries and route-related functionality.

#### 5. GPS Data Quality

The application filters GPS points with low accuracy, distances that are
too small, and abnormal speeds to reduce GPS noise.

#### 6. Background Location

Expo Location and Expo Task Manager maintain walking state and continue
capturing GPS points when the application is running in the background.

#### 7. Image Optimization

Images are validated, converted to WebP, resized, and cleaned before
being uploaded to Cloudinary.

#### 8. User-Dog Permissions

Sensitive operations verify the user's relationship with the dog through
`dog_members`, with deletion additionally requiring the `OWNER` role.

### Mobile Permissions

The application uses:

-   **Location** --- walking recording, map positioning, distance,
    duration and speed
-   **Background Location** --- continuing an active walk in the
    background
-   **Photos** --- selecting or changing a dog's avatar

### Build and Deployment

The project uses EAS Build with:

-   `preview` profile for internal testing
-   `production` profile for production builds

The backend can be packaged and deployed using Docker.

------------------------------------------------------------------------

# Technical Skills

## Backend

-   Java
-   Spring Boot
-   Spring Security
-   MyBatis-Plus
-   REST API
-   JWT
-   Redis
-   MySQL
-   PostgreSQL
-   PostGIS
-   Stripe
-   Spring AI
-   OpenAI
-   Docker

## Frontend & Mobile

-   React
-   React Native
-   TypeScript
-   Vite
-   React Router
-   Expo
-   Expo Router
-   Axios
-   MapLibre

## Security

-   JWT Access / Refresh Tokens
-   OAuth / Google Sign-In
-   CSRF
-   CORS
-   BCrypt
-   Role-Based Access Control
-   Redis Session Management
-   Rate Limiting
-   Secure Token Storage

## Deployment

-   Vercel
-   Render
-   Contabo VPS
-   Docker
-   EAS Build
-   Maven
-   Git / GitHub

------------------------------------------------------------------------

# Development Experience

Through these projects, I have worked across the complete application
lifecycle:

**Architecture → Database Design → Backend API → Authentication →
Security → Frontend / Mobile Development → External Integrations →
Testing → Deployment**

The projects demonstrate experience with both traditional web
applications and mobile applications, including production-oriented
concerns such as security, authentication, payments, geospatial data,
image optimization, SEO, session management, and deployment.

## Production and Source Code

TunkBook is deployed at:

**https://tunkbook.com/**

Because some projects are connected to production environments and real
users, production source code and sensitive configuration are kept
private.

This portfolio repository focuses on the project architecture, technical
implementation, engineering challenges, and technologies used.

------------------------------------------------------------------------

# Contact

**Wei Xu**\
https://www.linkedin.com/in/wei-xu-a57b31250/

Software Developer\
Java / Spring Boot / Full Stack

Open to software development opportunities.
