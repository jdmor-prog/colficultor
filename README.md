# Colficultor

Plataforma web de comercio electrónico que conecta directamente a caficultores con compradores para reducir intermediarios y fortalecer la comercialización del café colombiano.

## Estado del proyecto

**En desarrollo funcional**. El repositorio contiene backend y frontend operativos con módulos clave implementados (autenticación, catálogo, carrito, órdenes, pagos, PQR, reseñas, reportes y notificaciones).

## Tabla de contenido

- [Descripción general](#descripción-general)
- [Características principales](#características-principales)
- [Módulos funcionales](#módulos-funcionales)
- [Arquitectura general](#arquitectura-general)
- [Estructura del repositorio](#estructura-del-repositorio)
- [Stack tecnológico](#stack-tecnológico)
- [Requisitos previos](#requisitos-previos)
- [Instalación general](#instalación-general)
- [Configuración general](#configuración-general)
- [Ejecución del proyecto](#ejecución-del-proyecto)
- [Flujo básico de uso](#flujo-básico-de-uso)
- [Roles del sistema](#roles-del-sistema)
- [Documentación por módulo](#documentación-por-módulo)
- [Despliegue](#despliegue)
- [API y documentación técnica](#api-y-documentación-técnica)
- [Datos de prueba](#datos-de-prueba)
- [Troubleshooting](#troubleshooting)
- [Checklist funcional](#checklist-funcional)
- [Pendiente por definir](#pendiente-por-definir)

## Descripción general

Colficultor está organizado como un monorepo con dos submódulos:

- `back-colficultor`: API REST en FastAPI + MongoDB.
- `front-colficultor`: cliente web en HTML/CSS/JavaScript vanilla.

El frontend consume la API del backend y adapta la interfaz según el rol del usuario autenticado.

## Características principales

- Registro e inicio de sesión con JWT.
- OAuth con Google para onboarding/login.
- Recuperación de contraseña vía correo electrónico.
- Catálogo de productos con búsqueda, filtros, ordenamiento y paginación.
- Gestión de productos por caficultor (crear, editar, desactivar/eliminar, imágenes).
- Carrito de compras y creación de órdenes.
- Pago con proveedor configurable (`mock` o PayU).
- Seguimiento de estado de pedidos y flujo de ventas para caficultor.
- Reseñas de productos y respuesta del caficultor.
- Tickets PQR con mensajería y gestión administrativa.
- Notificaciones internas por eventos de negocio.
- Exportación de reportes CSV/PDF para admin y caficultor.

## Módulos funcionales

| Módulo | Estado | Evidencia en código |
|---|---|---|
| Autenticación y registro | Implementado | `app/api/routes/auth.py`, `google_auth.py` |
| Gestión de perfiles | Implementado | `app/api/routes/users.py` (`/me`, `PATCH /me`) |
| Gestión de productos | Implementado | `app/api/routes/productos.py`, `product_images.py` |
| Búsqueda y filtrado | Implementado | `app/api/routes/products_public.py` + filtros en `front/js/app.js` |
| Carrito y órdenes | Implementado | `app/api/routes/cart.py`, `orders.py` |
| Pagos | Implementado (mock + PayU) | `app/api/routes/payments.py`, `services/payments_service.py` |
| Seguimiento de pedidos | Implementado | estados y transiciones en `schemas/orders.py`, `services/orders_service.py` |
| Reseñas | Implementado | `app/api/routes/reviews.py` |
| Soporte / PQR | Implementado | `app/api/routes/pqr.py` |
| Notificaciones | Implementado | `app/api/routes/notifications.py` |
| Reportes | Implementado | `app/api/routes/reports.py` |

## Arquitectura general

- **Frontend estático** (`front-colficultor`) ejecutado en navegador.
- **Backend API** (`back-colficultor`) con FastAPI y lógica de negocio por servicios.
- **Persistencia** en MongoDB usando Motor (async).
- **Integraciones externas**: reCAPTCHA v3, Google OAuth, Cloudinary, SMTP y PayU.

## Estructura del repositorio

```text
.
├── README.md
├── .gitmodules
├── back-colficultor/
│   ├── app/
│   ├── requirements.txt
│   ├── .env.example
│   └── run.py
└── front-colficultor/
    ├── index.html
    ├── reset-password.html
    ├── css/
    ├── js/
    └── img/
```

## Stack tecnológico

### Backend
- Python
- FastAPI
- Uvicorn
- MongoDB + Motor/PyMongo
- Pydantic
- JWT (`python-jose`)
- `bcrypt` para hash de contraseñas
- `httpx` (reCAPTCHA/Google)
- Cloudinary SDK
- ReportLab y OpenPyXL (reportes)

### Frontend
- HTML5
- CSS3
- JavaScript vanilla (ES modules/global)
- Font Awesome (CDN)
- Google Fonts (Poppins)
- Google reCAPTCHA v3 script

## Requisitos previos

- Git
- Python 3.10+
- MongoDB accesible
- Cuenta/credenciales para servicios externos según módulo:
  - SMTP
  - Google OAuth
  - reCAPTCHA
  - Cloudinary
  - PayU (si se usa proveedor real)

## Instalación general

```bash
git clone <URL_DEL_REPO>
cd colficultor
git submodule update --init --recursive
```

## Configuración general

1. Copiar variables de entorno del backend:

```bash
cd back-colficultor
cp .env.example .env
```

2. Ajustar `.env` según tu entorno (MongoDB, JWT, SMTP, reCAPTCHA, Google OAuth, Cloudinary, pagos).

## Ejecución del proyecto

### 1) Backend

```bash
cd back-colficultor
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python run.py
```

Backend disponible en `http://localhost:8000`.

### 2) Frontend

No hay `package.json` ni scripts de build en este repo. Es un frontend estático.

Ejemplo con servidor estático en puerto `5500`:

```bash
cd front-colficultor
python -m http.server 5500
```

Frontend disponible en `http://localhost:5500`.

## Flujo básico de uso

1. Abrir frontend.
2. Registrar usuario o iniciar sesión (local o Google).
3. Explorar catálogo y filtrar productos.
4. Como comprador: agregar al carrito, crear orden, iniciar pago, seguir estado, dejar reseñas, abrir PQR.
5. Como caficultor: gestionar productos, revisar ventas, actualizar estados de orden, descargar reportes.
6. Como admin: gestionar usuarios/productos, monitorear PQR y descargar reportes globales.

## Roles del sistema

- `comprador`
- `caficultor`
- `admin`

El control de permisos está implementado en backend con `require_role(...)` y validaciones adicionales en servicios/rutas.

## Documentación por módulo

- Backend: [`back-colficultor/README.md`](./back-colficultor/README.md)
- Frontend: [`front-colficultor/README.md`](./front-colficultor/README.md)

## Despliegue

Proyecto desplegado en Render

- Frontend apunta en producción a `https://back-colficultor.onrender.com` (`front-colficultor/js/config.js`).
- CORS del backend incluye `https://front-colficultor.onrender.com` (`back-colficultor/app/core/config.py`).

## API y documentación técnica

Al ejecutar FastAPI localmente:

- Swagger UI: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`
- OpenAPI JSON: `http://localhost:8000/openapi.json`

## Datos de prueba

En el arranque del backend se ejecuta un seed de usuarios demo (`app/db/seed_users.py`):

- `admin@colficultor.com` / `Admin123!`
- `caficultor@colficultor.com` / `Cafe123!`
- `comprador@colficultor.com` / `Compra123!`

## Troubleshooting

- **Error de CORS**: verifica `BACKEND_CORS_ORIGINS` en `.env` del backend.
- **No inicia sesión/registro por reCAPTCHA**: revisar `RECAPTCHA_SECRET_KEY` y `RECAPTCHA_ENABLED`.
- **No llegan correos de recuperación**: validar `SMTP_*` y `MAIL_FROM`.
- **Pago no procesa**: revisar `PAYMENT_PROVIDER`, `WEBHOOK_SECRET` y credenciales PayU si aplica.
- **Fallos subiendo imágenes**: revisar credenciales Cloudinary y límites `PRODUCT_IMAGE_MAX_*`.
- **Google OAuth falla**: verificar `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`, `GOOGLE_CALLBACK_URL` y URI en Google Console.

## Checklist funcional

- [x] Registro local con rol (`comprador`/`caficultor`)
- [x] Login local con JWT
- [x] Logout con revocación de token (JTI)
- [x] Login/registro con Google OAuth
- [x] Recuperación de contraseña con token de un solo uso
- [x] Perfil de usuario editable
- [x] CRUD de productos para caficultor
- [x] Gestión de imágenes de producto (Cloudinary)
- [x] Catálogo público con filtros/orden/paginación
- [x] Carrito de compras
- [x] Creación de órdenes desde carrito
- [x] Cambio de estado de orden por rol
- [x] Pago mock
- [x] Integración PayU (intento + confirmación)
- [x] Notificaciones por eventos
- [x] Reseñas y respuesta del caficultor
- [x] PQR para usuarios y gestión admin
- [x] Reportes CSV/PDF
- [x] Paneles frontend diferenciados por rol
