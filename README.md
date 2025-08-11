<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>Calculadora de Minerales</title>
  <style>
    /* Reset y base */
    body, html {
      margin: 0;
      padding: 0;
      height: 100vh;
      font-family: Arial, sans-serif;
      background-color: #f9f9f9;
      overflow-x: hidden;
    }

    /* Contenedor principal con layout de 3 columnas */
    .page-container {
      display: flex;
      height: 100vh;
      width: 100%;
    }

    /* Contenedor de anuncios a los lados */
    .ad-side {
      width: 120px; /* ancho del anuncio lateral */
      background-color: #eee;
      padding: 10px;
      box-sizing: border-box;
      display: flex;
      flex-direction: column;
      align-items: center;
      overflow-y: auto;
    }

    /* Contenedor central para el contenido */
    .content {
      flex: 1;
      overflow-y: auto;
      padding: 30px;
      text-align: center;
      box-sizing: border-box;
    }

    /* Estilos para contenido dentro del centro */
    h1 {
      margin-bottom: 20px;
    }

    .section {
      margin: 20px auto;
      max-width: 700px;
      background: #fff;
      padding: 20px;
      border-radius: 12px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }

    label {
      display: block;
      margin-top: 10px;
    }

    input[type="number"] {
      width: 60px;
      margin: 5px;
    }

    button {
      margin-top: 15px;
      padding: 10px 20px;
      font-size: 16px;
      cursor: pointer;
    }

    .result {
      margin-top: 20px;
      font-weight: bold;
    }

    /* Estilo para anuncios simulados */
    .ad-box {
      width: 100px;
      height: 300px;
      background-color: #ccc;
      margin-bottom: 20px;
      border: 2px dashed #999;
      display: flex;
      justify-content: center;
      align-items: center;
      color: #666;
      font-size: 14px;
    }
  </style>
