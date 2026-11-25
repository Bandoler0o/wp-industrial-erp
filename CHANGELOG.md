# Changelog

Release notes of **WP Industrial ERP**, newest first (in Spanish, as written for the users).

## v0.27.0 · VERIFICACIÓN EN DOS PASOS, SESIONES, AVISOS Y COPIAS CIFRADAS

```text
· Verificación en dos pasos (Google / Microsoft Authenticator), obligatoria para administradores y para quien ve importes
  (ventas, compras, tesorería, contabilidad, informes, ajustes); opcional para el resto. Quien la tiene que tener y no
  la tiene solo puede ir a «Mi seguridad» (🔒 junto a su nombre) hasta configurarla. 10 códigos de recuperación de un uso.
  El código se pide en la pantalla de entrada de WordPress; contraseñas de aplicación desactivadas (se la saltarían).
  Emergencia: define('PDS_2FA_DESACTIVAR', true); en wp-config.php. Un administrador puede quitársela a otro.
· Sesiones: caducan a las 12 h (7 días con «Recuérdame») y tras 120 min sin usar la gestión (configurable).
  «Cerrar mi sesión en los demás dispositivos»; un administrador puede cerrar todas las de otro.
· Entradas en Auditoría (IP, navegador, con o sin verificación). Avisos por correo: entrada desde una IP nueva, IP
  bloqueada, código de recuperación usado, exportación completa, descarga de copia, nuevo administrador.
· Copias de seguridad: solo salen del servidor cifradas (ZIP AES-256 con contraseña de 12+ caracteres; se abre con
  7-Zip/WinRAR o se restaura en la gestión). Sin contraseña no se envía nada.
· La clave de la API de Claude se guarda cifrada (las contraseñas SMTP ya lo estaban).
· Ajustes → Seguridad: usuarios con acceso, su verificación y última entrada; nuevas comprobaciones y ajustes.
```

## v0.26.0 · SEGURIDAD

```text
· Cabeceras en la gestión: Content-Security-Policy (solo recursos del propio dominio, sin iframes de otras webs,
  formularios solo al propio dominio, sin plugins/objetos), X-Frame-Options, X-Content-Type-Options, Referrer-Policy
  same-origin, Permissions-Policy, Cross-Origin-Opener/Resource-Policy, Cache-Control no-store, X-Robots-Tag y HSTS con HTTPS.
· En el resto de la web (se puede apagar): nosniff, X-Frame-Options SAMEORIGIN, Referrer-Policy, Permissions-Policy y HSTS.
· Acceso: bloqueo de la IP tras 5 intentos fallidos durante 15 min (configurable); el error no dice si el usuario
  existe; XML-RPC apagado; la API REST y ?author= no dan los nombres de usuario; sin versión de WordPress a la vista.
· Ajustes → Seguridad: lista de comprobaciones (HTTPS, usuario «admin», errores visibles, edición de código,
  PHP, WordPress y plugins al día, nº de administradores), interruptores y últimos bloqueos.
· La carpeta de importaciones se protege con el mismo .htaccess que adjuntos y copias (Apache 2.2 y 2.4).
```

## v0.25.0 · IMPORTAR ARCHIVOS GRANDES Y RECUPERAR EL NÚMERO AL BORRAR

```text
· Importar: si los archivos pesan más de lo que admite el servidor (se ve en la pantalla), el navegador los sube solos
  por trozos y luego sigue como siempre. Antes PHP tiraba el envío entero y se volvía al panel sin avisar; ahora, si
  pasa en cualquier pantalla, sale un aviso con el tamaño enviado y el límite.
· Los .xlsx se leen fila a fila (PARTES_DIARIOS_2026: 7.651 filas con 14 MB de memoria). La hoja SEGUIMIENTO CLIENTE
  (144 MB por dentro, con FALSE hasta la fila 1.048.572) no se lee.
· Borrar: al borrar el último parte o albarán de su serie, el siguiente vuelve a tener ese número (borras ALB/260448 y el
  siguiente es ALB/260448). Si no era el último, queda el hueco. Lo importado de golden no libera número.
  Botón «🗑 Borrar» también en el editor; en una copia recién duplicada, «🗑 Descartar esta copia».
```

## v0.24.0 · DEL PARTE DE HORAS AL PARTE DEL CLIENTE, Y ALTA DE TRABAJADORES

