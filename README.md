# IIDE — Información que te conecta

Prototipo funcional de un sistema de notificaciones institucionales (Grupo 2 · Ingeniería de Software). Esta carpeta es una Progressive Web App (PWA) instalable: todos los archivos están sueltos en la raíz, lista para desplegar en cualquier hosting estático con HTTPS (GitHub Pages, Netlify, Vercel) o para abrir localmente con un servidor estático (`python3 -m http.server`, `npx serve`, etc. — no funciona abriendo `index.html` directo con `file://` porque el service worker y el manifest necesitan un origen http(s)).

## Qué hace

- Secretaría, el director y el administrador emiten notificaciones a toda la institución, a un curso, a un estudiante puntual, a un docente en particular o a otra persona de secretaría en particular, con prioridad Normal o Urgente.
- Cada destinatario (estudiante, docente o persona de secretaría) ve el aviso completo y solo puede confirmarlo con el botón «OK» — no hay forma de responder, reenviar ni comentar.
- El emisor y el director ven el progreso de confirmaciones; el director además tiene un historial institucional completo.
- Secretaría y el director tienen una pestaña "Notificaciones recibidas" para los avisos que les dirige puntualmente otra persona de secretaría, el director o el administrador (el formulario nunca ofrece la propia cuenta como destinatario).
- El docente tiene una vista propia con tres pestañas: "Avisar a secretaría/director" (un canal interno, de una sola vía y sin confirmación, para avisarle a secretaría y/o al director lo más importante desde su rol, con la posibilidad de adjuntar un archivo), "Notificaciones recibidas" (donde confirma con «OK» las notificaciones que secretaría, el director o el administrador le dirigieron puntualmente a él) y "Enviadas por mí". El docente no tiene acceso al formulario de notificación a estudiantes.
- Cuentas de usuario reales dentro del prototipo: crear cuenta, iniciar sesión y recuperar cuenta (usuario/contraseña, hash SHA-256 en el cliente — ver limitaciones más abajo).
- Funciona en modo claro y oscuro, y en celular. Se auditó el contraste de colores contra WCAG 2.1 AA en ambos modos.
- Instalable como app (ícono en el dispositivo, funciona offline) gracias al manifest y al service worker de esta carpeta.

## Cuentas de ejemplo

Todas con la contraseña `demo1234`:

| Usuario | Rol | Nombre |
|---|---|---|
| `secretaria` | Secretaría | Secretaría Académica |
| `docente` | Docente | Prof. Marina Ibarra (Programación II) |
| `direccion` | Director | Dirección de Carrera *(el nombre de usuario de login y el nombre de la cuenta quedaron igual; solo cambió la etiqueta visible del rol, de "Dirección" a "Director")* |
| `administrador` | Administrador | Administración del Instituto |
| `julian` | Estudiante | Julián Rossi |

También se puede crear una cuenta nueva desde la pantalla "Crear cuenta", o recuperar una cuenta existente desde "¿Olvidaste tu usuario o tu contraseña?" en el login.

## Instalar la app

1. Serví esta carpeta con HTTPS (en producción) o con un servidor local por HTTP (para probar).
2. Abrí `index.html` en Chrome/Edge en desktop o Android.
3. Va a aparecer el botón "⭳ Instalar app" en la barra lateral (o el ícono de instalación del navegador); al instalarla, se abre como app independiente y funciona offline.

## Estado de los datos

Todo — cuentas, notificaciones, confirmaciones y los avisos internos de docente a secretaría/director — se guarda en `localStorage` del navegador: no hay backend ni base de datos compartida. Una cuenta creada en un dispositivo no existe en otro. El botón "↺ Reiniciar demo" (en la barra lateral, una vez logueado) borra todo y vuelve a precargar las cuentas y el aviso de ejemplo.

## Limitaciones del prototipo (a tener en cuenta en la presentación)

- Autenticación 100% en el cliente: las contraseñas se hashean con SHA-256 antes de guardarse, pero no hay servidor validándolas.
- La recuperación de cuenta es informal (alcanza con el nombre de la persona) porque no hay un mail o SMS que verifique la identidad.
- El archivo adjunto de un aviso de docente a secretaría/director queda codificado dentro del propio aviso en `localStorage` (hasta 3 MB) — no hay un servidor de archivos.

Para la arquitectura de producción propuesta (backend, base de datos, autenticación real), ver la especificación funcional completa (`especificacion_sistema_notificaciones.docx` / `.pdf`).
