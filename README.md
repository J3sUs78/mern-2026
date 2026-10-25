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
| Interfaz web | React, Vite, Tailwind CSS | Aplicación de página única adaptable a los flujos de clientes y personal |
| API | Node.js y Express | API REST y lógica de negocio |
| Persistencia | MySQL/MariaDB | Base de datos relacional para usuarios, catálogo, reservas, pedidos y movimientos de bodega |
| Validación | Zod | Validación de datos en formularios y endpoints seleccionados |
| Sesiones y permisos | JWT, cookies `httpOnly`, bcryptjs y RBAC | Autenticación, protección de credenciales y autorización por permisos |
| Imágenes | Cloudinary | Almacenamiento de imágenes de productos y servicios |
| Correo | Nodemailer con Gmail | Verificación de cuentas, recuperación de contraseña y notificaciones |


## Estructura organizacional del sistema

La aplicación representa las funciones de trabajo del negocio mediante roles y permisos granulares. El backend es la autoridad para conceder acceso; la interfaz también adapta las rutas y opciones visibles al usuario.



Los permisos se asignan por recurso y acción (RBAC). Las áreas operativas también pueden organizarse mediante departamentos y asignaciones de usuarios. La configuración de roles y permisos se administra desde el sistema.

## Principios de arquitectura y diseño

- **Arquitectura cliente-servidor:** React consume la API REST de Express; solo el backend accede a la base de datos.
- **Separación de responsabilidades:** rutas y middlewares reciben las solicitudes, controladores aplican reglas de negocio y utilidades encapsulan integraciones y procesos compartidos.
- **Modularidad por dominio:** frontend y backend organizan sus componentes por áreas como autenticación, productos, pedidos, agenda, usuarios y bodega.
- **Persistencia relacional:** MySQL/MariaDB representa las entidades y sus relaciones con claves, restricciones y transacciones para operaciones que deben mantenerse consistentes.
- **Seguridad en varias capas:** contraseñas con hash, sesiones JWT en cookie `httpOnly`, permisos RBAC, validación de entradas y consultas SQL parametrizadas.
- **Validación compartida:** schemas Zod se utilizan en el cliente y en rutas del servidor para mantener reglas coherentes donde están integrados.
- **Integraciones desacopladas:** correo, imágenes y WhatsApp se gestionan mediante utilidades dedicadas, manteniendo sus credenciales fuera del código fuente.
- **Evolución incremental:** migraciones SQL y control de versiones permiten registrar cambios del esquema y del código durante el desarrollo.