```text
· Partes de horas: los trabajos de tipo «Parte» hechos en el ERP salen en «Por pasar a parte del cliente» con su botón
  «→ Hacer parte» (también en la ficha de cada línea). Crea el PT/PR del cliente de la obra con: un oficial por operario
  (las «H. parte» si se pusieron; si no, las trabajadas), sus horas extra y festivas, los materiales, OT, responsable
  y trabajo realizado. Los precios los pone el ERP (tarifa, catálogo o contrato); avisa de las líneas sin precio.
  Lo puede hacer el encargado (no ve precios). Las horas quedan enlazadas: no se pasa dos veces y en el resultado de la
  obra cuentan una sola vez. Si se borra el parte, el trabajo vuelve a estar pendiente.
· Importar → «Trabajadores»: plantilla Trabajadores.xlsx (Código, Nombre, Categoría, Email, Teléfono, DNI, costes, Activo).
  Cruza por código de nómina o nombre; las celdas vacías no borran nada; volver a subirla actualiza.
```

## v0.23.0 · PARTES DIARIOS DEL EXCEL Y PARTES SIN PRECIOS PARA EL ENCARGADO

```text
· Importar → se puede subir PARTES_DIARIOS_2026.xlsx tal cual. Lee la hoja PARTES DIARIOS (aunque no sea la primera)
  y, para saber la obra, la hoja PROYECTOS (cuenta analítica 1002529 → proyecto 25.29 NORTEC ORION; 1001001 se
  separa entre REFINOR y REFINOR CHEMICALS por la empresa). Cada operario de cada trabajo entra como un parte de horas con
  horas, extra, festivas y horas del parte del cliente, tipo (parte/presupuesto), OT, responsable y trabajo realizado;
  los materiales del trabajo van con él (sin mover stock). Sin proyecto: el más usado por esa empresa, marcado.
  Operarios nuevos → trabajadores con su código de nómina y el €/h de la hoja «HE Y HF». Volver a importar sustituye
  lo importado en esas fechas; lo hecho en el ERP no se toca. Las fechas futuras (21/11/2028) se saltan.
· Precios ocultos: quien no tiene Ventas, Compras, Tesorería, Contabilidad ni Informes (el encargado) no ve precios,
  importes ni costes en ningún sitio: ni en el editor del parte, ni en la ficha, ni en las listas, ni en su panel,
  ni en el código de la página. Al guardar, el ERP pone los precios del cliente (tarifa o catálogo, precios de contrato
  PR, partidas de material) y conserva los que la oficina haya cambiado. No puede bajar el PDF valorado ni enviarlo:
  eso lo hace la oficina (sale «Sin enviar» en su lista).
· Parte del día (Producción → Partes de horas), pensado para el encargado: fecha, obra, tipo, trabajo, OT y responsable;
  tocar el nombre de un operario pone 8 h; «Los mismos que el último día en esta obra»; buscador de operarios y los
  que no han trabajado en 3 semanas ocultos; trabajos recientes de la obra al escribir; materiales habituales primero.
  «Guardar y hacer otro» vuelve con la misma fecha. Horas extra, festivas y horas del parte del cliente.
· Trabajadores: coste hora festiva, código de nómina y email (para casarlos con la app de fichajes).
· Las horas festivas cuentan en el resultado de la obra, el panel y el resumen semanal.
```

## v0.22.0 · NÚMERO AL CREAR Y DUPLICAR, CONTROL DE ENVÍO POR CORREO

```text
· Parte o albarán nuevo: el editor enseña ya el número que tendrá (PT/260695, ALB/260448, PR/201171…). En los partes
  cambia solo al elegir un cliente de otra serie (Refinor → PR). Se confirma al guardar y el aviso dice el número creado.
· Duplicar: el aviso dice el número nuevo y de cuál es copia («Copia creada: PT/260696 (duplicado de PT/260695)»).
  El editor y la ficha enseñan «⧉ Copia de …» con enlace al original. La copia nace sin firmar y sin enviar.
  En albaranes se copia también el nº de pedido del cliente (el camión no).
· Al crear un parte o albarán desde un pedido, el aviso dice su número; al cambiar de serie, el número nuevo y el anterior.
· Envío por correo: al enviar desde el ERP se guarda fecha, hora y a quién (Para y CC).
  Partes y albaranes hechos en el ERP salen «✉ Sin enviar» (en rojo) hasta que se envían.
  Si se mandó desde Outlook o se entregó en mano: en la ficha, «Apuntar envío hecho fuera del ERP» (fecha y a quién).
  También se puede quitar la marca. Todo queda en el seguimiento del cliente y en la auditoría.
· Listados de partes y albaranes: columna «Correo», filtro «Sin enviar / Enviados» y aviso con el número pendiente.
  «Sin enviar» saca los de cualquier año. Lo importado de golden no cuenta (sale «—»).
· Avisos del inicio: «N partes / albaranes sin enviar al cliente». Nuevo indicador del panel «Sin enviar».
· Excel de partes y albaranes: columnas Enviado, Enviado a y Copia de.
```

