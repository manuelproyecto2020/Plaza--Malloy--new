# Plaza Malloy Arena, sitio de eventos

Sitio estático con las 4 páginas de eventos de Plaza Malloy Arena (Ferris, TX).
Cada evento es una página con su flyer, su mapa de mesas, sus precios y su botón
de reserva conectado a Stripe (Payment Links).

## Estructura

```
index.html               Los Dos de Tamaulipas   (se abre en la raíz "/")
el-fantasma.html         El Fantasma             (/el-fantasma)
rienda-real.html         Rienda Real y Pócima    (/rienda-real)
la-zenda-nortena.html    La Zenda Norteña        (/la-zenda-nortena)
assets/                  logo, video, flyers, mapas y fotos del recinto
vercel.json              activa las URLs limpias (sin ".html")
```

Cada página enlaza a las otras tres en la sección "Más Eventos".

---

## 1. Conectar los botones con Stripe (Payment Links)

El mismo Stripe sirve para las 4 páginas; lo único que cambia es el precio.
Necesitas **8 Payment Links** en total (Gold y Silver de cada evento).

### Precios por página

| Archivo                | Evento                        | Fecha            | Gold  | Silver |
|------------------------|-------------------------------|------------------|-------|--------|
| index.html             | Los Dos de Tamaulipas         | 29 Ago 2026      | $500  | $400   |
| la-zenda-nortena.html  | La Zenda Norteña              | 5 Sep 2026       | $500  | $400   |
| el-fantasma.html       | El Fantasma                   | 12 Sep 2026      | $600  | $500   |
| rienda-real.html       | Rienda Real y Pócima Norteña  | 22 Ago 2026      | $300  | $200   |

### Crear cada Payment Link en Stripe

1. Entra a Stripe, menú **Payment Links**, botón **New / Crear**.
2. **Add product**: ponle un nombre claro para identificarlo, por ejemplo
   "Mesa Gold, Los Dos de Tamaulipas". Pon el **precio** correspondiente
   (por ejemplo 500 USD) y marca **One time / Pago único**.
3. (Opcional pero recomendado) en "Options" activa que pida **nombre y teléfono**
   del cliente, para tener sus datos.
4. **Create link** y **copia la URL** (queda como `https://buy.stripe.com/xxxxx`).
5. Repite hasta tener los 8 links (Gold y Silver de cada evento).

### Pegar los links en cada página

Abre cada archivo `.html` con un editor de texto, busca (Ctrl+F) **`var PAGO`**
cerca del final, dentro de `<script>`, y reemplaza `"PENDING"` por tu link:

```js
var PAGO = {
  gold:   "https://buy.stripe.com/XXXXXX",   // Mesas Gold
  silver: "https://buy.stripe.com/YYYYYY"    // Mesas Silver
};
```

Haz esto en los 4 archivos (cada uno con sus 2 links). Nada más.

### Qué hace el botón

Cuando el cliente elige una mesa y da "Confirmar Reserva", el botón abre el link
de pago correcto (Gold o Silver) y le agrega automáticamente el número de mesa
como `client_reference_id` (por ejemplo `zenda-Mesa-23`). Así, en el panel de
Stripe ves **qué mesa** pagó cada persona.

> Nota importante: los Payment Links cobran bien y registran la mesa, pero por sí
> solos **no bloquean una mesa en tiempo real**. Si dos personas abren la misma
> mesa a la vez, ambas podrían pagarla. Para bloqueo automático (estado libre,
> bloqueada, vendida) se necesita la versión con base de datos (Supabase) y
> Stripe del lado servidor, que es el siguiente paso.

---

## 2. Subir a GitHub

### Opción A, sin comandos (desde la web)

1. Entra a github.com y crea un repositorio nuevo (botón **New**), por ejemplo
   `plaza-malloy-arena`.
2. En el repo vacío, pulsa **"uploading an existing file"**.
3. Arrastra **todo el contenido de esta carpeta** (los 4 `.html`, `vercel.json`,
   `README.md` y la carpeta `assets`) a la ventana.
4. Pulsa **Commit changes**.

### Opción B, con Git (terminal)

Dentro de esta carpeta:

```bash
git init
git add .
git commit -m "Plaza Malloy Arena, sitio de eventos"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/plaza-malloy-arena.git
git push -u origin main
```

---

## 3. Publicar en Vercel

1. Entra a vercel.com e inicia sesión (puedes usar tu cuenta de GitHub).
2. **Add New, Project** y elige **Import Git Repository**, selecciona el repo.
3. En la configuración:
   - **Framework Preset**: Other (o "No Framework").
   - **Build Command**: déjalo vacío.
   - **Output Directory**: déjalo vacío.
   - **Root Directory**: `./`
4. Pulsa **Deploy**. En unos segundos queda online.

Tus páginas quedan en:

```
https://tu-proyecto.vercel.app/                    Los Dos de Tamaulipas
https://tu-proyecto.vercel.app/el-fantasma         El Fantasma
https://tu-proyecto.vercel.app/rienda-real         Rienda Real y Pócima
https://tu-proyecto.vercel.app/la-zenda-nortena    La Zenda Norteña
```

### Actualizar después (por ejemplo al pegar los links de Stripe)

Cada vez que subas un cambio a GitHub, Vercel **redespliega solo**. Es decir:
puedes desplegar ya con los links en "PENDING" y luego, cuando pegues los
Payment Links y hagas commit, el sitio se actualiza automáticamente.

### Dominio propio (opcional)

En Vercel, entra al proyecto, **Settings, Domains**, y agrega tu dominio
(por ejemplo `plazamalloyarena.com`). Vercel te indica los registros DNS a poner.

---

## Resumen del flujo recomendado

1. Crea los 8 Payment Links en Stripe y pégalos en los 4 archivos.
2. Sube la carpeta a GitHub.
3. Importa el repo en Vercel y pulsa Deploy.
4. (Opcional) conecta tu dominio.

Listo, las 4 páginas quedan online con pago funcionando.
