# efecto-lab.com — landing de presentación

Una sola página estática. Sin build, sin dependencias, sin framework.

## Desplegar

Subir `index.html` a la raíz del hosting. Nada más.

En Vercel, Netlify o Cloudflare Pages: arrastrar la carpeta, o conectar el repo
sin comando de build y con la raíz del proyecto como directorio de salida.

## Ver en local

```bash
py -m http.server 8000    # o: npx serve .
```

## Qué hay adentro

`index.html` contiene el markup, los estilos y el campo reactivo del fondo.

Dos efectos, los dos sobre el wordmark. El fondo quedo liso a proposito: la
audacia se gasta en un solo lugar.

- **La hendidura.** Al pasar el cursor, las letras cercanas se hunden hacia
  atras siguiendo una campana de Gauss, y el punto de fuga persigue al puntero
  para que la letra se vaya derecho para atras y no hacia el centro. El cursor
  es la causa; la deformacion es el efecto.

  `DEPTH` manda todo: mas alto y las letras encogen tanto que se abren huecos
  y el wordmark se lee roto. 78px deja la letra bajo el cursor al 0,84 de
  escala aparente, que se nota sin romper la palabra.

- **El cortocircuito.** El 96% del ciclo no pasa nada. Cada 9s hay una rafaga
  de ~0,5s: `EFECTO` y `LAB` saltan en sentidos opuestos, el color cae casi al
  fondo, y el filete de `LAB` se llena por un instante como una subida de
  tension.

Ambos respetan `prefers-reduced-motion`.

## Dos trampas que ya se pagaron

**No animar `opacity` sobre `.mark b` / `.mark i`.** Animar la opacidad de esos
elementos obliga al navegador a aplanar el 3D de sus hijos, y las letras dejan
de hundirse: el `translateZ` queda puesto pero sin perspectiva. Por eso el
apagon del cortocircuito se hace con `color` y `-webkit-text-stroke-color`, que
no tocan el compositing.

**El centinela de "sin cursor" no puede ser una coordenada.** La primera
version usaba `px = -1e5` para decir "no hay cursor", pero el punto de fuga se
iba a esa misma coordenada y cualquier `z` residual proyectaba las letras fuera
de la pantalla: el wordmark explotaba al sacar el mouse. Ahora hay un booleano
`have` aparte, y el punto de fuga vuelve al centro y se acota a la caja.

El bloque `.mark` es el wordmark **provisorio**, hecho sólo con tipografía
(Anton). Cuando exista el logo, se reemplaza ese bloque y nada más depende
de él.

> El `@font-face` "Display Fallback" del principio no es decorativo: mientras
> Anton baja, el wordmark se pinta con la tipografía del sistema. Sin ajustar,
> el fallback mide 50% más ancho que Anton y, como el wordmark es `nowrap`, se
> sale de la pantalla en el primer pintado. El `size-adjust:92%` deja la
> diferencia en 0,6%. No lo saques.

## Pendiente

- **Crear la casilla `hola@efecto-lab.com`** — la página ya enlaza ahí.
- **Imagen de OG** (`og.png`, 1200×630) para las previews al compartir el link.
  Los meta tags están puestos; falta el archivo y su `<meta property="og:image">`.
- **Logo y sistema visual** (Nico) — reemplaza el wordmark tipográfico.
- Definir dominio definitivo. El documento de naming marca que `efecto-lab.com`
  cuesta dictarlo en voz alta y sugiere revisar `efectolab.cl` y `efectolab.lat`.
  La URL aparece en `canonical` y en los `og:` del `<head>`.