## v0.21.0 · PARTES MÁS SENCILLOS, SERIES COMO EN GOLDEN

```text
· El parte se rellena por bloques: OFICIALES (categoría, quién, horas, horas extra y sus precios) y PINTURA Y
  MATERIALES (escribe y elige; pone ud y precio). Sin sistemas ni metros.
· Serie PR (Refinor y Refinor Chemicals; se elige en la ficha del cliente, «Serie de los partes»): además, TRABAJOS DEL
  CONTRATO con sus códigos y precios, Contrato nº y Conforme. Se imprime como «DETALLES DE TRABAJOS DE OBRAS».
  Los precios están en Ajustes → Precios de contrato (PR) y se pueden recargar del Excel de Refinor.
· Numeración seguida como en golden: PT/260673, ALB/260447… (con el año delante, empieza de nuevo cada año) y
  PR/201170 (correlativa). Crear o duplicar toma el siguiente. Si cambias el cliente de un parte entre PT y PR, cambia de serie.
· Cliente y obra se buscan escribiendo (las obras del cliente salen primero). «Su pedido», «OT» y «Contrato» proponen
  los que ya usó ese cliente, y se puede escribir otro o dejarlo vacío.
· Fotos o parte escaneado: se suben al guardar el parte (o el albarán); desde el móvil abre la cámara.
· Los PR importados de golden pasan a ser partes (antes entraron como albaranes).
```

## v0.20.0 · PANEL CON FILTRO

```text
Encima del panel: Ver mes / trimestre / año / entre fechas, Obra y Cliente. El filtro se recuerda por usuario.
Widgets nuevos: «Resumen del periodo» (ofertas, pedidos, albaranes, partes, certificaciones, facturas, compras y horas
del filtro; cada casilla abre la lista filtrada), «Ventas y compras por mes» (de los documentos; pulsa un mes para verlo),
«Obras del periodo» (pulsa una obra para filtrar por ella) y «Documentos del periodo» (pestañas).
Los indicadores Servido / Facturado / Compras usan el periodo; las listas de pendientes, la obra y el cliente.
A los paneles ya guardados se les añaden una vez el resumen, el gráfico por meses y los documentos del periodo.
```

## v0.19.0

```text
· CUENTAS Y FIRMAS DE CORREO (Ajustes → Cuentas de correo): enviar desde produccion@… u otro buzón por SMTP (IONOS,
  Microsoft 365, Gmail…), con la firma de Outlook importada tal cual (.htm de %APPDATA%\Microsoft\Signatures + las imágenes
  de su carpeta _files) o escrita a mano con una imagen. Las imágenes van incrustadas. La contraseña se guarda cifrada.
  Al enviar un documento, parte o PPI se elige «Enviar desde»; se recuerda la última de cada usuario. «Enviar prueba».
· EXPORTAR: en cada listado de documentos «⬇ Excel» y «⬇ Excel con líneas» (todo lo filtrado, sin páginas; columnas como
  golden, así que se puede volver a subir en Importar datos). Cualquier otra tabla tiene su «⬇ Excel». En Ajustes,
  «Exportar todo a Excel» (una hoja por lista). Al importar, lo hecho en el ERP nunca se toca (solo lo importado).
· CERTIFICACIONES: los albaranes pendientes salen en su propio bloque con casilla (los traídos de golden se eligen uno a
  uno, como los partes); los del ERP siguen entrando por partidas, y se listan debajo.
· PDF del parte: el cuadro del cliente crece si la dirección es larga (antes se montaba sobre «OT»).
```

## IMPORTAR DOCUMENTOS DE GOLDEN (v0.18.0)

