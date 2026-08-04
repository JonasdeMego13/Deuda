# Control de Deudas 💸

Aplicación web para controlar préstamos personales: registra préstamos, calcula intereses, lleva el historial de pagos y genera reportes PDF.

- **Login local:** usuario y contraseña configurados en `config.js`.
- **Login con Google (opcional):** sincroniza los datos con una hoja de cálculo de Google Sheets.
- **Almacenamiento:** los datos se guardan en el `localStorage` de tu navegador.

## Despliegue en GitHub Pages

1. Sube este repositorio a GitHub (debe ser **público** o tener GitHub Pages habilitado).
2. Ve a **Settings → Pages**.
3. En *Branch*, selecciona `main` y la carpeta `/ (root)`.
4. Tu app quedará disponible en: `https://jonasmemego13.github.io/Deuda/`

## Configuración

Todos los datos se rellenan en el archivo **`config.js`**:

```js
// Login local
const LOCAL_USER = 'Jonas';
const LOCAL_PASS = '1q2w3e4r5t';

// Sincronización con Google Sheets (opcional)
const GOOGLE_CLIENT_ID = 'TU_CLIENT_ID.apps.googleusercontent.com';
const GOOGLE_SHEET_ID = 'TU_SHEET_ID';
const GOOGLE_SHEET_TAB = 'DB';
const GOOGLE_ALLOWED_EMAILS = 'tu.correo@gmail.com';
```

### Configurar la sincronización con Google Sheets

1. Entra en [Google Cloud Console](https://console.cloud.google.com/) y crea un proyecto.
2. Ve a **APIs y servicios → Biblioteca** y habilita **Google Sheets API**.
3. Ve a **Pantalla de consentimiento de OAuth** y crea una app (tipo *Externo*). Añade tu correo en *Usuarios de prueba*.
4. Ve a **Credenciales → Crear credenciales → ID de cliente OAuth**, tipo **Aplicación web**.
5. En *Orígenes de JavaScript autorizados* añade: `https://jonasmemego13.github.io`
6. Copia el *Client ID* generado y pégalo en `config.js` → `GOOGLE_CLIENT_ID`.
7. Crea una hoja de cálculo en Google Sheets y renombra su primera pestaña a `DB`.
8. El `GOOGLE_SHEET_ID` es la parte de la URL entre `/d/` y `/edit`.
9. En `GOOGLE_ALLOWED_EMAILS` escribe el/los correos con los que podrás iniciar sesión con Google.

> La primera vez que inicies sesión con Google, la app te pedirá permiso para acceder a tu hoja de cálculo y guardará ahí una copia de la base de datos. Después, cada cambio se sincroniza automáticamente.
