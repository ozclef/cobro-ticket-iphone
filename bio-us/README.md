Sí — tranqui: no necesitás Python para usar lo que ya te di. Te explico todo claro y directo, y te dejo código listo para copiar/pegar según lo que quieras (A4 o ticket térmico 80mm, QR folio, logo, políticas, y script `git push` con tu usuario para que lo subas cuando quieras).

### Resumen rápido

* El `receipt.html` funciona **abrirlo en el navegador** (doble clic). No hace falta Python.
* Si preferís servirlo localmente (útil para pruebas o para que `fetch()` funcione), podés usar Python así:

  * `python -m http.server 8000` en la carpeta y abrir `http://localhost:8000/receipt.html`.
* Abajo te dejo:

  1. HTML compacto para **ticket 80mm** (ideal para impresora térmica — letra pequeña, ocupa poco).
  2. HTML para **A4** (más presentación).
  3. Cómo generar un **folio + QR** (usa Google Charts QR si estás online; opción local si estás offline).
  4. Texto corto y listo para **política de garantía** (versión breve para ticket).
  5. `push-script.sh` listo para que reemplaces `TU_USUARIO` y `NOMBRE_REPO`.

---

## 1) Ticket térmico 80mm — código listo

Copia esto a `receipt-80mm.html` y ábrelo con doble clic. Si tenés conexión, el QR se genera usando Google Charts. Si estás offline, el ticket sigue funcionando sin QR.

```html
<!doctype html>
<html lang="es">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=280"/>
<title>Ticket 80mm</title>
<style>
  body{font-family:monospace; width:280px; margin:0; padding:8px; background:#fff; color:#000;}
  .center{text-align:center;}
  .tiny{font-size:11px;}
  .small{font-size:12px;}
  .bold{font-weight:700;}
  .sep{border-top:1px dashed #000; margin:6px 0;}
  .right{text-align:right;}
  #qr{margin:6px auto; width:120px; height:120px;}
  @media print{
    body{width:80mm;}
    .no-print{display:none;}
  }
</style>
</head>
<body>
  <div class="center bold">Taller OS - Reparaciones</div>
  <div class="center tiny">Ciudad - RFC/ID opcional</div>
  <div class="sep"></div>

  <div><strong>Cliente:</strong> <span id="cliente">--------------</span></div>
  <div><strong>Servicio:</strong> <span id="servicio">Reparación</span></div>
  <div class="right"><strong>$</strong> <span id="precio">0.00</span> MXN</div>
  <div class="tiny">Fecha: <span id="fecha"></span></div>
  <div class="sep"></div>

  <div id="qrarea" class="center"></div>

  <div class="tiny center">Política: 30 días garantía por fallas en servicio. No cubre golpes ni agua.</div>
  <div class="tiny center">Contacto: 55-1234-5678</div>

  <div style="margin-top:8px;" class="center no-print">
    <label>Cliente <input id="c" /></label>
    <label>Artículo <input id="it" /></label>
    <label>Precio <input id="p" value="250" /></label>
    <button id="g">Generar</button>
    <button onclick="window.print()">Imprimir</button>
  </div>

<script>
function pad(n){return n<10?'0'+n:n;}
document.getElementById('g').addEventListener('click',()=>{
  const cliente = document.getElementById('c').value || 'Cliente';
  const item = document.getElementById('it').value || 'Servicio';
  const precio = parseFloat(document.getElementById('p').value || 0).toFixed(2);
  const now = new Date();
  const fecha = now.getFullYear() + '-' + pad(now.getMonth()+1) + '-' + pad(now.getDate()) + ' ' + pad(now.getHours()) + ':' + pad(now.getMinutes());
  const folio = 'OS' + now.getFullYear().toString().slice(-2) + (Math.random().toString(36).slice(2,8)).toUpperCase();

  document.getElementById('cliente').textContent = cliente;
  document.getElementById('servicio').textContent = item;
  document.getElementById('precio').textContent = precio;
  document.getElementById('fecha').textContent = fecha;

  // QR con Google Charts (funciona si estás online). Contiene texto simple: FOLIO|TOTAL|FECHA
  const qrtext = encodeURIComponent('FOLIO:' + folio + '|TOTAL:' + precio + '|FECHA:' + fecha);
  const qrimg = '<img alt="QR" src="https://chart.googleapis.com/chart?cht=qr&chs=200x200&chl=' + qrtext + '" style="width:120px;height:120px;"/>';
  document.getElementById('qrarea').innerHTML = qrimg + '<div class="tiny">Folio: ' + folio + '</div>';
});
</script>
</body>
</html>
```

---

## 2) Ticket A4 (más presentable)

Para recibos más grandes o guardar en PDF. Guardá como `receipt-a4.html`.