```text
Además de fichas y contabilidad, «Importar datos» reconoce ahora estas listas de golden:
  · Proyectos → obras (código 25.27…, cliente, situación EC/FI, fechas).
  · Pedidos de venta (con la columna Realizado), Albaranes de venta (con Facturado; la serie PT entra como parte de trabajo).
  · Histórico de facturas de venta y de compra (Total Bases, Su número, Código de proyecto, Cuota de retención, Tipo IVA 1).
  · Tesorería → Cobros y Pagos: cada vencimiento se une a su factura por el número; marca la factura como cobrada/pagada.
    Los pendientes sin factura (préstamos, impuestos, facturas que solo están en la contabilidad de golden…) entran
    sueltos en tesorería y en la previsión con su texto. Lo ya cobrado/pagado sin factura no se importa.
Los documentos importados se marcan como «importado»: no generan asientos (ya están en el traspaso de saldos), pero sí
cuentan en libros de IVA, tesorería, obras e informes. Golden no da las líneas en la lista: cada uno lleva una línea resumen
con su base; el detalle sigue en golden. Volver a importar actualiza por número (no duplica) y rehace la cartera.
```

## AÑO DE TRABAJO (v0.17.0)

```text
Arriba, junto a tu nombre, hay un selector de año. Cada año empieza limpio: los listados (ofertas, pedidos, albaranes,
facturas, compras, partes, PPI, historial de stock, informes y contabilidad) solo enseñan lo de ese año.
Lo que quedó PENDIENTE de años anteriores (pedidos abiertos, albaranes sin facturar, facturas sin cobrar…) sigue saliendo
en el año en curso, marcado con su año, para que no se pierda nada en enero.
Para consultar un año anterior se elige en el selector; sale un aviso amarillo con el botón «Volver». El 1 de enero todos
los usuarios pasan solos al año nuevo. La numeración (PV-27-0001…) y los asientos ya empiezan de cero cada año.
El buscador de arriba y las fichas de clientes y obras buscan en todos los años.
Si eliges fechas «desde/hasta» en un listado, manda la fecha que pongas, sea del año que sea.
v0.16.1: arreglada la subida de archivos en «Importar datos» (en el servidor no aparecía la vista previa).
```

## IMPORTAR DATOS DE GOLDEN.NET (v0.16.0)

```text
Ajustes → Importar datos (golden). Se suben los Excel que exporta golden (.xls tal cual; también .xlsx y .csv), varios a la vez.
El ERP reconoce cada archivo por sus columnas y enseña una vista previa: qué es, cuántos son nuevos, cuántos se actualizan,
cuántos se saltan y avisos (sin NIF, saldos que no cuadran…). No cambia nada hasta pulsar «Importar», y antes hace una copia.
  · Formas de pago (código, nº de vencimientos, días).
  · Clientes y proveedores: código, nombre, NIF, dirección, teléfono, email, forma de pago (por su código de golden),
    subcuenta contable de golden, descuento, IBAN y % de retención IRPF (proveedores).
  · Artículos: código, descripción, familia, coste (último precio de entrada o precio medio). Por defecto solo los que
    tienen precio o coste.
  · Plan contable: nombres de todas las cuentas; el ERP se pone a los mismos dígitos que golden (12) si aún no hay apuntes.
    Opcional: asiento de TRASPASO DE SALDOS a la fecha elegida (saldo de cada subcuenta); se puede quitar y rehacer.
  · Obras / proyectos desde las cuentas analíticas de golden (las inactivas, como cerradas).
Volver a importar actualiza por código (no duplica). Los datos de ejemplo no se tocan.
En golden, para traer más columnas: clic derecho en la cabecera de la lista → Añadir una columna; luego hoja verde → Exportar a Excel.
Nuevo también: campo «Retención IRPF (%)» en facturas emitidas y recibidas (se rellena con el % del proveedor) y en la ficha.
```

## ERRORES CLAROS Y COPIABLES (v0.15.3)

