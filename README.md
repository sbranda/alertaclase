# Tablón — versión PWA

Este es el prototipo de "Tablón" empaquetado como Progressive Web App: instalable en el celular o la PC, con el ícono en el escritorio/pantalla de inicio, el shell disponible offline, y ahora con cuentas reales (usuario + contraseña) en lugar del selector de identidades de la primera versión.

## Por qué son estos archivos y no el link que ya tenías

El link que te pasé antes vive en el hosting de artefactos de Claude, dentro de un sandbox que no permite registrar un service worker ni servir un manifest — por eso ahí nunca aparece el cartel de "instalar" (aunque el login funciona igual ahí, para probarlo rápido). Estos archivos son el mismo diseño y la misma lógica, reempaquetados como un sitio normal para que los alojes donde quieras y sí sea instalable de verdad.

## Contenido

Todo va junto en la misma raíz del sitio (sin subcarpetas), porque `manifest.json` y `service-worker.js` referencian los íconos con rutas relativas planas (`icon-192.png`, no `icons/icon-192.png`):

- `index.html` — la app: pantalla de inicio de sesión / crear cuenta, y luego las vistas de personal (secretaría, docentes, dirección, seguridad y control) y de estudiante.
- `manifest.json` — nombre, ícono y colores que usa el sistema operativo al instalarla.
- `service-worker.js` — cachea el shell de la app para que abra sin conexión.
- `icon-192.png`, `icon-512.png`, `icon-maskable-192.png`, `icon-maskable-512.png`, `apple-touch-icon.png`, `favicon-64.png` — íconos en los tamaños que piden Android/iOS/escritorio.

## Cuentas y contraseñas

Al entrar por primera vez se precargan 6 cuentas de ejemplo, todas con la contraseña **`demo1234`**:

| Usuario | Rol |
|---|---|
| `secretaria` | Secretaría |
| `docente` | Docente (Programación II, comisión 2B) |
| `direccion` | Dirección |
| `seguridad` | Seguridad y control |
| `julian` | Estudiante (2do año · Comisión B) |
| `bianca` | Estudiante (1er año · Comisión A) |

Desde "Crear cuenta" se pueden registrar cuentas nuevas de cualquier rol (para estudiante, eligiendo carrera/comisión; para docente, con un detalle opcional de materia). El botón "↺ Reiniciar datos de la demo" borra todo (cuentas, notificaciones y confirmaciones) y vuelve a precargar las 6 cuentas de ejemplo.

**Importante — esto sigue siendo un prototipo sin backend.** Las contraseñas se guardan hasheadas (SHA-256) en el `localStorage` del navegador, no en texto plano, pero no hay ningún servidor validándolas: cualquiera con las herramientas de desarrollador del navegador podría leer ese almacenamiento. Sirve para demostrar el flujo de alta/login y no repetir usuario y contraseña a mano en cada prueba, pero **no es autenticación apta para producción**. Antes de usarlo con datos reales hace falta el backend de la sección 10 de la especificación (API + base de datos), con las contraseñas hasheadas del lado del servidor (bcrypt/argon2) y sesiones validadas ahí, no en el navegador.

## Cómo probarla

Un service worker (y también `crypto.subtle`, que se usa para las contraseñas) necesita `https://` o `localhost` — no funciona abriendo el `index.html` con doble clic (`file://`). Desde esta misma carpeta:

```bash
npx serve .
# o
python3 -m http.server 8080
```

Después abrí `http://localhost:8080` (o el puerto que indique) en Chrome. Iniciá sesión con cualquiera de las cuentas de ejemplo, o creá una nueva. Vas a ver el botón "⭳ Instalar app" en la barra lateral una vez logueado, y también podés instalarla desde el ícono de instalación de la barra de direcciones.

## Cómo publicarla para que la use el grupo

Cualquier hosting de archivos estáticos con HTTPS sirve. Las más simples y gratuitas:

- **GitHub Pages**: subir estos archivos a la raíz de un repo y activar Pages sobre esa rama.
- **Netlify** (arrastrar y soltar): entrar a app.netlify.com/drop y soltar esta carpeta.
- **Vercel**: `vercel deploy` parado en esta carpeta.

En los tres casos no hace falta build ni configuración extra: son archivos estáticos. Ojo: cada persona que entre desde un navegador distinto (o borre los datos del sitio) arranca con las 6 cuentas de ejemplo — las cuentas nuevas que registre una persona no las ve otra, porque todavía no hay backend compartido (ver más abajo).

## Estado de los datos

Los usuarios, notificaciones y confirmaciones son datos de demostración que se guardan en el `localStorage` del navegador de cada persona — no hay backend todavía, así que un aviso enviado por un docente, o una cuenta nueva creada, no las ve otra persona en otro dispositivo o navegador. Para la versión real, el paso siguiente es el que ya quedó en la especificación funcional: una API + base de datos compartida (sección 10, "Arquitectura tecnológica propuesta"), para que las cuentas y los avisos se sincronicen entre todos los que usan la app.

## Botón "Instalar app"

Usa el evento estándar `beforeinstallprompt`, que hoy soportan Chrome/Edge/Android. En Safari/iOS no existe ese cartel: ahí se instala desde Compartir → "Agregar a pantalla de inicio" (los metatags de `index.html` ya están puestos para que se vea bien instalada así).