```html
<!doctype html>
<html lang="es">
<head><meta charset="utf-8"/><title>Recibo A4</title>
<style>
  body{font-family:Inter,Arial; padding:20px; max-width:800px; margin:auto;}
  .header{display:flex; justify-content:space-between; align-items:center;}
  .company{font-size:20px; font-weight:700;}
  .meta{font-size:13px; color:#555;}
  table{width:100%; margin-top:14px; border-collapse:collapse;}
  th,td{padding:8px; border-bottom:1px solid #eee; text-align:left;}
  .total{font-size:18px; font-weight:700; text-align:right;}
  .small{font-size:12px; color:#666;}
  .qr{float:right;}
  @media print{ .no-print{display:none} }
</style>
</head>
<body>
  <div class="header">
    <div>
      <div class="company" id="comp">Taller OS</div>
      <div class="meta" id="city">Ciudad</div>
    </div>
    <div class="qr" id="qrarea"></div>
  </div>

  <div style="margin-top:12px;">
    <div><strong>Cliente:</strong> <span id="cust">---</span></div>
    <div class="small">Fecha: <span id="dt">---</span></div>
  </div>

  <table>
    <thead><tr><th>Artículo</th><th>Cantidad</th><th>Precio</th><th>Subtotal</th></tr></thead>
    <tbody id="lines"><tr><td>Reparación</td><td>1</td><td>$250.00</td><td>$250.00</td></tr></tbody>
  </table>

  <div class="total" id="grand">$250.00 MXN</div>

  <div style="margin-top:18px;" class="small">
    <strong>Política de garantía:</strong> 30 días por defectos del servicio. No aplica por golpes, humedad o desarmado por terceros.
  </div>

  <div class="no-print" style="margin-top:14px;">
    <input id="c" placeholder="Cliente"/> <input id="i" placeholder="Artículo"/>
    <input id="pr" placeholder="Precio" value="250"/>
    <button id="gen">Generar</button>
    <button onclick="window.print()">Imprimir / Guardar PDF</button>
  </div>

<script>
function pad(n){return n<10?'0'+n:n;}
document.getElementById('gen').addEventListener('click',()=>{
  const cliente=document.getElementById('c').value||'Cliente';
  const item=document.getElementById('i').value||'Servicio';
  const price=parseFloat(document.getElementById('pr').value||0).toFixed(2);
  const now=new Date();
  const dateStr=now.getFullYear()+'-'+pad(now.getMonth()+1)+'-'+pad(now.getDate())+' '+pad(now.getHours())+':'+pad(now.getMinutes());
  const folio='OS'+Date.now().toString().slice(-6);
  document.getElementById('cust').textContent=cliente;
  document.getElementById('dt').textContent=dateStr;
  document.getElementById('lines').innerHTML = '<tr><td>'+item+'</td><td>1</td><td>$'+price+'</td><td>$'+price+'</td></tr>';
  document.getElementById('grand').textContent = '$' + price + ' MXN';
  const qrtext=encodeURIComponent('FOLIO:'+folio+'|TOTAL:'+price+'|FECHA:'+dateStr);
  document.getElementById('qrarea').innerHTML = '<img alt="qr" src="https://chart.googleapis.com/chart?cht=qr&chs=150x150&chl='+qrtext+'"/><div class="small">Folio: '+folio+'</div>';
});
</script>
</body>
</html>
```

---

## 3) Folio + QR — cómo funciona y alternativas

* En los ejemplos arriba el folio se genera al crear el ticket (una cadena corta como `OS241A3F` o usando `Date.now()`).
* El QR en los ejemplos usa **Google Charts**: `https://chart.googleapis.com/chart?cht=qr&chs=150x150&chl=...` — funciona si tenés internet. Codifica `FOLIO|TOTAL|FECHA`.
* Si necesitás QR **offline**, dime y te pego `qrcode.min.js` embebido (lo puedo insertar en el archivo; ocupa más líneas pero funciona sin internet).

---

## 4) Política de garantía — versión corta (ideal para ticket térmico)

Pega esto en el `receipt` en el área de notas:

> Garantía: 30 días contra defectos del servicio (no cubre: golpes, líquidos, apertura por terceros). Cambio o reparación según diagnóstico. Conserva este ticket como comprobante.

Si querés, te doy 2 versiones: legal/extendida (para A4) y ultra-corta (para 80mm).

---

## 5) Script `push` listo — reemplazá tu usuario

Guarda como `push-script.sh` y reemplaza `TU_USUARIO` y `NOMBRE_REPO`:

```bash
#!/bin/bash
# Uso: ./push-script.sh TU_USUARIO NOMBRE_REPO
USER="$1"
REPO="$2"
if [ -z "$USER" ] || [ -z "$REPO" ]; then
  echo "Uso: ./push-script.sh TU_USUARIO NOMBRE_REPO"
  exit 1
fi
URL="git@github.com:${USER}/${REPO}.git"
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin "$URL"
git push -u origin main
echo "Repo subido: $URL"
```

Si querés que lo deje con tu usuario ya puesto, decime tu nombre de usuario y nombre del repo y te doy el script con eso listo.

---

## Qué podés hacer ahora (acciones concretas)

1. Copiá el archivo `receipt-80mm.html` y probalo. Si imprimís a la impresora térmica, en el diálogo de impresión pon ancho 80mm o el tamaño de tu rollo.
2. Usá el botón **Generar** y luego **Imprimir** → elegí impresora o **Guardar como PDF**.
3. Si querés QR offline y que lo incluya todo en un solo archivo, te lo dejo ya embebido (te lo pego en el próximo mensaje sin preguntar).
4. Si querés que ya arme un ZIP con estas versiones y con tu `push-script` con tu usuario, pasame tu `username` y `nombre_repo` y te lo genero (o si preferís, lo hacés después).

---

Decime qué querés ahora sin vueltas:

* ¿Lo quiero con **QR offline embebido**? (te lo pego ya)
* ¿Te armo el ZIP con A4 + 80mm + `push-script` con tu usuario? (dame usuario y repo)
* ¿O querés que personalice logo y texto fiscal (RFC, dirección) en los tickets? Pegá aquí los datos que deben aparecer y lo hago.