```text
Si algo falla, el ERP ya no se queda en blanco: sale una caja roja «Ha ocurrido un error» con un informe listo para copiar
(botón «📋 Copiar informe»): versión, pantalla o acción, usuario, mensaje, archivo y línea, traza y los datos enviados
(sin contraseñas, claves ni nonces). El resto de la app sigue funcionando.
Captura: excepciones y errores fatales de PHP, avisos (warnings) del propio plugin («N problemas técnicos en esta pantalla»)
y fallos de base de datos en consultas del ERP (al guardar avisa en rojo en vez de decir «guardado»).
Ajustes → Registro de errores: todos los fallos, cuántas veces han pasado, copiar uno o todos los pendientes, marcar
resueltos y borrar los resueltos. Si hay pendientes de los últimos 30 días, sale un aviso en el panel del administrador.
```

## PANEL DE CONTABILIDAD (v0.15.2)

```text
Inicio → Personalizar → plantilla «Contabilidad» (solo la ven quienes tienen el permiso Contabilidad). Widgets nuevos,
que también se pueden añadir sueltos a cualquier panel:
  Indicadores: Resultado del año · IVA del trimestre (borrador 303) · Bancos y caja (57) · Clientes por cobrar (43) ·
  Proveedores por pagar (40/41) · Amortizaciones pendientes.
  Bloques: Estado de la contabilidad (periodo bloqueado, amortizaciones, cierre del año anterior, cuadre, terceros sin NIF) ·
  Ingresos y gastos por mes · IVA por trimestres · Margen por obra (contable) · Últimos asientos manuales.
La plantilla trae además Próximos cobros y Próximos pagos.
```

## PARTE PRESTACIÓN DE SERVICIOS (v0.15.1)

```text
El parte de trabajo se imprime y sale en PDF con el formato de golden: título «PARTE PRESTACIÓN DE SERVICIOS», cuadro del
cliente arriba a la derecha, fila Número · Fecha · Su Pedido · OT, cuadro «TRABAJOS REALIZADOS» con «Petición efectuada por»
y líneas Concepto · Cantidad · (HOR, L…) · Precio · Importe, con totales y firmas.
Campos nuevos del parte: OT (orden de trabajo del cliente, aparte de su pedido), Petición efectuada por, Trabajos realizados
(varias líneas) y Zona / lugar. La OT sale en el listado, en la búsqueda, en la línea del parte dentro de la certificación y
en la hoja PARTES del Excel. «Duplicar» y «copiar el último parte de la obra» conservan pedido, OT, petición y trabajos.
```

## CONTABILIDAD (v0.15.0)

```text
Menú «Contabilidad» (permiso nuevo «Contabilidad»; los administradores lo tienen, a los demás hay que darlo en Ajustes → Permisos).
Partida doble con el Plan General Contable de PYMES. Subcuentas de 8 dígitos (configurable antes del primer apunte).

Asientos automáticos (se crean y se rehacen solos al entrar en Contabilidad):
  · Factura emitida:  430+código cliente (total) [+ 473 si nos retienen]  a  705 (base, con la obra)  +  477 (IVA)
  · Factura recibida: 601 (base, con la obra) + 472 (IVA)  a  400+código proveedor (total)  [+ 4751 si retenemos IRPF]
  · Cobro: 572 a 430 · Pago: 400 a 572 (si el medio es «efectivo» o «caja», 570)
  Si cambias o borras la factura, el asiento se rehace o se quita. En periodo cerrado no se toca: sale un aviso.
  Cuentas por cliente/proveedor: en su ficha, «Subcuenta contable» (p. ej. la de golden) y «Cuenta de ingreso/gasto»
  (607 subcontratas, 621 alquileres, 628 suministros…). Una factura concreta se cambia desde su asiento.
Asientos manuales: nóminas (de la gestoría), préstamos, seguros, liquidaciones de IVA, impuesto de sociedades…
  Se pueden duplicar (asientos que se repiten cada mes).

Informes: diario, plan contable con saldos, extracto (subcuenta o cuenta corta: 430 = todos los clientes),
sumas y saldos por nivel, balance de situación y PyG abreviados PYMES comparados con el año anterior. Todo a Excel.
Libros registro de IVA (expedidas/recibidas) y borradores de modelos: 303 / 390, 111 / 190 (retenciones de facturas
recibidas) y 347 (terceros > 3.005,06 € con IVA, por trimestre). Son borradores para copiar en la AEAT: no incluyen lo
que no esté en el ERP (nóminas, alquileres con retención, importaciones, inversión del sujeto pasivo, prorrata).
Analítica por obra: ingresos y gastos contables con obra + material salido del almacén + mano de obra de los partes.
Bienes de inversión: ficha (cuenta 21x, valor, % anual, factura de compra) y «Amortizar hasta…» crea un asiento por mes
(681 a 281x) sin duplicar. La factura del bien va a su cuenta 21x y su IVA sale como bienes de inversión en el 303.
Cierre: regularización (6 y 7 a 129), asiento de cierre y apertura del año siguiente (se pueden deshacer en orden),
renumerar asientos por fecha y «Contabilidad cerrada hasta…» para bloquear periodos ya presentados.
Al borrar los datos de ejemplo se quitan también sus asientos y subcuentas.
Pendiente para próximas versiones: importar el plan y saldos de apertura desde golden, ficheros oficiales de la AEAT
(.txt de 303/347), asientos predefinidos, conciliación bancaria y presupuestos.
```

