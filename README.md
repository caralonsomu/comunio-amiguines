# Comunio Amiguines 26/27 — web del grupo

Web estática (un solo archivo `index.html`, sin build ni frameworks) para llevar la
clasificación, las liquidaciones y los pagos por Bizum de vuestra liga de Comunio.
Pensada para desplegarse en **Vercel** (como hicisteis con la porra del mundial) y
usar **Firebase Firestore**, gratis, como base de datos en vivo, para que el panel
Admin se actualice para todos en tiempo real.

Nadie necesita cuenta ni login para abrir el enlace — es una web pública normal.

## 1. Crear el proyecto de Firebase (gratis, ~3 minutos)

1. Ve a https://console.firebase.google.com/ y entra con tu cuenta de Google.
2. "Agregar proyecto" (o "Add project"). Ponle el nombre que quieras, por ejemplo
   `comunio-amiguines`. No hace falta activar Google Analytics.
3. Dentro del proyecto, en el menú de la izquierda entra en **Compilación (Build) →
   Firestore Database** y pulsa "Crear base de datos". Elige la ubicación más
   cercana (por ejemplo `eur3 (europe-west)`) y empieza en **modo de producción**
   (ya ajustamos las reglas nosotros en el paso 3).
4. Ve a **Configuración del proyecto** (el icono de engranaje, arriba a la
   izquierda) → pestaña **General** → baja hasta "Tus apps" → pulsa el icono
   `</>` (Web) para registrar una app web. Ponle un apodo (por ejemplo "web") y
   pulsa "Registrar app". **No** hace falta marcar la casilla de Firebase Hosting.
5. Firebase te muestra un bloque `firebaseConfig = { apiKey: "...", ... }`.
   Copia esos valores y pégalos en `index.html`, dentro de la constante
   `FIREBASE_CONFIG` (búscala cerca de la línea 10 del `<script>`, justo debajo
   del comentario "CONFIGURA AQUÍ TU PROYECTO DE FIREBASE"). Sustituye los 6
   campos (`apiKey`, `authDomain`, `projectId`, `storageBucket`,
   `messagingSenderId`, `appId`) por los tuyos.

## 2. Reglas de Firestore

En la consola de Firebase, ve a **Firestore Database → Reglas** y pega esto:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;
    }
  }
}
```

Pulsa "Publicar". Esto deja la base de datos abierta a lectura y escritura para
cualquiera que sepa la URL del proyecto (no solo para quien entre por la web).

**Importante sobre seguridad:** el PIN del panel Admin es una protección solo a
nivel de interfaz — impide que alguien vea los botones de editar sin saber el
PIN, pero no impide que alguien con conocimientos técnicos escriba directamente
en la base de datos sin él. Para un grupo de amigos con un enlace que no es
público, esto suele ser suficiente (es el mismo compromiso que la mayoría de
webs caseras tipo porra/quiniela). Si en algún momento queréis protección de
verdad, se puede añadir Firebase Authentication con una contraseña real —
dínoslo y lo preparamos.

## 3. Subir el proyecto a GitHub

Esto es lo que te comentaron: si despliegas en Vercel arrastrando la carpeta
directamente (o con `vercel --prod` suelto), cada despliegue "suelto" puede
generar su propia URL de vista previa, y solo si siempre publicas al mismo
proyecto conservas el dominio fijo. La forma estándar de evitarte estar
pendiente de esto es conectar Vercel a un repositorio de GitHub: cada cambio
que subas a GitHub se despliega automáticamente **al mismo dominio de
siempre**, sin tocar nada en Vercel.

**Si no quieres usar comandos de Git**, puedes subir los archivos desde el
navegador:
1. Entra en https://github.com/new, dale un nombre al repositorio (por ejemplo
   `comunio-amiguines`) y pulsa "Create repository". Puede ser público o
   privado, da igual para esto.
2. En la página del repositorio recién creado, pulsa el enlace **"uploading an
   existing file"**.
3. Arrastra `index.html`, `README.md` y `.gitignore` (los tres archivos de la
   carpeta `comunio-vercel`) y pulsa "Commit changes".

**Si prefieres usar Git** (la carpeta que te pasé ya viene con el repositorio
inicializado y el primer commit hecho):
```
cd comunio-vercel
git remote add origin https://github.com/TU_USUARIO/comunio-amiguines.git
git branch -M main
git push -u origin main
```
(Crea antes el repositorio vacío en https://github.com/new, sin añadirle README
ni licencia, para que la URL de arriba exista.)

## 4. Conectar GitHub con Vercel

1. Entra en https://vercel.com/new con la cuenta que usasteis para la porra.
2. En "Import Git Repository" autoriza el acceso a GitHub si te lo pide, y
   elige el repositorio `comunio-amiguines` que acabas de crear.
3. Deja la configuración por defecto (es un sitio estático, no hace falta
   ningún "build command") y pulsa "Deploy".
4. En un minuto tendrás una URL fija tipo `comunio-amiguines.vercel.app` — esa
   es la que compartes con el grupo, y ya no cambiará.

A partir de aquí, cualquier cambio que subas a GitHub (otro "commit changes"
desde el navegador, o un `git push`) hace que Vercel vuelva a desplegar
automáticamente en ese mismo dominio, sin que tengas que entrar en Vercel para
nada.

## 5. Primeros pasos ya con la web publicada

1. Abre la URL que te dio Vercel.
2. Ve a la pestaña **Admin**, introduce el PIN `2627` (puedes cambiarlo editando
   la constante `ADMIN_PIN` en `index.html` antes de desplegar).
3. Pulsa **"Importar histórico"** — carga de una vez las jornadas 1 a 4 y los
   pagos que ya teníais apuntados en el Excel, así no hay que teclearlos a mano.
4. A partir de ahí, cada semana: pestaña Admin → elige la jornada → introduce
   posición y puntos de cada equipo → "Guardar jornada". Los pagos por Bizum se
   registran en la misma pestaña, más abajo.

Cualquier amigo con el enlace puede abrir la web y ver todo (Resumen, Jornadas,
Reglas) sin PIN ni cuenta; solo hace falta el PIN para entrar en el modo de
edición de Admin.

## Actualizaciones futuras

- Cambios de **datos** (jornadas, pagos) se guardan directamente desde el panel
  Admin de la propia web — no requieren tocar GitHub ni Vercel para nada.
- Cambios de **código o diseño** de la página, si nos pides algún ajuste más
  adelante: subimos el `index.html` actualizado a tu mismo repositorio de
  GitHub (o te lo paso para que lo subas tú con "uploading an existing file"),
  y Vercel lo despliega solo al dominio de siempre.
