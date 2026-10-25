# Studio Valerinur

Plataforma web de gestión para una PYME chilena dedicada a servicios de cuidado personal y manicura, y a la venta de productos de repostería y similares. El sistema reúne en un solo lugar la información de clientes, catálogo, agenda, reservas, pedidos y bodega, que antes podía quedar repartida entre redes sociales y mensajería.

## Sobre el cliente

Studio Valerinur es una pequeña empresa que combina la prestación de servicios con la comercialización de productos. La plataforma apoya tanto la atención de clientes como la gestión interna del negocio: permite consultar productos y servicios, solicitar pedidos y reservar horas, además de coordinar la revisión, preparación y entrega de esos pedidos.

El proyecto responde a la necesidad de reducir la dispersión de información y facilitar el seguimiento de solicitudes, reservas y pedidos. Su alcance funcional y los requerimientos se documentan en [docs/requerimientos.md](docs/requerimientos.md).

## Equipo de desarrollo

El informe técnico de definición identifica a **Jesús Castro y Andy Machuca** como estudiantes responsables del Proyecto APT. El plan de trabajo asigna actividades de análisis, diseño, desarrollo, validación y documentación a ambos, con algunas tareas de implementación individual distribuidas entre los integrantes. El trabajo se organiza con un enfoque ágil basado en Scrum, adaptado al contexto académico, mediante ciclos iterativos y priorización de funcionalidades.

El cliente aporta el contexto del negocio y sus necesidades; el equipo convierte esos procesos en requerimientos, reglas de negocio y funcionalidades de la plataforma. El código se mantiene bajo control de versiones con Git y GitHub.

## Stack tecnológico

| Componente | Tecnología | Uso |
|---|---|---|
| Interfaz web | React 18, Vite, Tailwind CSS | Aplicación de página única adaptable a los flujos de clientes y personal |
| API | Node.js y Express 4 | API REST y lógica de negocio |
| Persistencia | MySQL/MariaDB 10.4, `mysql2` | Base de datos relacional para usuarios, catálogo, reservas, pedidos y movimientos de bodega |
| Validación | Zod | Validación de datos en formularios y endpoints seleccionados |
| Sesiones y permisos | JWT, cookies `httpOnly`, bcryptjs y RBAC | Autenticación, protección de credenciales y autorización por permisos |
| Imágenes | Cloudinary | Almacenamiento de imágenes de productos y servicios |
| Correo | Nodemailer con Gmail | Verificación de cuentas, recuperación de contraseña y notificaciones |
| Mensajería | WhatsApp Business Cloud API (Meta) | Notificaciones configurables por WhatsApp |
| Entorno local | Docker Compose | Ejecución local de MariaDB |

## Estructura organizacional del sistema

La aplicación representa las funciones de trabajo del negocio mediante roles y permisos granulares. El backend es la autoridad para conceder acceso; la interfaz también adapta las rutas y opciones visibles al usuario.

| Actor / rol | Responsabilidad principal |
|---|---|
| Cliente | Registrarse, revisar el catálogo, crear pedidos, reservar horas y responder a propuestas de horario |
| Administrador | Gestionar usuarios, roles, permisos, catálogo, agenda y operaciones del negocio |
| Revisor | Revisar solicitudes, aprobar o rechazar pedidos y coordinar cambios de horario |
| Bodeguero | Gestionar stock, registrar movimientos y confirmar entregas |
| Funcionario | Consultar el catálogo y crear pedidos internos según sus permisos |

Los permisos se asignan por recurso y acción (por ejemplo, `product:view` o `order:approve`). Las áreas operativas también pueden organizarse mediante departamentos y asignaciones de usuarios. La configuración de roles y permisos se administra desde el sistema.

## Principios de arquitectura y diseño

