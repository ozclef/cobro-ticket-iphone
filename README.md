# cobro-ticket-iphone
Bateria de iphone 6 plus - A1522 - ticket facture, -- 26 - 11 - 25


------



---

# 📄 README.md — Ticket Generator (bio-us / TechnoFix)

Este proyecto es una pequeña herramienta web que permite generar tickets de servicio en formato HTML y descargar un PDF del ticket con un botón.
El formato del ticket es simple, tipo recibo de papel, y puede usarse para servicios técnicos, reparaciones o entregas.

## 🚀 Características

* Formato de ticket minimalista estilo recibo.
* Encabezado con **bio-us / TechnoFix** y “Servicio por Oscar”.
* Campos editables:

  * Nombre del cliente
  * Descripción del servicio
  * Costo
  * Fecha (automática, editable)
* Botón **Descargar PDF** (usa `html2pdf.js`).
* Encabezado principal visible en la página pero oculto al imprimir.
* Todo el CSS está en el mismo archivo para mayor simplicidad.

---

## 📂 Estructura del proyecto

```
/
├── index.html        (Página principal con formulario y ticket)
├── ticket.css        (Estilos incluyendo estilos de impresión)
└── README.md         (Este archivo)
```

> Si prefieres tener todo embebido dentro de `index.html`, puedes eliminar `ticket.css` y pegarlo dentro del `<style>`.

---

## 🛠️ Tecnologías usadas

* **HTML5**
* **CSS3**
* **JavaScript**
* **html2pdf.js** para generar el PDF

---

## 📦 Uso

1. Abre `index.html` en cualquier navegador.
2. Llena los campos del formulario.
3. Haz clic en **“Generar Ticket”**.
4. Aparecerá el ticket listo para imprimir o descargar.
5. Presiona **“Descargar PDF”** si deseas guardarlo.

---

## 🖨️ Notas importantes

* El encabezado superior (parte web) **no se imprime ni aparece en el PDF**.
* Solo se imprime el bloque del ticket con formato estilo recibo.
* Puedes modificar el ancho del ticket en la clase `.ticket`.

---

## 🧩 Personalización

Puedes modificar:

* Logo o nombre de la marca (actualmente: **bio-us / TechnoFix**).
* Información fija como *“Servicio por Oscar”*.
* Paleta de colores.
* Tipografías.
* Tamaño del ticket.

---

## 📜 Licencia

SIN CONCENTIMIENTO PARA QUE NADIE OCUPE ESTOS CODES DE NINGUNA MANERA
no plagios, reconocmimmiento y contrato necesario por Oscar.

---
