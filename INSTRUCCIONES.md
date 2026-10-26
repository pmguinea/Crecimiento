# Lo que falta — proyecto `crecimiento-guineas`

Ya está hecho: proyecto creado, Firestore `(default)` funcionando, acceso con
Google activado y app web registrada. La configuración ya va metida en
`index.html`, no tienes que tocar nada de eso.

Quedan tres cosas.

---

## 1 · Publicar las reglas de Firestore

El archivo `index.html` ya lleva dentro los dos correos autorizados
(`pmguinea@gmail.com` y `luciarsierra@gmail.com`). No hay que editarlo.

Falta poner esos mismos correos en el lado del servidor, que es lo que de
verdad protege los datos.

Consola de Firebase → `Bases de datos y almacenamiento` → **Firestore Database**
→ pestaña **Reglas**. Borra lo que haya y pega esto tal cual:

```
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {

    function autorizado() {
      return request.auth != null
          && request.auth.token.email_verified == true
          && request.auth.token.email.lower() in [
               'pmguinea@gmail.com',
               'luciarsierra@gmail.com'
             ];
    }

    match /kids/{id}       { allow read, write: if autorizado(); }
    match /treatments/{id} { allow read, write: if autorizado(); }
    match /{document=**}   { allow read, write: if false; }
  }
}
```

Pulsa **Publicar**.

---

## 2 · Subirlo a GitHub

Son **7 archivos**, todos a la raíz del repositorio y con su nombre exacto:

```
index.html                 la app
manifest.webmanifest       nombre, color e iconos al instalarla
sw.js                      permite abrirla sin conexión
icon-192.png
icon-512.png
icon-maskable-512.png      versión recortable de Android
apple-touch-icon.png
favicon.png
```

Todo desde el navegador, sin instalar nada.

1. Cuenta en <https://github.com> si no la tienes.
2. Botón **+** arriba a la derecha → **New repository**.
3. Nombre: `crecimiento`. Déjalo **Public**.
   *(Private obligaría a pagar para publicar la página. Público no es problema:
   en los archivos no hay secretos, los datos están en Firestore y protegidos
   por las reglas del paso 1.)*
4. Marca **Add a README file** → **Create repository**.
5. Dentro: **Add file → Upload files** → selecciona **todos** los archivos de la
   lista de arriba a la vez → **Commit changes**.
6. Pestaña **Settings** → **Pages** (menú izquierdo).
7. **Source**: *Deploy from a branch*. Branch: **`main`**, carpeta **`/ (root)`**
   → **Save**.
8. Un par de minutos y arriba aparece la dirección:
   `https://TUUSUARIO.github.io/crecimiento/`

> Si en el móvil no ves "Add file", abre el menú de Chrome y marca
> **Sitio para ordenadores**.

## 3 · Autorizar el dominio en Firebase

Sin esto, el botón de entrar dará `auth/unauthorized-domain`.

1. Consola de Firebase → **Authentication** → pestaña **Settings**
   (o *Configuración*) → **Dominios autorizados** → **Agregar dominio**.
2. Escribe solo `TUUSUARIO.github.io`
   Sin `https://`, sin la carpeta del final, sin barra.

---

## Probarlo

1. Abre la dirección. Debe salir la pantalla con el botón **Entrar con Google**.
2. Entra con `pmguinea@gmail.com`. Arriba a la derecha aparecerá tu nombre con
   un icono de nube.
3. Pestaña **Hijos** → añade uno con su fecha de nacimiento → apunta peso y talla.
4. Que Lucía abra la misma dirección y entre con su cuenta: debe ver lo mismo.
   Apunta algo desde un móvil y comprueba que aparece en el otro sin recargar.

**Instalarla en el móvil**: abre la dirección y usa **Instalar aplicación** o
**Añadir a pantalla de inicio** (Chrome: menú de tres puntos. Safari: botón de
compartir). Aparece con su icono de gota, se abre a pantalla completa sin barra
del navegador, y funciona aunque no haya cobertura: consultar dosis y ver las
últimas medidas va sin conexión, y lo que apuntes se sincroniza al volver.

---

## Si falla

| Qué ves | Qué pasa |
|---|---|
| `auth/unauthorized-domain` | Falta el paso 3, o escribiste mal el dominio. |
| "La cuenta X no tiene acceso" | Ese correo no está en `CORREOS_PERMITIDOS`, o está escrito distinto. |
| "Sin permiso: revisa las reglas de Firestore" | Los correos de las reglas no coinciden con los del archivo. |
| Arriba pone "En este dispositivo" | El archivo subido no es el bueno, o se rompió al editarlo. |
| Se instala pero con una miniatura de la web en vez del icono | Faltan los PNG o el `manifest.webmanifest` en la raíz del repositorio. |
| Cambias el archivo y el móvil sigue mostrando lo viejo | Es la caché. Cierra y abre la app, o recárgala desde el navegador. |

---

## Después

Para cambiar algo: en GitHub, abre `index.html`, el lápiz de **Edit**, y
**Commit changes**. En un minuto está actualizado en los dos móviles (puede
hacer falta cerrar y volver a abrir la app para que suelte la copia en caché).
**Los datos no se tocan**: viven en Firestore, no en el archivo.

Para una copia de seguridad: pestaña **Hijos** → **Exportar a Excel**.

---

## Recordatorio

Las dosis siguen la guía de la AEPap, pero esto es una ayuda de cálculo para
casa, no sustituye al pediatra. Comprueba siempre la concentración del envase:
el mismo jarabe se vende en 20 y en 40 mg/ml.
