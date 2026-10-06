# especificaciones 

Este es un proyecto para mantener fichas de pacientes medicos.

## Funcionalidades

* Crear, leer, actualizar y eliminar fichas de pacientes.
* Buscar pacientes por nombre o identificación.
* Visualizar detalles completos de cada paciente.
* Mantener un historial de visitas y tratamientos.
* Pantalla de resumen de pacientes.
* Pantalla de login para acceso seguro de usuarios.

## Requisitos técnicos
[tecnicos.md](tecnicos.md)


## Persistencia

* Los datos se van a guardar en archivos .json en el servidor, dentro del directorio `data/`:
    * `data/pacientes.json`: fichas de pacientes. El historial de visitas se guarda dentro de cada paciente (campo `historialVisitas`); no hay un archivo separado para visitas.
    * `data/usuarios.json`: usuarios del sistema.
* Se debe garantizar la integridad y consistencia de los datos al realizar operaciones CRUD.
* Cree algunos datos de ejemplo iniciales.
* Agrega un usuario llamado "admin" con la contraseña "admin"

## Seguridad

* El acceso a la aplicación debe estar protegido mediante autenticación.
* Solo los usuarios autenticados pueden realizar operaciones CRUD sobre las fichas de pacientes.
* Las contraseñas deben almacenarse de forma segura, utilizando hashing.
* El sistema no ocupa roles, todos los usuarios autenticados tienen los mismos permisos.
* La sesión se mantiene con una cookie `httpOnly` firmada que guarda el identificador de sesión: se crea al iniciar sesión, se envía en cada petición y se destruye al cerrar sesión.

## Interfaz de usuario

**El diseño es responsivo (mobile-first)**: la interfaz se adapta a cualquier tamano de pantalla (movil, tablet y desktop) utilizando el sistema de breakpoints y cuadricula de MUI, sin scroll horizontal ni elementos desbordados.

Para el diseño del proyecto, se utilizará Material-UI (MUI) para los componentes de la interfaz de usuario, asegurando un diseño consistente y responsivo. Se seguirán las pautas de diseño de MUI y se aplicarán estilos personalizados cuando sea necesario.

### diseño de la interfaz de usuario

* Una pantalla de login, con el login y la contraseña para acceder a la aplicación. La pantalla de login, es la unica que tiene un diseño diferente
* Un diseño de pantallas que ocupen el diseño de paginas de administracion, con el menu al lado izquierdo, y que sea colapsable.  
    * Todas las otras pantallas, salvo login, deben usar este diseño.
    * En la parte superior derecha, deberia aparecer el usuario conectado, y una opcion de cerrar sesión.
* Para los contenedores y botones, no use bordes redondeados. Use contenedores sin margen ni bordes redondeados.

### paleta de colores

* Color principal para titulos, botones y enlaces: rojo
* Color de fondo: gris.
* Color secundario de fondo: blanco.
* Color de texto: negro (salvo cuando se usa con color principal)
* Coor de texto (cuando se usa con color principal): blanco

## Rutas

Las rutas se definen en `app/routes.ts` siguiendo las convenciones del modo framework de React Router (SSR, loaders y actions). Las rutas protegidas se agrupan bajo un layout de administración (`layout("routes/admin.tsx", ...)`) que exige autenticación; si no hay sesión activa, el usuario es redirigido a `/login`.

| Ruta | Archivo | Página | Protegida |
|------|---------|--------|-----------|
| `/login` | `app/routes/login.tsx` | Login | No |
| `POST /logout` | `app/routes/logout.tsx` | Cierre de sesión (solo `action`, sin interfaz) | No |
| `/` | `app/routes/dashboard.tsx` | Resumen de pacientes | Sí |
| `/pacientes` | `app/routes/pacientes.lista.tsx` | Lista y búsqueda de pacientes | Sí |
| `/pacientes/nuevo` | `app/routes/pacientes.nuevo.tsx` | Nueva ficha de paciente | Sí |
| `/pacientes/:id` | `app/routes/pacientes.$id.tsx` | Detalle del paciente e historial de visitas | Sí |
| `/pacientes/:id/editar` | `app/routes/pacientes.$id.editar.tsx` | Editar ficha de paciente | Sí |
| `*` (404) | `app/routes/not-found.tsx` | Página no encontrada | No |

Cada ruta obtiene y modifica sus datos mediante `loader` y `action`, que llaman directamente a los servicios en el servidor. No existe una capa de API REST: la persistencia es directa sobre los archivos `.json`.

## Páginas

* **Login** (`/login`): única pantalla con diseño propio (sin layout de administración). Usa el componente `FormularioLogin`; al autenticar redirige al resumen.
* **Resumen de pacientes** (`/`): pantalla inicial tras el login. Muestra totales de pacientes, últimas visitas registradas y acceso rápido a la búsqueda.
* **Lista de pacientes** (`/pacientes`): tabla con todos los pacientes y búsqueda por nombre o identificación. Acciones por paciente: ver, editar y eliminar.
* **Nueva ficha de paciente** (`/pacientes/nuevo`): formulario de creación de la ficha.
* **Detalle del paciente** (`/pacientes/:id`): datos completos del paciente y su historial de visitas; permite agregar visitas al historial.
* **Editar ficha de paciente** (`/pacientes/:id/editar`): formulario de edición de la ficha; incluye opción de eliminar el paciente.
* **Página no encontrada** (`404`): pantalla simple con enlace de retorno al resumen.

