# Tableros — Bernard Kizer

Sitio estático con los reportes del embudo de la subcuenta de GoHighLevel de Bernard Kizer
(`qv9rF8ulG9G4xN53nl3v`).

| Ruta | Página |
|---|---|
| `/` | Hub: qué mide cada versión, para poder compararlas |
| `/v2` | **Señal y silencio** — el reporte vivo |
| `/v1` | **De 1.607 mensajes a 7 agendas** — la versión anterior, congelada |

Las dos versiones se mantienen publicadas a propósito: miden cosas distintas y se están
comparando para decidir qué métricas merecen sobrevivir a la siguiente.

## Señal y silencio (`/v2`)

**Ventana de datos: 21/08/2026 – 04/09/2026 (14 días), 5.897 conversaciones, 45,1 % con
respuesta.** Las cifras están congeladas en el HTML; la página no consulta la API en tiempo
de ejecución.

Se paginaron las conversaciones de la subcuenta por fecha de creación y se leyó cada hilo
abierto dentro de la ventana, mensaje por mensaje. Cada latencia se calcula comparando el
sello de tiempo de un mensaje con el del anterior en el mismo hilo — no son estimaciones.

La **fuente de tráfico** se deduce de la firma del primer mensaje del lead: los trigger links
de la subcuenta redirigen a `wa.me` con un texto pre-cargado distinto cada uno, así que el
origen queda escrito en el propio mensaje. Lo que no llega por trigger link se clasifica por
canal, que dice por dónde entró pero no qué lo motivó. Esa brecha de atribución es el
hallazgo central del reporte, y sigue abierta.

| Bloque | Contenido |
|---|---|
| Conversaciones | Abiertas, respondidas y continuadas (≥4, ≥6, ≥20 mensajes), timeline diaria apilada por canal, canal y número de entrada |
| Fuentes de tráfico | Conversaciones, profundidad, tasa de respuesta, calificadas y agendadas por cada fuente |
| Calificación | Embudo acumulado del agente de IA por etiqueta, global y por fuente |
| Ritmo | Latencia del lead para contestar la primera vez y la nuestra para responderle |
| Cuándo | Hora y día de apertura, con la porción que recibió respuesta |
| Campañas y citas | Contactos por campaña saliente y agenda por closer |
| Países | De la ventana y de la base entera (~106 k contactos) |

## De 1.607 mensajes a 7 agendas (`/v1`)

**Ventana: 22/07/2026 – 21/08/2026 (30 días).** Congelada, no se regenera.

Lee el embudo desde el envío saliente en vez de desde la conversación: cuántos mensajes
salieron, cuántos llegaron de verdad y qué pasó después. Conserva dos cortes que la v2 no
tiene — la **entrega real de los envíos** y el **rendimiento por tag de campaña** — y por eso
sigue publicada.

## Despliegue

Sitio estático sin build. `vercel.json` activa `cleanUrls`, redirige las rutas antiguas y
fija las cabeceras. No hay comando de build, dependencias ni variables de entorno.

El HTML es autocontenido — la única petición externa es a Google Fonts. Funciona en tema
claro y oscuro según la preferencia del navegador.

## Datos personales

Estas páginas **no publican nombres, teléfonos ni correos de leads**: todas las cifras son
agregadas. Las cabeceras mantienen `X-Robots-Tag: noindex, nofollow`, que evita la
indexación pero **no es control de acceso**: cualquiera con el enlace puede abrir el sitio.
Por eso ninguna página puede llevar datos personales incrustados.

El 2026-09-07 se retiró `growth-hub.html` por incumplir esto: llevaba 30 teléfonos y 15
correos de leads en el propio HTML. Sigue en el historial de git.

## Regenerar con datos frescos

Las páginas se compilan desde plantillas en el proyecto `smg`:

```bash
python scripts/etl.py 14     # extrae de la API v2 -> data.json  (~13 min)
python scripts/build.py      # genera v2.html y el hub index.html
python scripts/deploy.py     # copia aquí, commitea y pushea
```

`deploy.py` publica `index.html` y `v2.html`. Las versiones antiguas no se regeneran: viven
commiteadas aquí y no se tocan.