## CORREOS POR CLIENTE / OBRA Y PLANTILLAS DE CORREO (v0.14.0)

```text
A quién va cada documento:
  · En la ficha del cliente (y en la de cada obra) hay un bloque «✉ A quién se envía cada documento».
  · Cada regla es: obra (o todas) + documento (parte, factura, certificación, albarán, oferta, pedido, PPI o todos) + Para + CC + nota.
    Ejemplo NORTEC: obra ORION · partes → juan@…; obra X · partes → mario@…; todas las obras · facturas → administracion@…
  · Al pulsar «Enviar por correo» el «Para» y la «CC» se rellenan con la regla más concreta:
    obra + documento › obra (todos) › cliente + documento › cliente (todos) › email de la ficha.
    Debajo del «Para» pone de qué regla salen. Siempre se pueden cambiar a mano.
  · Atajo: al enviar, marca «Guardar estos correos para los próximos envíos de … de esta obra / de este cliente»
    y la regla se crea o se actualiza sola (sin duplicar).
Plantillas de correo (Ventas → Plantillas de correo):
  · Nombre, documento, cliente y obra (opcionales), asunto y mensaje.
  · Datos que se rellenan solos: {documento} {el_documento} {numero} {cliente} {obra} {obra_codigo} {de_la_obra}
    {pedido} {fecha} {mes} {importe} {camion} {usuario} {empresa}.
  · Al enviar se elige sola la más concreta (obra › cliente › general) y en el desplegable «Plantilla» se puede cambiar.
  · Si no hay ninguna, se usa el texto de siempre («Por defecto»).
Vale para partes, albaranes, certificaciones, facturas, ofertas, pedidos y PPI. Al actualizar se crean 2 tablas nuevas
(correo_destinos y correo_plantillas), que también entran en las copias de seguridad.
```

## ENTRADA Y SALIDA MANUAL DE STOCK (v0.13.1)

```text
- Almacén → Movimientos de material → «Entrada manual»: stock inicial, material sin albarán, muestras…
  (coste opcional; vacío = el del artículo). «Salida / merma»: caducado, roto, derramado.
  Ninguna de las dos cuenta como coste de obra. El motivo en Notas es obligatorio y queda en el historial.
```

## CERTIFICACIÓN COMO LA PIDE CADA CLIENTE (v0.13)

```text
- En la ficha de la obra, sección «Certificación (como la pide el cliente)»: formato (estándar o
  «Relación valorada de subcontratista», como la de NORTEC), título, proyecto, subcontratista, jefe de
  obra, descripción del pedido, rótulo del presupuesto (p. ej. CONTRATO HASTA 30/04/2025), casilla de
  firma (VºBº NORTEC), nº de la primera certificación (para seguir la numeración del cliente), sacar todas
  las partidas del pedido y mostrar o no el IVA.
- Cada certificación guarda esos datos y se pueden cambiar en ella («✏ Ajustar la certificación»), igual
  que las cantidades y precios del mes si el cliente pide otra cosa. La siguiente parte de lo ajustado.
- Abajo, los recuadros grandes: importe pedido, certificado a origen, certificado anterior e importe a
  certificar.
- Excel tipo NORTEC: hoja de la relación valorada (con las fórmulas SUMIFS por fechas, como la suya) y hoja
  ALBARANES-MEDICIÓN con los albaranes y los partes como líneas PT, con filtros.
- Datos de ejemplo: obra ORION de NORTEC con los 395 albaranes y 119 partes de sep-2025 a nov-2026 sacados
  de su Excel, 14 certificaciones ya facturadas (la nº 14, octubre 2026 = 99.800,20 €) y noviembre pendiente
  para probar la nº 15. Se borra con el resto de datos de ejemplo.
```