## Componentes

Todos los componentes usan MUI, sin bordes redondeados y con diseño responsivo (mobile-first).

### Login

* `FormularioLogin`: formulario de usuario y contraseña con su propio diseño (independiente del layout de administración); valida las credenciales mediante el `action` de la ruta y muestra el error si fallan.

### Layout

* `AdminLayout`: layout de administración con menú lateral colapsable y barra superior; envuelve todas las páginas salvo login.
* `MenuLateral`: Drawer colapsable con la navegación principal (Resumen, Pacientes, Nueva ficha).
* `BarraUsuario`: se ubica en la parte superior derecha; muestra el usuario conectado y la opción de cerrar sesión, que envía un `POST` a la ruta `/logout`.

### Pacientes

* `TablaPacientes`: tabla responsiva con la lista de pacientes y sus acciones (ver, editar, eliminar).
* `BuscadorPacientes`: campo de búsqueda por nombre o identificación.
* `FormularioPaciente`: formulario compartido por las páginas de crear y editar (DRY); campos según el modelo Paciente.
* `ListaVisitas`: listado del historial de visitas de un paciente (fecha, motivo, tratamiento).
* `FormularioVisita`: formulario para agregar una visita al historial.
* `DialogoConfirmacion`: diálogo genérico de confirmación para acciones destructivas (eliminar paciente o visita).



## Principios de código

* SRP (Single Responsibility Principle): cada módulo o clase debe tener una única responsabilidad o motivo para cambiar.
* KISS (Keep It Simple, Stupid): el código debe ser lo más simple posible, evitando complejidad innecesaria.
* DRY (Don't Repeat Yourself): se debe evitar la duplicación de código, promoviendo la reutilización y la modularidad.


## Modelos

Cada modelo se define como una interfaz de TypeScript en `app/types/`, un archivo por modelo: `paciente.ts` (interfaz `Paciente`), `usuario.ts` (interfaz `Usuario`) y `visita.ts` (interfaz `Visita`). Servicios, rutas y componentes usan estas interfaces para el tipado estático.

### Paciente

* id: string (identificador único del paciente)
* nombre: string
* apellido: string
* fechaNacimiento: string (formato YYYY-MM-DD)
* genero: string (masculino, femenino, otro)
* direccion: string
* telefono: string
* email: string
* historialVisitas: array de objetos (cada objeto representa una visita con fecha, motivo y tratamiento)
* usuario: objeto (representa al usuario asociado al paciente, con id, nombre, apellido, username)

### Usuario

* id: string (identificador único del usuario)
* nombre: string
* apellido: string
* username: string
* password: string (almacenada de forma segura, utilizando hashing) 

### Visita

* id: string (identificador único de la visita)
* fecha: string (formato YYYY-MM-DD)
* motivo: string
* tratamiento: string

## Servicios

Los servicios se ubican en `app/services/`, uno por modelo (SRP), y encapsulan todo el acceso a datos: leen y escriben directamente los archivos `.json` del servidor, sin capa de API REST intermedia. Solo los `loader` y `action` de las rutas los utilizan; los componentes nunca acceden a los datos directamente. Cada servicio garantiza la integridad y consistencia de los datos en las operaciones CRUD (por ejemplo, ids únicos y persistencia atómica).

### PacienteService (`app/services/pacientes.service.ts`)

* `listar(busqueda?)`: devuelve todos los pacientes; si se recibe `busqueda`, filtra por nombre, apellido o id (sin distinguir mayúsculas/minúsculas).
* `obtenerPorId(id)`: devuelve la ficha del paciente con su historial de visitas, o null si no existe.
* `crear(paciente)`: genera un id único, asocia el usuario autenticado y guarda la ficha.
* `actualizar(id, paciente)`: reemplaza los datos de la ficha existente.
* `eliminar(id)`: elimina la ficha y su historial de visitas.

### UsuarioService (`app/services/usuarios.service.ts`)

* `autenticar(username, password)`: valida las credenciales contra el hash almacenado y crea la sesión (establece la cookie `httpOnly`).
* `obtenerSesion()`: lee la cookie de sesión y devuelve el usuario conectado, o null si no hay sesión activa.
* `cerrarSesion()`: destruye la sesión y elimina la cookie.

### VisitaService (`app/services/visitas.service.ts`)

* `listarPorPaciente(pacienteId)`: devuelve el historial de visitas del paciente, ordenado por fecha descendente.
* `crear(pacienteId, visita)`: genera un id único y agrega la visita al historial del paciente.
* `eliminar(pacienteId, visitaId)`: elimina la visita del historial del paciente.

