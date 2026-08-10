# Winflex · Control de OC y Entregas

App de una sola página para seguir órdenes de compra y entregas por cliente, y saber en todo momento cuántos kilos faltan entregar.

Reemplaza el análisis manual de pendientes que se rehacía cada quince días cruzando la planilla de producción contra la de administración.

## Uso

Abrir `index.html`. No requiere instalación, servidor ni build.

Publicada en GitHub Pages: `https://winflexsa.github.io/<repo>/`

## Qué hace

- **Una solapa por cliente** más una de **Totales** consolidada.
- **Pendientes**: los pedidos abiertos agrupados en Vencidas / Próximos 15 días / Más adelante, con un resumen arriba de cuántos kilos faltan por artículo.
- **Derivación por OC**: al tocar cualquier pedido se abre el detalle remito por remito con el acumulado, que es de dónde sale el saldo.
- **Barra de avance**: entregado contra pedido. Si se entregó de más, el excedente aparece rayado en naranja pasando la marca del pedido.
- **Alertas**: OC vencidas y OC con entregas por encima de lo pedido.
- **Carga de OC desde PDF**: reconoce el formato de Don Yeyo, Molinos Río de la Plata, La Italiana y El Artesano. Siempre abre una pantalla de revisión antes de guardar.
- **Pedidos sin OC**: para clientes que no mandan orden de compra (Dantelli). Se deja el número vacío y se usa el campo Referencia.
- **Planilla**: exporta el pendiente a CSV para abrir en Excel.
- **Respaldo**: exporta e importa toda la base en JSON.

## Cómo cargar

**Órdenes de compra.** Arrastrar el PDF en la solapa correspondiente, revisar lo detectado y guardar. Si el cliente no manda OC, cargarla a mano dejando el número vacío.

Cuando una OC trae varias líneas o un cronograma de entregas escalonado, cada línea se carga como un pedido aparte con su propia fecha. Así cada tramo lleva su propio saldo y su propio atraso.

**Entregas.** Solapa Entregas: fecha, número de remito, OC contra la que se imputa, kg totales, cantidad de bobinas, kg por bobina, cantidad de pallets y observación.

Los kg por bobina se calculan solos a partir de los kg totales y la cantidad de bobinas. Si el producto de bobinas por kg/bobina no coincide con los kg totales por más de un 3%, aparece un aviso y pide confirmación antes de guardar. Ese control es el que detecta un pallet de un formato metido en una OC de otro.

**Un remito repartido entre dos OC** se carga como dos líneas con el mismo número de remito, una por cada OC.

## Criterios

- Los kilos son **peso neto**, sin conos. Es el criterio con el que se factura.
- Las entregas cancelan las OC **por orden de antigüedad**.
- Se usa el **número de remito**, no el de factura: el remito refleja la fecha real de salida del material.

## Datos

Se guardan en el navegador (`localStorage`), por equipo. No hay backend ni sincronización: para pasar los datos de una máquina a otra se usa Respaldo → Importar.

Si la app la van a usar varias personas a la vez, hay que migrar a persistencia central, como el resto de las apps del ecosistema.

## Dependencias

Ninguna para operar. Al arrastrar el primer PDF se carga `pdf.js` desde cdnjs; sin conexión la lectura de PDF no funciona, el resto sí. Las tipografías (Archivo, IBM Plex Sans, IBM Plex Mono) vienen de Google Fonts, con alternativas del sistema.

## Límites conocidos

- Los PDF escaneados no se pueden leer: no tienen texto. Avisa y hay que cargar a mano.
- Si un cliente cambia el formato de su OC, el parser deja de reconocerlo. No rompe: cae en la pantalla de revisión mostrando el texto crudo para cargar a mano.
- Las OC de El Artesano vienen con el campo de cantidad en cero. La app marca la fila en naranja y no inventa el número.