## PARTES DE TRABAJO, TARIFAS Y CERTIFICACIONES (v0.12)

```text
- Producción → Partes de trabajo: el parte que firma el cliente (horas de oficial y material, con precio).
  · Desde un pedido: botón «+ Parte de trabajo» (obras pequeñas / trabajos fuera de presupuesto).
  · Desde la obra: «+ Parte de trabajo (copia del último)», con fecha de hoy y sin firma.
  · «✍ Firmado por el cliente» lo marca firmado. Se factura directamente («Facturar») o entra en la
    certificación del mes.
  · Los antiguos partes internos de horas siguen en «Partes de horas (internos)».
- Costes: las horas del parte (partidas tipo MO del catálogo: «Hora oficial 1ª», «Hora extra oficial 1ª»)
  cuentan como coste con el coste/hora del catálogo. Los litros del parte solo se cobran: el coste del
  material es lo que sale de verdad del almacén hacia la obra (Almacén → Dar de baja en obra).
  Pon coste y precio a las partidas MO, y da de alta las pinturas que se cobran en partes como partidas MAT.
- Tarifas (Ventas → Tarifas): el catálogo de venta es la tarifa general. «Subir precios» sube todo un %
  (y guarda antes una copia, p. ej. «Tarifa 2026»). A un cliente que mantiene precios antiguos
  (NORTEC con 2025) se le asigna esa tarifa en su ficha. Las ofertas, pedidos y partes cogen solos los
  precios de la tarifa del cliente.
- Certificaciones: en la obra, «Certificación del mes». Por partida: presupuesto (pedidos), a origen
  anterior, a origen (albaranes hasta fin de mes) y este mes, más los partes que marques como líneas
  extra. Mismo formato que la pestaña CERTIFICACIÓN de la plantilla de obra grande (el TOTAL arriba,
  cabecera granate). El Excel lleva fórmulas y tres hojas como la plantilla: CERTIFICACIÓN (el «a origen»
  suma la hoja MEDICIÓN), MEDICIÓN (una línea por partida de cada albarán, con filtros y totales que
  respetan los filtros) y PARTES (detalle de cada parte, con filtros). Lo incluido queda «Certificado». Se descarga en PDF o Excel,
  se envía por correo y se factura.
  Solo se puede borrar la última certificación de una obra. Los precios van netos (con descuentos).
- Enviar por correo (cualquier documento de venta): manda el PDF (y el Excel en certificaciones) desde la
  web y lo apunta en el seguimiento; o «Abrir en Outlook» con el texto preparado.
  Para que el envío desde la web llegue bien, la web debe enviar correo por SMTP (p. ej. plugin WP Mail SMTP
  con la cuenta de correo de la empresa).
```

## TRABAJADORES POR CATEGORÍA Y APARIENCIA (v0.10)

```text
- Producción → Trabajadores ahora es «Trabajadores y categorías». Lo normal es dar de alta
  categorías (p. ej. «Oficial 1ª» con su coste/hora) y no personas. En el parte del día se pone
  cuántos han trabajado y las horas de cada uno: 3 oficiales × 8 h = 24 h imputadas a la obra.
  DNI y teléfono son opcionales (mejor dejarlos vacíos si no hacen falta).
- Modo claro / oscuro: botón 🌗 de la barra de arriba (automático → claro → oscuro). Es personal.
  Los documentos (ofertas, albaranes, facturas, PPI) se ven siempre en blanco, como en papel,
  y al imprimir salen siempre en claro.
- Colores de la empresa: Ajustes → Apariencia (solo administradores). Color principal y de
  acento; si la web usa Elementor, aparecen sus colores globales para elegirlos con un clic.
  Los demás tonos se calculan solos. Avisa si un color es demasiado claro.
```

## PERMISOS POR ÁREAS (v0.9)

```text
Ajustes → Permisos: tabla de usuarios × áreas (Ventas, Compras, Obras, Partes, Calidad, Almacén, Tesorería,
Seguimiento, Informes, Ajustes). Cada persona solo ve en el menú, el buscador y su panel lo que tenga marcado,
y no puede entrar ni hacer acciones fuera de ello (tampoco abrir adjuntos de otras áreas).
Perfiles de WordPress: Administrador (todo) · Oficina (todo menos Ajustes) · Encargado de taller (Partes) ·
Usuario de gestión (lo que se le marque; p. ej. solo Almacén). Las áreas se pueden cambiar usuario a usuario.
```