- **Arquitectura cliente-servidor:** React consume la API REST de Express; solo el backend accede a la base de datos.
- **Separación de responsabilidades:** rutas y middlewares reciben las solicitudes, controladores aplican reglas de negocio y utilidades encapsulan integraciones y procesos compartidos.
- **Modularidad por dominio:** frontend y backend organizan sus componentes por áreas como autenticación, productos, pedidos, agenda, usuarios y bodega.
- **Persistencia relacional:** MySQL/MariaDB representa las entidades y sus relaciones con claves, restricciones y transacciones para operaciones que deben mantenerse consistentes.
- **Seguridad en varias capas:** contraseñas con hash, sesiones JWT en cookie `httpOnly`, permisos RBAC, validación de entradas y consultas SQL parametrizadas.
- **Validación compartida:** schemas Zod se utilizan en el cliente y en rutas del servidor para mantener reglas coherentes donde están integrados.
- **Integraciones desacopladas:** correo, imágenes y WhatsApp se gestionan mediante utilidades dedicadas, manteniendo sus credenciales fuera del código fuente.
- **Evolución incremental:** migraciones SQL y control de versiones permiten registrar cambios del esquema y del código durante el desarrollo.

## Organización del repositorio

```text
.
├── client/                 # Aplicación React y configuración de Vite
│   └── src/
│       ├── api/            # Comunicación HTTP por recurso
│       ├── components/     # Componentes de interfaz
│       ├── context/        # Estado por dominio
│       ├── pages/          # Vistas y rutas
│       └── utils/          # Utilidades de interfaz
├── server/                 # API Express
│   ├── controllers/        # Lógica de negocio y acceso a datos
│   ├── middlewares/        # Autenticación, permisos y validación
│   ├── routes/             # Endpoints REST
│   ├── schemas/            # Schemas Zod
│   └── utils/              # Agenda, correo, imágenes y notificaciones
├── docs/                   # Requerimientos, informes y documentación técnica
├── db_bodega.sql           # Esquema inicial de la base de datos
└── db_migracion_*.sql      # Migraciones incrementales del esquema
```

La documentación ampliada de arquitectura y funcionalidades está en [docs/contexto/README.md](docs/contexto/README.md). El [informe técnico](docs/InformeTecnico_JesusCastro_AndyMachuca.docx) contiene la definición del proyecto APT y sus fundamentos.

## Inicio local

Requisitos: Node.js 20 o superior, pnpm, Docker y Docker Compose.

1. Instala las dependencias:

   ```bash
   pnpm install
   ```

2. Copia `.env.example` a `.env` y configura las variables de base de datos y `TOKEN_SECRET`. Añade las credenciales de Cloudinary, correo o WhatsApp solo si usarás esas integraciones.

3. Inicia MariaDB:

   ```bash
   docker compose up -d
   ```

4. Crea la base `bodega_valerinur`, carga `db_bodega.sql` y aplica las migraciones SQL en orden. Antes de ejecutar `db_migracion_reservas_coherentes.sql`, revisa las instrucciones y posibles conflictos descritos en su encabezado.

5. Inicia la API y el frontend:

   ```bash
   pnpm dev
   ```

Por defecto, la API escucha en `http://localhost:3000` y la aplicación Vite en `http://localhost:5173`. Para producción, `pnpm start` inicia la API y `pnpm --prefix client run build` genera los archivos del frontend.

## Configuración

Las variables disponibles están descritas en [.env.example](.env.example):

- `TOKEN_SECRET`, `PORT`, `CLIENT_URL`, `CORS_ORIGINS`, `COOKIE_SAME_SITE` y `NODE_ENV`.
- `DB_HOST`, `DB_PORT`, `DB_USER`, `DB_PASSWORD` y `DB_DATABASE`.
- Credenciales opcionales: `CLOUDINARY_NAME`, `CLOUD_API_KEY`, `CLOUD_API_SECRET`, `EMAIL_USER`, `EMAIL_PASS` y variables `WHATSAPP_*`.
- Para un frontend desplegado, `VITE_API_URL` configura la URL de la API durante la compilación del cliente.

No publiques el archivo `.env` ni credenciales reales. La configuración de ejemplo de Docker es solo para desarrollo local; cambia las credenciales antes de cualquier despliegue.

## Documentación relacionada

- [Requerimientos funcionales y no funcionales](docs/requerimientos.md)
- [Contexto de arquitectura y funcionalidades](docs/contexto/README.md)
- [Modelo de datos](docs/anexo-c-modelo-bd.md)
- [Casos de prueba](docs/casos-de-prueba.md)
- [Informe técnico de definición](docs/InformeTecnico_JesusCastro_AndyMachuca.docx)
