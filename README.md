# Pesebre WebApp

A full-stack web application built for **Universidad Politécnica Salesiana (UPS) – Campus Sur**, designed to digitize and showcase the university's annual Christmas nativity scene (*pesebre*) tradition. The platform allows visitors to explore galleries, vote for their favorite nativity scene, support departments through donations, and interact with 3D models — all managed through a role-based admin panel.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Database Schema](#database-schema)
- [Getting Started](#getting-started)
- [Access URLs](#access-urls)
- [User Roles](#user-roles)
- [3D Models](#3d-models)

---

## Features

### Public (no login required)
- Home page with welcome content, video, and audio
- Photo gallery of nativity scenes by university department
- 3D interactive model viewer (GLB format via `<model-viewer>`)
- History and background of the nativity tradition
- Christmas categories browser
- Christmas novena calendar (9-day event schedule)

### Authenticated Visitors
- User registration and login
- Vote for a favorite nativity scene (one vote per user)
- Live voting ranking
- Support/donate to a nativity scene via a cart-based flow
- Payment simulation for donations
- Christmas trivia game (10 questions)
- Christmas mini-game (catch the stars)
- Christmas curiosities page
- Logout

### Admin Panel (profile = ADMIN)
- Dashboard with live metrics: total users, admins, nativity scenes, events
- Recent records preview (users, scenes, events)
- Full CRUD for users (with block/unblock)
- Full CRUD for nativity scenes (gallery)
- Full CRUD for Christmas events (novenas)
- System audit log viewer (bitácora)

---

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | Java 17, JSP (JavaServer Pages) |
| Server | Apache Tomcat 9 |
| Database | PostgreSQL 18 |
| JDBC Driver | `postgresql-42.7.8.jar` |
| Frontend | HTML5, CSS3, JavaScript |
| 3D Rendering | Google `<model-viewer>` 4.0.0 (GLB) |
| IDE | Eclipse IDE (Java EE) |

---

## Project Structure

```
pesebre/
├── src/
│   └── main/
│       ├── java/
│       │   └── com/pesebre/
│       │       ├── datos/
│       │       │   ├── Conexion.java          # JDBC connection and query helpers
│       │       │   └── TestConexion.java       # Connection test utility
│       │       ├── seguridad/
│       │       │   ├── Usuario.java            # User model: login, register, block check
│       │       │   └── Pagina.java             # Dynamic role-based menu builder
│       │       └── visitante/
│       │           ├── Galeria.java            # Gallery data access
│       │           ├── Novenas.java            # Novena events data access
│       │           └── Votar.java              # Voting logic and ranking
│       └── webapp/
│           ├── index.jsp                       # Home page
│           ├── login.jsp                       # Login form
│           ├── validarLogin.jsp                # Login handler + session setup
│           ├── registro.jsp                    # Registration form
│           ├── nuevoCliente.jsp                # Registration handler
│           ├── menu.jsp                        # Main navigation menu
│           ├── galeria.jsp                     # Photo gallery
│           ├── galeria3d.jsp                   # 3D model viewer
│           ├── novenas.jsp                     # Novena calendar
│           ├── historia.jsp                    # History page
│           ├── categorias.jsp                  # Categories browser
│           ├── admin/
│           │   ├── admin.jsp                   # Admin dashboard
│           │   ├── adminUsuarios.jsp           # User management
│           │   ├── adminPesebres.jsp           # Nativity scene management
│           │   ├── adminNovenas.jsp            # Event management
│           │   └── bitacora.jsp                # Audit log viewer
│           ├── visitante/
│           │   ├── votar.jsp                   # Voting page
│           │   ├── apoya.jsp                   # Donation cart
│           │   ├── pagoApoyo.jsp               # Payment simulation
│           │   ├── trivia.jsp                  # Christmas trivia
│           │   ├── juego.jsp                   # Mini-game
│           │   ├── curiosidades.jsp            # Christmas curiosities
│           │   └── logout.jsp                  # Session termination
│           ├── head&foot/
│           │   ├── head.jsp                    # Shared HTML head + navbar
│           │   ├── menuu.jsp                   # Role-based navigation bar
│           │   ├── footer.jsp                  # Shared footer
│           │   └── molde.jsp                   # Page template
│           ├── 3d/                             # GLB 3D model files
│           ├── images/                         # Images, video, and audio assets
│           ├── css/                            # Public page stylesheets
│           ├── css2/                           # Authenticated/admin stylesheets
│           ├── js/                             # JavaScript files
│           └── WEB-INF/
│               └── lib/
│                   └── postgresql-42.7.8.jar   # PostgreSQL JDBC driver
└── PESEBREBBDD.sql                             # Full database dump (schema + seed data)
```

---

## Database Schema

The application uses PostgreSQL with the following tables:

| Table | Description |
|---|---|
| `tb_perfil` | User roles: `ADMIN` (1), `VISITANTE` (2) |
| `tb_estadocivil` | Marital status lookup (Soltero, Casado, Viudo) |
| `tb_usuario` | Registered users with role, credentials, and block flag |
| `tb_pagina` | Registered pages/routes in the system |
| `tb_perfilpagina` | Role-to-page access control mapping |
| `tb_categoria` | Nativity scene categories |
| `tb_galeria` | Nativity scenes per department (image, description) |
| `tb_novenas` | 9-day Christmas event schedule |
| `tb_voto` | One vote per user per nativity scene |
| `tb_apoyos` | Donation records per scene |
| `tb_auditoria` | Automatic audit log (INSERT/UPDATE/DELETE via trigger) |

The database includes a PostgreSQL trigger function `fn_auditoria()` that automatically logs all data changes to `tb_auditoria` with the operation type, old/new data as JSONB, user, IP, and source page.

---

## Getting Started

### Prerequisites

- Java 17+
- Apache Tomcat 9+
- PostgreSQL 13+ (tested on 18)
- Eclipse IDE for Enterprise Java (or any IDE with Tomcat support)

### Setup

**1. Clone the repository**
```bash
git clone <repository-url>
```

**2. Create the database**
```sql
CREATE DATABASE db_pesebresc;
```

**3. Import the schema and seed data**
```bash
psql -U postgres -d db_pesebresc -f PESEBREBBDD.sql
```

**4. Update the database connection**

Edit `pesebre/src/main/java/com/pesebre/datos/Conexion.java` and update the credentials to match your environment:

```java
this.user   = "your_db_user";
this.pwd    = "your_db_password";
this.cadena = "jdbc:postgresql://your_host:5432/db_pesebresc";
```

**5. Deploy to Tomcat**

- Import the project into Eclipse as an existing project
- Add it to a Tomcat 9 server instance
- Start the server

Alternatively, build a `.war` file and drop it into Tomcat's `webapps/` directory.

---

## Access URLs

| Page | URL |
|---|---|
| Home | `http://localhost:8080/pesebre/` |
| Login | `http://localhost:8080/pesebre/login.jsp` |
| Register | `http://localhost:8080/pesebre/registro.jsp` |
| Gallery | `http://localhost:8080/pesebre/galeria.jsp` |
| 3D Gallery | `http://localhost:8080/pesebre/galeria3d.jsp` |
| Novena Calendar | `http://localhost:8080/pesebre/novenas.jsp` |
| Vote | `http://localhost:8080/pesebre/visitante/votar.jsp` |
| Support/Donate | `http://localhost:8080/pesebre/visitante/apoya.jsp` |
| Admin Dashboard | `http://localhost:8080/pesebre/admin/admin.jsp` |

---

## User Roles

| Role | Profile ID | Access |
|---|---|---|
| `ADMIN` | 1 | Full admin panel, user/scene/event management, audit log |
| `VISITANTE` | 2 | Voting, donations, trivia, game, curiosities |

Navigation menus are dynamically generated per role using the `tb_pagina` and `tb_perfilpagina` tables. Unauthenticated users are redirected to `login.jsp` when accessing protected pages.

---

## 3D Models

Located in `src/main/webapp/3d/`. Rendered using [Google `<model-viewer>`](https://modelviewer.dev/) with auto-rotate and camera controls enabled.

| File | Model |
|---|---|
| `arbolnavidad.glb` | Christmas Tree |
| `bastoncaramelo.glb` | Candy Cane |
| `escenanavidad.glb` | Nativity Scene |
| `galletasnavidad.glb` | Christmas Cookies |
| `regalo.glb` | Gift Box |
| `santaclaus.glb` | Santa Claus |
| `snowman.glb` | Snowman |
| `stag.glb` | Reindeer |
| `star.glb` | Christmas Star |

---

## License

This project was developed for educational purposes at **Universidad Politécnica Salesiana**.  
Free to use for learning, reference, or extension.
