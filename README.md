# Historias de Usuario
**Proyecto: Plataforma de Donaciones por Campañas**
 
---
 
## HU-01 — Registro de usuario
 
**Como** visitante de la plataforma,
**quiero** poder crear una cuenta con mi nombre, correo y contraseña,
**para** acceder a las funcionalidades de la plataforma como donante o creador de campañas.
 
### Criterios de Aceptación
 
- El formulario de registro debe solicitar: nombre (opcional), correo electrónico y contraseña.
- Si el correo ya existe en el sistema, el sistema debe retornar un error con el mensaje "El correo ya está registrado" (HTTP 409).
- La contraseña debe almacenarse cifrada con bcrypt (factor de coste mínimo 10).
- Al registrarse exitosamente, el sistema retorna un access token JWT y los datos básicos del usuario (id, nombre, correo, rol).
- El correo se normaliza en minúsculas y sin espacios antes de guardarse.
- El rol asignado por defecto al nuevo usuario es `USER`.
---
 
## HU-02 — Inicio de sesión
 
**Como** usuario registrado,
**quiero** iniciar sesión con mi correo y contraseña,
**para** acceder a mi cuenta y gestionar mis campañas.
 
### Criterios de Aceptación
 
- El sistema acepta correo y contraseña para autenticar al usuario.
- Si el correo no existe o la contraseña es incorrecta, retorna el mensaje "Credenciales incorrectas" (HTTP 401), sin distinguir cuál campo es incorrecto.
- Al autenticarse exitosamente, el sistema retorna un access token JWT y los datos del usuario (id, nombre, correo, rol).
- El token JWT incluye el `sub` (id del usuario), el correo y el rol.
- El correo se normaliza (minúsculas, sin espacios) antes de validarlo.
---
 
## HU-03 — Creación de una campaña de donación
 
**Como** usuario autenticado,
**quiero** crear una campaña de donación en una de las categorías disponibles,
**para** recaudar fondos para una causa específica.
 
### Criterios de Aceptación
 
- El usuario puede crear campañas en las siguientes categorías: Salud, Educación, Tecnología, Arte, Emprendimiento, Medio Ambiente, Proyectos Universitarios.
- Cada campaña debe incluir: título, descripción y meta de recaudación (`goal`).
- El campo `raised` (monto recaudado) inicia en 0 al crear la campaña.
- La campaña queda asociada automáticamente al usuario autenticado (`userId`).
- Si el usuario no está autenticado, el sistema retorna HTTP 401.
- El sistema registra la fecha de creación (`createdAt`) automáticamente.
---
 
## HU-04 — Consulta de campañas por categoría
 
**Como** visitante o usuario autenticado,
**quiero** ver la lista de campañas activas de una categoría,
**para** conocer las iniciativas disponibles y decidir a cuáles apoyar.
 
### Criterios de Aceptación
 
- El sistema expone un endpoint GET por cada categoría (ej. `/health`, `/education`, `/technology`, etc.).
- La respuesta incluye para cada campaña: id, título, descripción, meta, monto recaudado y fecha de creación.
- Si no hay campañas en la categoría, el sistema retorna una lista vacía (no un error).
- El listado es accesible sin necesidad de autenticación.
---
 
## HU-05 — Consulta de una campaña específica
 
**Como** usuario o visitante,
**quiero** ver el detalle de una campaña por su ID,
**para** conocer su información completa antes de donar.
 
### Criterios de Aceptación
 
- El endpoint `GET /<categoría>/id/:id` o `GET /<categoría>/:id` retorna el detalle completo de la campaña.
- Si el ID no corresponde a ninguna campaña, el sistema retorna HTTP 404.
- La respuesta incluye al menos: id, título, descripción, goal, raised, userId y fechas.
---
 
## HU-06 — Actualización del progreso de una campaña
 
**Como** usuario autenticado,
**quiero** aportar a una campaña,
**para** aumentar el monto recaudado y acercarla a su meta.
 
### Criterios de Aceptación
 
