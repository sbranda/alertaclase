# Tablón — versión PWA

Este es el prototipo de "Tablón" empaquetado como Progressive Web App: instalable en el celular o la PC, con el ícono en el escritorio/pantalla de inicio y el shell disponible offline.

## Por qué son estos archivos y no el link que ya tenías

El link que te pasé antes vive en el hosting de artefactos de Claude, dentro de un sandbox que no permite registrar un service worker ni servir un manifest — por eso ahí nunca aparece el cartel de "instalar". Estos archivos son el mismo diseño y la misma lógica, reempaquetados como un sitio normal para que los alojes donde quieras y sí sea instalable de verdad.

## Contenido

Todo va junto en la misma raíz del sitio (sin subcarpetas) porque `manifest.json` y `service-worker.js` referencian los íconos con rutas relativas planas (`icon-192.png`, no `icons/icon-192.png`):

- `index.html` — la app (mismo prototipo: roles de personal + estudiantes, notificaciones y confirmación con OK).
- `manifest.json` — nombre, ícono y colores que usa el sistema operativo al instalarla.
- `service-worker.js` — cachea el shell de la app para que abra sin conexión.
- `icon-192.png`, `icon-512.png`, `icon-maskable-192.png`, `icon-maskable-512.png`, `apple-touch-icon.png`, `favicon-64.png` — íconos en los tamaños que piden Android/iOS/escritorio.

## Cómo probarla

Un service worker necesita `https://` o `localhost` — no funciona abriendo el `index.html` con doble clic (`file://`). Desde esta misma carpeta:

```bash
npx serve .
# o
python3 -m http.server 8080
```

Después abrí `http://localhost:8080` (o el puerto que indique) en Chrome. Vas a ver el botón "⭳ Instalar app" en la barra lateral, y también podés instalarla desde el ícono de instalación de la barra de direcciones.

## Cómo publicarla para que la use el grupo

Cualquier hosting de archivos estáticos con HTTPS sirve. Las más simples y gratuitas:

- **GitHub Pages**: subir estos archivos a la raíz de un repo y activar Pages sobre esa rama.
- **Netlify** (arrastrar y soltar): entrar a app.netlify.com/drop y soltar esta carpeta.
- **Vercel**: `vercel deploy` parado en esta carpeta.

En los tres casos no hace falta build ni configuración extra: son archivos estáticos.

## Estado de los datos

Como en el prototipo original, los usuarios y notificaciones son datos de demostración que se guardan en el `localStorage` del navegador de cada persona — no hay backend todavía. Para la versión real, el paso siguiente es el que ya quedó en la especificación funcional: una API + base de datos compartida (sección 10, "Arquitectura tecnológica propuesta"), para que un aviso enviado por un docente lo vean todos los estudiantes, no solo en el navegador de quien lo mandó.

## Botón "Instalar app"

Usa el evento estándar `beforeinstallprompt`, que hoy soportan Chrome/Edge/Android. En Safari/iOS no existe ese cartel: ahí se instala desde Compartir → "Agregar a pantalla de inicio" (los metatags de `index.html` ya están puestos para que se vea bien instalada así).