</head>
<body>
  <div class="page-container">
    <!-- Anuncio lado izquierdo -->
    <div class="ad-side" id="ad-left">
      <div class="ad-box">Anuncio Vertical Izquierdo</div>
      <!-- Puedes agregar más anuncios aquí -->
    </div>

    <!-- Contenido principal -->
    <div class="content">
      <h1>Calculadora de Minerales</h1>
      <p style="font-size: 16px; color: #555; margin-top: -10px;">
        Ingrese el nivel actual de su habilidad para calcular los minerales necesarios para maximizarla
      </p>

      <div class="section">
        <h2>Habilidades Comunes</h2>
        <label>¿Cuántas habilidades comunes quieres ingresar?</label>
        <input type="number" id="cantidadComunes" min="0" value="0" />
        <button onclick="generarCampos('comun')">Generar Campos</button>
        <div id="camposComunes"></div>
      </div>

      <div class="section">
        <h2>Habilidades Épicas</h2>
        <label>¿Cuántas habilidades épicas quieres ingresar?</label>
        <input type="number" id="cantidadEpicas" min="0" value="0" />
        <button onclick="generarCampos('epica')">Generar Campos</button>
        <div id="camposEpicas"></div>
      </div>

      <div class="section">
        <button onclick="calcularTotales()">Calcular Totales</button>
        <div class="result" id="resultado"></div>
        <button onclick="generarPDF()" style="display:none;" id="btnDescargar">Descargar Detalle (PDF)</button>
      </div>
    </div>

    <!-- Anuncio lado derecho -->
    <div class="ad-side" id="ad-right">
      <div class="ad-box">Anuncio Vertical Derecho</div>
      <!-- Puedes agregar más anuncios aquí -->
    </div>
  </div>

  <!-- Librerías jsPDF y autotable -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.29/jspdf.plugin.autotable.min.js"></script>

  <script>
    // Aquí va todo tu JS existente (sin cambios)
    let detalleComunes = [];
    let detalleEpicas = [];
    let totalBrillantes = 0;
    let totalRadiantes = 0;
    let totalEstelares = 0;

    function generarCampos(tipo) {
      const cantidad = parseInt(document.getElementById("cantidad" + (tipo === 'comun' ? 'Comunes' : 'Epicas')).value);
      const contenedor = document.getElementById("campos" + (tipo === 'comun' ? 'Comunes' : 'Epicas'));
      contenedor.innerHTML = "";
      for (let i = 0; i < cantidad; i++) {
        const label = document.createElement("label");
        label.innerText = `Nivel de habilidad ${i + 1}:`;
        const input = document.createElement("input");
        input.type = "number";
        input.min = "1";
        input.max = tipo === "comun" ? "18" : "27";
        input.required = true;
        input.className = tipo;
        contenedor.appendChild(label);
        contenedor.appendChild(input);
      }
    }

    function calcularTotales() {
      detalleComunes = [];
      detalleEpicas = [];
      totalBrillantes = 0;
      totalRadiantes = 0;
      totalEstelares = 0;

      // Habilidades comunes
      const comunes = document.querySelectorAll(".comun");
      comunes.forEach((input, i) => {
        const nivel = parseInt(input.value);
        if (isNaN(nivel) || nivel < 1 || nivel > 18) return;

        let division = Math.floor(nivel / 3);
        let cantidadR = 0, cantidadB = 0;

        if (nivel <= 2) cantidadR = 3720;
        else if (nivel <= 5) cantidadR = 3700;
        else if (nivel <= 8) cantidadR = 3600;
        else if (nivel <= 11) cantidadR = 3400;
        else cantidadR = 600 * (9 - division);

        let h = nivel - 1;
        if (h <= 8) {
          let sum_n = h * (h + 1) / 2;
          let sum_n2 = h * (h + 1) * (2 * h + 1) / 6;
          cantidadB = 56060 - (20 * sum_n2 + 60 * sum_n + 40 * h);
        } else {
          let sum_part1 = 6560;
          let sum_n_from_9 = (h - 8) * (h + 9) / 2;
          cantidadB = 56060 - (sum_part1 + 100 * sum_n_from_9 + 1000 * (h - 8));
        }

        cantidadR -= 1800;
        cantidadB -= 28800;

        totalRadiantes += cantidadR;
        totalBrillantes += cantidadB;

        detalleComunes.push({
          indice: i + 1,
          nivel: nivel,
          brillantes: Math.floor(cantidadB),
          radiantes: Math.floor(cantidadR)
        });
      });

      // Habilidades épicas
      const epicas = document.querySelectorAll(".epica");
      epicas.forEach((input, i) => {
        const nivel = parseInt(input.value);
        if (isNaN(nivel) || nivel < 1 || nivel > 27) return;

        let division = Math.floor(nivel / 3);
        let cantidadE = 0, cantidadR = 0, cantidadB = 0;

        if (nivel <= 8) cantidadE = 480;
        else if (nivel <= 11) cantidadE = 470;
        else if (nivel <= 14) cantidadE = 450;
        else if (nivel <= 17) cantidadE = 420;
        else if (nivel <= 20) cantidadE = 370;
        else if (nivel <= 23) cantidadE = 270;
        else if (nivel <= 26) cantidadE = 150;
        else cantidadE = 0;

        if (nivel <= 2) cantidadR = 3720;
        else if (nivel <= 5) cantidadR = 3700;
        else if (nivel <= 8) cantidadR = 3600;
        else if (nivel <= 11) cantidadR = 3400;
        else cantidadR = 600 * (9 - division);

        let h = nivel - 1;
        if (h <= 8) {
          let sum_n = h * (h + 1) / 2;
          let sum_n2 = h * (h + 1) * (2 * h + 1) / 6;
          cantidadB = 56060 - (20 * sum_n2 + 60 * sum_n + 40 * h);
        } else {
          let sum_part1 = 6560;
          let sum_n_from_9 = (h - 8) * (h + 9) / 2;
          cantidadB = 56060 - (sum_part1 + 100 * sum_n_from_9 + 1000 * (h - 8));
        }

        totalRadiantes += cantidadR;
        totalBrillantes += cantidadB;
        totalEstelares += cantidadE;

        detalleEpicas.push({
          indice: i + 1,
          nivel: nivel,
          brillantes: Math.floor(cantidadB),
          radiantes: Math.floor(cantidadR),
          estelares: Math.floor(cantidadE)
        });
      });

      document.getElementById("resultado").innerHTML =
        `Total:<br>
        <strong>${Math.floor(totalBrillantes)}</strong> minerales brillantes<br>
        <strong>${Math.floor(totalRadiantes)}</strong> minerales radiantes<br>
        <strong>${Math.floor(totalEstelares)}</strong> minerales estelares`;

      document.getElementById("btnDescargar").style.display = "inline-block";
    }

    async function generarPDF() {
      const { jsPDF } = window.jspdf;
      const doc = new jsPDF();

      doc.setFontSize(18);
      doc.text("Detalle de Minerales por Habilidad", 14, 20);

      let y = 30;

      if (detalleComunes.length > 0) {
        doc.setFontSize(14);
        doc.text("Habilidades Comunes", 14, y);
        y += 6;

        doc.autoTable({
          startY: y,
          head: [["#", "Nivel", "Brillantes", "Radiantes"]],
          body: detalleComunes.map(h => [h.indice, h.nivel, h.brillantes, h.radiantes]),
          theme: "grid",
          headStyles: { fillColor: [100, 149, 237] },
          margin: { left: 14, right: 14 }
        });
        y = doc.lastAutoTable.finalY + 10;
      }

      if (detalleEpicas.length > 0) {
        doc.setFontSize(14);
        doc.text("Habilidades Épicas", 14, y);
        y += 6;

        doc.autoTable({
          startY: y,
          head: [["#", "Nivel", "Brillantes", "Radiantes", "Estelares"]],
          body: detalleEpicas.map(h => [h.indice, h.nivel, h.brillantes, h.radiantes, h.estelares]),
          theme: "grid",
          headStyles: { fillColor: [205, 94, 235] },
          margin: { left: 14, right: 14 }
        });
        y = doc.lastAutoTable.finalY + 10;
      }

      doc.setFontSize(16);
      doc.text("Totales Generales:", 14, y);
      y += 7;
      doc.setFontSize(14);
      doc.text(`Minerales Brillantes: ${Math.floor(totalBrillantes)}`, 14, y);
      y += 7;
      doc.text(`Minerales Radiantes: ${Math.floor(totalRadiantes)}`, 14, y);
      y += 7;
      doc.text(`Minerales Estelares: ${Math.floor(totalEstelares)}`, 14, y);

      doc.save("detalle_minerales.pdf");
    }
  </script>
</body>
</html>