- Cualquier usuario autenticado puede realizar un aporte a una campaña.
- El aporte debe ser un valor numérico mayor a 0.
- El sistema actualiza el campo `raised` sumando el valor aportado.
- El campo `goal` no se modifica al realizar un aporte.
- El sistema puede mostrar el monto faltante calculando `goal - raised`.
- Si la campaña no existe, el sistema retorna HTTP 404.
- Si el usuario no está autenticado, el sistema retorna HTTP 401.
---
 
## HU-07 — Eliminación de una campaña
 
**Como** usuario autenticado y creador de una campaña,
**quiero** poder eliminar una campaña que creé,
**para** retirarla de la plataforma si ya no es necesaria.
 
### Criterios de Aceptación
 
- El endpoint DELETE elimina permanentemente la campaña del sistema.
- Solo el creador de la campaña o un usuario con rol `ADMIN` puede eliminarla.
- Si la campaña no existe, el sistema retorna HTTP 404.
- Tras la eliminación, el sistema retorna HTTP 200 con mensaje de confirmación.
- Si el usuario no está autenticado, el sistema retorna HTTP 401.
---
 
## HU-08 — Registro de objetivos de actividad
 
**Como** usuario autenticado,
**quiero** registrar objetivos de actividad,
**para** llevar control de acciones o metas relacionadas con campañas.
 
### Criterios de Aceptación
 
- El usuario puede crear una actividad con objetivo requerido.
- El listado muestra las actividades registradas.
- Si no hay actividades, muestra lista vacía.
- Si no está autenticado, retorna HTTP 401.
---
 
## HU-09 — Control de acceso por roles (ADMIN / USER)
 
**Como** administrador de la plataforma,
**quiero** tener un rol especial que me permita gestionar todos los recursos,
**para** supervisar y moderar el contenido de la plataforma.
 
### Criterios de Aceptación
 
- Existen dos roles: `ADMIN` y `USER`. El rol por defecto al registrarse es `USER`.
- Un usuario con rol `ADMIN` puede acceder, modificar y eliminar cualquier campaña o recurso independientemente del propietario.
- Un usuario con rol `USER` solo puede gestionar sus propios recursos.
- El rol se incluye en el JWT y es validado por el guard de roles en cada endpoint protegido.
- Si un `USER` intenta acceder a un recurso restringido a `ADMIN`, el sistema retorna HTTP 403.
---
 
## HU-10 — Visualización del panel principal
 
**Como** usuario autenticado,
**quiero** ver un panel principal con los objetivos de campañas y accesos a las categorías,
**para** navegar rápidamente y registrar actividades de la plataforma.
 
### Criterios de Aceptación
 
- El panel muestra el módulo de objetivos de campañas.
- El usuario puede registrar un objetivo.
- El usuario puede ver el historial de objetivos.
- El usuario puede navegar a las categorías desde el menú.
- Si no está autenticado, es redirigido a login.
---
 
## HU-11 — Navegación principal
 
**Como** usuario autenticado,
**quiero** tener una barra de navegación principal visible,
**para** moverse fácilmente entre las diferentes categorías y secciones de la plataforma.
 
### Criterios de Aceptación
 
- La navegación principal muestra las categorías disponibles y el dashboard.
- La sección actual se resalta visualmente para indicar la ruta activa.
- La navegación permanece disponible en todas las páginas internas de la aplicación.
- Cada elemento del menú redirige correctamente a su ruta correspondiente.
- La navegación se muestra en la parte superior de la interfaz.
- Si el usuario no está autenticado, el sistema puede redirigirlo al login.
---
 
## Resumen
 
| ID | Historia | Actor Principal |
|----|----------|----------------|
| HU-01 | Registro de usuario | Visitante |
| HU-02 | Inicio de sesión | Usuario registrado |
| HU-03 | Creación de campaña de donación | Usuario autenticado |
| HU-04 | Consulta de campañas por categoría | Visitante / Usuario |
| HU-05 | Consulta de una campaña específica | Visitante / Usuario |
| HU-06 | Actualización del progreso de una campaña | Usuario autenticado |
| HU-07 | Eliminación de una campaña | Usuario autenticado |
| HU-08 | Registro de objetivos de actividad | Usuario autenticado |
| HU-09 | Control de acceso por roles (ADMIN / USER) | Administrador |
| HU-10 | Visualización del panel principal | Usuario autenticado |
| HU-11 | Navegación principal | Usuario autenticado |
