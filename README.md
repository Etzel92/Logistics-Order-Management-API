# logistics-orders-api · NestJS + MongoDB

API REST desarrollada con **NestJS (TypeScript)** y **MongoDB (Mongoose)** para la gestión de **usuarios**, **trucks**, **locations** y **orders**.  
Incluye autenticación con **JWT**, validación de datos con `class-validator`, documentación interactiva con **Swagger** y consumo de **Google Places API** para resolver ubicaciones.

## Descripción

El proyecto permite que un usuario autenticado cree y administre órdenes logísticas vinculando un **truck**, una ubicación de **pickup** y una de **dropoff**.  
Cada orden pertenece a un usuario y puede avanzar mediante un flujo de estatus controlado a través de un endpoint dedicado.

Además, la API incluye:

- registro e inicio de sesión con JWT
- CRUD para usuarios, trucks y locations
- creación, consulta, actualización y eliminación de órdenes
- paginación y filtros en consultas de órdenes
- `populate` opcional en detalle y listado de órdenes
- aggregation para conteo de órdenes por estatus
- validación global de DTOs
- documentación y pruebas manuales desde Swagger en `/docs`

## Stack tecnológico

- **NestJS**
- **TypeScript**
- **MongoDB**
- **Mongoose**
- **JWT / Passport**
- **Swagger**
- **class-validator / class-transformer**
- **Google Places API**

## Reglas de negocio principales

- El usuario autenticado puede crear órdenes asociadas a:
  - un `truck`
  - una ubicación de `pickup`
  - una ubicación de `dropoff`
- El `user` de la orden se toma del token JWT
- El cambio de estatus se realiza únicamente desde un endpoint dedicado:
  - `created -> in_transit -> completed`
- No se permiten transiciones inválidas de estatus
- Un usuario normal solo puede consultar y modificar sus propias órdenes
- El endpoint de estadísticas devuelve el conteo de órdenes por estatus

## Módulos principales

### Auth
- `POST /auth/signup`
- `POST /auth/login`

### Users
- CRUD de usuarios
- email único
- password hasheado con bcrypt
- nombre derivado del email cuando no se envía explícitamente

### Trucks
- CRUD de trucks
- placas únicas en mayúsculas

### Locations
- CRUD de locations por usuario
- resolución de dirección y coordenadas a partir de `placeId` usando Google Places API

### Orders
- creación de órdenes con referencias a truck, pickup y dropoff
- listado paginado con filtros
- detalle con `expand=true`
- actualización de relaciones principales
- cambio de estatus en endpoint separado
- estadísticas por estatus mediante aggregation
