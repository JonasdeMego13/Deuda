# Control de Deudas 💸

Aplicación web para controlar préstamos personales: registra préstamos, calcula intereses, lleva el historial de pagos y genera reportes PDF.

- **Login local:** usuario y contraseña configurados en `config.js`.
- **Login con Google (opcional):** botón "Acceder con Google" para entrar con tu correo.
- **Sincronización automática:** los datos se guardan en tu Google Sheet a través de un **Google Apps Script** (gratis, sin clics ni popups).

## Despliegue en GitHub Pages

1. Sube este repositorio a GitHub (debe ser **público** o tener GitHub Pages habilitado).
2. Ve a **Settings → Pages**.
3. En *Branch*, selecciona `main` y la carpeta `/ (root)`.
4. Tu app quedará disponible en: `https://jonasdemego13.github.io/Deuda/`

## Configuración

Todos los datos se rellenan en el archivo **`config.js`**:

```js
// Login local
const LOCAL_USER = 'Jonas';
const LOCAL_PASS = '1q2w3e4r5t';

// Login con Google (opcional)
const GOOGLE_CLIENT_ID = 'tu_client_id.apps.googleusercontent.com';
const GOOGLE_ALLOWED_EMAILS = 'tu.correo@gmail.com';

// Sincronización
const GOOGLE_SCRIPT_URL = 'https://script.google.com/macros/s/TU_ID/exec';
const GOOGLE_SCRIPT_KEY = 'tu-clave-secreta';
```

### Configurar la sincronización con Google Apps Script (recomendado)

1. Crea una hoja de cálculo en [Google Sheets](https://sheets.new) y renombra la primera pestaña a `DB`.
2. Ve a [script.google.com](https://script.google.com) → **Nuevo proyecto**.
3. Borra el contenido y pega este código:

```js
const SHEET_ID = 'EL_ID_DE_TU_HOJA';          // parte de la URL entre /d/ y /edit
const TAB = 'DB';                              // nombre de la pestaña
const KEY = 'tu-clave-secreta';                // debe ser igual a GOOGLE_SCRIPT_KEY de config.js

function autorizado(e) {
  return e && e.parameter && e.parameter.k === KEY;
}

function doGet(e) {
  try {
    if (!autorizado(e)) return json({ error: 'noauth' });
    const ss = SpreadsheetApp.openById(SHEET_ID);
    const sh = ss.getSheetByName(TAB) || ss.getSheets()[0];
    const v = sh.getRange('A1').getValue();
    const db = v ? JSON.parse(v) : { deudores: [], moneda: 'S/', titular: '', updatedAt: 0 };
    return json({ db: db });
  } catch (err) { return json({ error: err.toString() }); }
}

function doPost(e) {
  try {
    if (!autorizado(e)) return json({ error: 'noauth' });
    const data = JSON.parse(e.postData.contents);
    const ss = SpreadsheetApp.openById(SHEET_ID);
    const sh = ss.getSheetByName(TAB) || ss.getSheets()[0];
    sh.getRange('A1').setValue(JSON.stringify(data));
    return json({ ok: true });
  } catch (err) { return json({ ok: false, error: err.toString() }); }
}

function json(obj) {
  return ContentService.createTextOutput(JSON.stringify(obj)).setMimeType(ContentService.MimeType.JSON);
}
```

4. Sustituye `EL_ID_DE_TU_HOJA` por el ID de tu hoja y `tu-clave-secreta` por una clave que elijas.
5. Clic en **Guardar** → **Implementar → Nueva implementación → Aplicación web**.
6. *Quién tiene acceso:* **Cualquier usuario** (así la app puede guardar sin pedir permisos cada vez).
7. *Ejecutar como:* **Yo** (tu cuenta).
8. Clic en **Implementar** y acepta los permisos de Google.
9. Copia la **URL del Web App** (termina en `/exec`) y pégala en `config.js` → `GOOGLE_SCRIPT_URL`.
10. Pon en `GOOGLE_SCRIPT_KEY` la misma clave que pusiste en el script.

> ⚠️ La URL del Web App y la clave son como una contraseña: quien las tenga podría leer o modificar tu hoja. No las compartas.

### Configurar el login con Google (opcional)

1. En [Google Cloud Console](https://console.cloud.google.com/) crea un proyecto y habilita la API de Google Sign-In.
2. Crea una pantalla de consentimiento de OAuth (tipo *Externo*) y añade tu correo en *Usuarios de prueba*.
3. Crea un **ID de cliente OAuth** de tipo *Aplicación web* y añade como origen autorizado: `https://jonasdemego13.github.io`.
4. Copia el *Client ID* en `config.js` → `GOOGLE_CLIENT_ID` y tu correo en `GOOGLE_ALLOWED_EMAILS`.