## PANEL PERSONALIZABLE Y FOTOS (v0.8)

```text
- Inicio = panel tipo service desk. Cada usuario elige sus widgets: 16 indicadores clicables (cobros vencidos,
  camiones sin PPI, mis tareas…) y 16 bloques (avisos, tareas, obras, calidad, cobros, stock, horas…).
  «Personalizar»: arrastrar para ordenar, tamaño pequeño/mediano/ancho, quitar, añadir. Plantillas:
  Dirección, Oficina/administración, Producción y calidad.
- Tareas asignables: en el seguimiento y en el panel se puede crear una tarea con fecha y asignarla a otra persona.
- Fotos: al subirlas se reducen solas (lado mayor 1.600 px, JPG calidad 75, giradas según el móvil).
  En Ajustes hay un botón para optimizar las fotos grandes que ya estuvieran subidas.
```

## CALIDAD, ADJUNTOS Y ESPECIFICACIÓN (v0.7)

```text
- Producción → Calidad (PPI): un PPI por albarán/camión. Se rellena solo con el sistema de pintura de la obra
  (capas, espesores nominales, perfil requerido) y los lotes de pintura dados de baja para ese albarán u obra.
  Calcula el punto de rocío y comprueba ΔT ≥ 3 °C, HR ≤ 85 %, perfil y espesores (media ≥ nominal, mínimo ≥ 80 %).
  Fotos y documentos, imprimir/PDF y enviar por email al cliente (queda apuntado en el seguimiento).
- Adjuntos en obras, documentos y PPI: PDF de correos, especificaciones, planos, fotos, apuntes…
  Se guardan en wp-content/uploads/pds-adjuntos (carpeta privada). Esa carpeta la copia UpdraftPlus o tu hosting,
  no la copia diaria del ERP.
- Ficha de obra → «Especificación técnica»: especificación del cliente, sistema, RAL, corrosividad, durabilidad,
  normas, requisitos de inspección y otras condiciones. Sale resumida en la obra, sus ofertas, pedidos y albaranes.
```

## ALMACENES Y MATERIAL (v0.6)

```text
- Almacén → Almacenes: varios almacenes (viene «Central» como principal). Stock por almacén y total.
- Almacén → Movimientos de material:
  · Dar de baja en obra: sale del almacén y se carga a la obra; opcionalmente a un albarán concreto (camión).
  · Devolver al almacén: lo que se cargó a una obra y no se gastó vuelve al stock y deja de ser coste de la obra.
  · Reasignar a otra obra: el coste pasa de una obra a otra.
  · Traspaso entre almacenes.
- Los partes también descuentan material, eligiendo el almacén.
- Las compras para una obra entran en el almacén y salen cargadas a la obra (así luego se pueden devolver o reasignar).
- Inventario por almacén.
- En la ficha de cada obra: material cargado y botones para dar de baja, devolver o reasignar.
- En cada albarán de venta: material gastado en ese camión.
```

## ASISTENTE CON IA (v0.5)

```text
Ajustes → Asistente con IA: pega la clave de la API de Anthropic (console.anthropic.com → API Keys), elige modelo
(Haiku 5.5, muy barato, o Sonnet 5.5, más listo), pon un límite de gasto al mes y activa. Sale un botón
«💬 Asistente» abajo a la derecha en todas las pantallas (solo para usuarios de oficina).
Consulta documentos, clientes, obras y márgenes, cobros y pagos, stock, catálogo, horas y ventas.
Puede crear ofertas EN BORRADOR y anotar seguimiento. No emite, borra, cobra ni cambia precios.
Las preguntas y los datos consultados se envían a Anthropic para responder.
```

## DATOS DE EJEMPLO (v0.4)

```text
Ajustes → Datos de ejemplo → Cargar. Crea clientes, proveedores, 2 obras, ofertas (aceptadas, enviada sin respuesta,
rechazada, con IVA 0), pedidos, albaranes por camión, facturas (cobrada, vencida), compras con stock, partes de
2 meses, seguimiento comercial… Todo lleva «(EJEMPLO)» o numeración EJ- y se borra de golpe sin tocar lo real.
```
