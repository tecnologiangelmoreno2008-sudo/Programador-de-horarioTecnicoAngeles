Horario_Escolar


<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Gestión de Horario Escolar Semanal</title>
  
  <!-- Librerías para generación de PDF -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.25/jspdf.plugin.autotable.min.js"></script>

  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 15px;
      background-color: #f4f6f9;
      user-select: none;
    }
    
    .encabezado-institucional {
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 15px;
      margin-bottom: 10px;
    }

    .logo-img {
      height: 70px;
      width: auto;
      object-fit: contain;
    }

    .titulo-contenedor {
      text-align: center;
    }

    h2 { margin: 0; color: #1a202c; font-size: 20px; }
    .subtitulo { color: #4a5568; margin-top: 5px; font-weight: bold; font-size: 14px; }
    
    .panel-control {
      background: #fff;
      padding: 12px;
      border-radius: 8px;
      margin-bottom: 8px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
      display: flex;
      gap: 8px;
      flex-wrap: wrap;
      align-items: center;
      position: relative;
      z-index: 100;
    }

    /* PALETA ESTILO EXCEL */
    .dropdown-color-container {
      position: relative;
      display: inline-block;
    }

    .btn-fill-excel {
      display: flex;
      align-items: center;
      gap: 3px;
      background: #f8f9fa;
      border: 1px solid #ced4da;
      padding: 4px 6px;
      border-radius: 4px;
      cursor: pointer;
    }
    .btn-fill-excel:hover { background: #e2e6ea; }

    .icon-bucket { font-size: 14px; }

    .color-indicator-bar {
      width: 16px;
      height: 3px;
      background-color: #ffff00;
      margin-top: 1px;
    }

    .arrow-down { font-size: 9px; color: #555; }

    .palette-dropdown {
      display: none;
      position: absolute;
      top: 105%;
      left: 0;
      background-color: #ffffff;
      border: 1px solid #b0b0b0;
      box-shadow: 0 4px 10px rgba(0,0,0,0.15);
      padding: 10px;
      z-index: 1000;
      width: 220px;
      border-radius: 2px;
    }

    .palette-dropdown.show { display: block; }

    .palette-section-title {
      font-size: 11px;
      font-weight: bold;
      color: #333;
      margin-bottom: 5px;
      margin-top: 5px;
    }

    .theme-colors-grid {
      display: grid;
      grid-template-columns: repeat(10, 1fr);
      gap: 2px;
    }

    .swatch {
      width: 18px;
      height: 18px;
      border: 1px solid rgba(0,0,0,0.1);
      cursor: pointer;
      box-sizing: border-box;
    }

    .swatch:hover { outline: 2px solid #333; z-index: 2; }

    .menu-item-btn {
      display: flex;
      align-items: center;
      gap: 8px;
      width: 100%;
      padding: 5px 2px;
      background: none;
      border: none;
      text-align: left;
      font-size: 11px;
      cursor: pointer;
      color: #222;
      margin-top: 4px;
    }

    .menu-item-btn:hover { background-color: #e8f0fe; }

    .no-color-box {
      width: 16px;
      height: 16px;
      border: 1px solid #ccc;
      background: #fff;
    }

    input, select, button {
      padding: 6px 8px;
      font-size: 12px;
      border: 1px solid #ccc;
      border-radius: 4px;
    }

    select#selectDiaVista {
      background-color: #2a3b5c;
      color: white;
      font-weight: bold;
    }

    .btn-guardar { background-color: #28a745; color: white; border: none; cursor: pointer; font-weight: bold; }
    .btn-guardar:hover { background-color: #218838; }

    .btn-director { background-color: #17a2b8; color: white; border: none; cursor: pointer; font-weight: bold; }
    .btn-director:hover { background-color: #138496; }

    .btn-grupo-add { background-color: #6f42c1; color: white; border: none; cursor: pointer; font-weight: bold; }
    .btn-grupo-add:hover { background-color: #5a32a3; }

    .btn-grupo-del { background-color: #e83e8c; color: white; border: none; cursor: pointer; font-weight: bold; }
    .btn-grupo-del:hover { background-color: #c2185b; }

    .btn-eliminar-dir { background-color: #fd7e14; color: white; border: none; cursor: pointer; font-weight: bold; }
    .btn-eliminar-dir:hover { background-color: #e06d10; }

    .btn-eliminar { background-color: #dc3545; color: white; border: none; cursor: pointer; font-weight: bold; }
    .btn-eliminar:hover { background-color: #c82333; }

    .btn-limpiar { background-color: #6c757d; color: white; border: none; cursor: pointer; font-weight: bold; }
    .btn-limpiar:hover { background-color: #5a6268; }

    .btn-pdf { background-color: #0275d8; color: white; border: none; cursor: pointer; font-weight: bold; }
    .btn-pdf:hover { background-color: #025aa5; }

    .contenedor-tabla {
      overflow-x: auto;
      background: #fff;
      border-radius: 8px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    }

    table {
      width: 100%;
      border-collapse: collapse;
      text-align: center;
      font-size: 11px;
    }

    th, td {
      border: 1px solid #808080;
      padding: 4px;
      min-width: 75px;
      height: 36px;
      position: relative;
      cursor: pointer;
    }

    /* MARCADOR DE SELECCIÓN PARA CUALQUIER CELDA DE LA TABLA */
    .celda-seleccionada {
      box-shadow: inset 0 0 0 2.5px #0275d8 !important;
      outline: 2px solid #0275d8 !important;
      z-index: 10;
    }

    /* ESTILOS BASE */
    .fila-director th {
      background-color: #e2e8f0;
      color: #1a202c;
      font-weight: bold;
      font-size: 9px;
    }

    .fila-grado th {
      background: #c2d1e0;
      color: #000000;
      font-weight: bold;
      font-size: 11px;
    }

    .hora-col { 
      background-color: #d9e1f2; 
      font-weight: bold; 
      color: #d9534f;
      width: 35px; 
      font-size: 12px;
    }

    .celda-materia { font-weight: bold; display: block; color: #000000; text-transform: uppercase; font-size: 10px; }
    .celda-docente { font-size: 8.5px; color: #4a5568; text-transform: uppercase; display: block; }
  </style>
</head>
<body>

  <!-- Header -->
  <div class="encabezado-institucional">
    <img id="imgLogo" class="logo-img" style="display:none;" alt="Logo Institución">
    <div class="titulo-contenedor">
      <h2>I. E. JOSÉ ANTONIO AGUILERA - SAN PEDRO VALLE</h2>
      <div class="subtitulo" id="tituloDiaActual">HORARIO DE: LUNES</div>
    </div>
  </div>

  <!-- Panel de control -->
  <div class="panel-control">
    <label><b>Logo:</b></label>
    <input type="file" id="inputLogo" accept="image/*" onchange="cargarLogo(event)" style="width: 140px;">

    <span style="border-left: 2px solid #ccc; height: 20px; margin: 0 2px;"></span>

    <!-- BOTÓN DESPLEGABLE DE COLORES TIPO EXCEL -->
    <div class="dropdown-color-container">
      <div class="btn-fill-excel" id="btnDropdownTrigger" onclick="toggleColorMenu(event)" title="Color de relleno">
        <div style="display:flex; flex-direction:column; align-items:center;">
          <span class="icon-bucket">🪟</span>
          <div class="color-indicator-bar" id="indicatorBar"></div>
        </div>
        <span class="arrow-down">▼</span>
      </div>

      <div class="palette-dropdown" id="colorPaletteDropdown" onclick="event.stopPropagation()">
        <div class="palette-section-title">Colores del tema</div>
        <div class="theme-colors-grid" id="gridThemeColors"></div>

        <div class="palette-section-title">Colores estándar</div>
        <div class="theme-colors-grid" id="gridStandardColors"></div>

        <hr style="border:0; border-top:1px solid #e0e0e0; margin: 6px 0;">

        <button class="menu-item-btn" onclick="aplicarColor(''); closeColorMenu();">
          <div class="no-color-box"></div>
          Restablecer / Sin color
        </button>

        <button class="menu-item-btn" onclick="document.getElementById('hiddenColorInput').click();">
          <span style="font-size: 14px;">🎨</span>
          Más colores...
        </button>
        <input type="color" id="hiddenColorInput" style="display:none;" onchange="aplicarColor(this.value); closeColorMenu();">
      </div>
    </div>

    <span id="textoSeleccionActual" style="font-size: 11px; color: #d9534f; font-weight: bold; margin-left: 5px;">👈 Toca celdas para colorear</span>

    <span style="border-left: 2px solid #ccc; height: 20px; margin: 0 2px;"></span>

    <label><b>Día:</b></label>
    <select id="selectDiaVista" onchange="cambiarDiaVista()">
      <option value="LUNES">LUNES</option>
      <option value="MARTES">MARTES</option>
      <option value="MIÉRCOLES">MIÉRCOLES</option>
      <option value="JUEVES">JUEVES</option>
      <option value="VIERNES">VIERNES</option>
    </select>

    <!-- Gestión de Grupos -->
    <input type="text" id="inputNuevoGrupo" placeholder="Nuevo Grupo" style="width: 80px;">
    <button class="btn-grupo-add" onclick="agregarGrupo()">+ Gr</button>
    <button class="btn-grupo-del" onclick="eliminarGrupo()">- Gr</button>

    <span style="border-left: 2px solid #ccc; height: 20px; margin: 0 2px;"></span>

    <label><b>Grado:</b></label>
    <select id="selectGrado" onchange="cargarDirectorEnInput()"></select>

    <input type="text" id="inputDirector" placeholder="Director de Grado" style="width: 100px;">
    <button class="btn-director" onclick="guardarDirector()">Dir.</button>
    <button class="btn-eliminar-dir" onclick="eliminarDirector()">Borrar Dir.</button>

    <span style="border-left: 2px solid #ccc; height: 20px; margin: 0 2px;"></span>

    <label><b>Hora:</b></label>
    <select id="selectHora">
      <option value="1">1</option>
      <option value="2">2</option>
      <option value="3">3</option>
      <option value="4">4</option>
      <option value="5">5</option>
      <option value="6">6</option>
      <option value="7">7</option>
    </select>

    <input type="text" id="inputMateria" placeholder="Materia" style="width: 90px;">
    <input type="text" id="inputDocente" placeholder="Docente" style="width: 90px;">

    <button class="btn-guardar" onclick="asignarClase()">Guardar Clase</button>
    <button class="btn-eliminar" onclick="eliminarClase()">Eliminar</button>
    <button class="btn-limpiar" onclick="limpiarFormulario()">Limpiar</button>
    <button class="btn-pdf" onclick="descargarPDFSemanal()">📄 PDF Semanal</button>
  </div>

  <!-- Tabla de Horario Completa -->
  <div class="contenedor-tabla">
    <table id="tablaHorario">
      <thead>
        <tr id="filaDirectores" class="fila-director"></tr>
        <tr id="encabezadoTabla" class="fila-grado"></tr>
      </thead>
      <tbody></tbody>
    </table>
  </div>

  <script>
    const gradosPredeterminados = [
      "8-1", "8-2", "8-3", "8-4",
      "9-1", "9-2", "9-3",
      "10-1", "10-2", "10-3",
      "11-1", "11-2", "11-3"
    ];

    let grados = JSON.parse(localStorage.getItem("horario_grados_lista")) || gradosPredeterminados;
    const horas = [1, 2, 3, 4, 5, 6, 7];
    const diasSemana = ["LUNES", "MARTES", "MIÉRCOLES", "JUEVES", "VIERNES"];
    
    let asignaciones = JSON.parse(localStorage.getItem("horario_escolar_datos")) || [];
    let directores = JSON.parse(localStorage.getItem("horario_directores_datos")) || {};
    let coloresCasillas = JSON.parse(localStorage.getItem("horario_colores_datos")) || {};
    let logoBase64 = localStorage.getItem("horario_logo_base64") || "";

    let celdasSeleccionadas = new Set();
    let estaArrastrando = false;

    // PALETA EXCEL
    const baseThemeColors = ["#ffffff", "#000000", "#e7e6e6", "#44546a", "#5b9bd5", "#ed7d31", "#a5a5a5", "#ffc000", "#4472c4", "#70ad47"];
    const themeTints = [
      ["#f2f2f2", "#7f7f7f", "#d0cece", "#d9e1f2", "#ddebf7", "#fce4d6", "#ededed", "#fff2cc", "#dedede", "#e2efda"],
      ["#d9d9d9", "#595959", "#aeabab", "#b4c6e7", "#bdd7ee", "#f8cbad", "#dbdbdb", "#ffe699", "#c6d9f1", "#c6e0b4"],
      ["#bfbfbf", "#3f3f3f", "#757171", "#8ea9db", "#9cc2e5", "#f4b084", "#c9c9c9", "#ffd966", "#8db4e2", "#a9d08e"],
      ["#a6a6a6", "#262626", "#3a3838", "#305496", "#2f5597", "#c65911", "#7b7b7b", "#bf8f00", "#548235", "#548235"],
      ["#7f7f7f", "#0c0c0c", "#171616", "#203764", "#1f3864", "#833c0c", "#525252", "#806000", "#375623", "#375623"]
    ];
    const standardColors = ["#c00000", "#ff0000", "#ffc000", "#ffff00", "#92d050", "#00b050", "#00b0f0", "#0070c0", "#002060", "#7030a0"];

    function generarPaletaExcel() {
      const gridTheme = document.getElementById("gridThemeColors");
      gridTheme.innerHTML = "";
      baseThemeColors.forEach(color => {
        const div = document.createElement("div");
        div.className = "swatch";
        div.style.backgroundColor = color;
        div.onclick = () => { aplicarColor(color); closeColorMenu(); };
        gridTheme.appendChild(div);
      });

      themeTints.forEach(row => {
        row.forEach(color => {
          const div = document.createElement("div");
          div.className = "swatch";
          div.style.backgroundColor = color;
          div.onclick = () => { aplicarColor(color); closeColorMenu(); };
          gridTheme.appendChild(div);
        });
      });

      const gridStandard = document.getElementById("gridStandardColors");
      gridStandard.innerHTML = "";
      standardColors.forEach(color => {
        const div = document.createElement("div");
        div.className = "swatch";
        div.style.backgroundColor = color;
        div.onclick = () => { aplicarColor(color); closeColorMenu(); };
        gridStandard.appendChild(div);
      });
    }

    function toggleColorMenu(e) {
      e.stopPropagation();
      document.getElementById("colorPaletteDropdown").classList.toggle("show");
    }

    function closeColorMenu() {
      document.getElementById("colorPaletteDropdown").classList.remove("show");
    }

    document.addEventListener('click', () => { closeColorMenu(); });

    function ordenarGrados() {
      grados.sort((a, b) => a.localeCompare(b, undefined, { numeric: true, sensitivity: 'base' }));
    }

    function inicializarEstructura() {
      ordenarGrados();
      generarPaletaExcel();

      if (logoBase64) {
        const img = document.getElementById("imgLogo");
        img.src = logoBase64;
        img.style.display = "block";
      }

      actualizarEncabezadosYSeparador();
      construirTabla();
      cargarHorarioActual();
      cargarDirectorEnInput();

      document.addEventListener('mouseup', () => { estaArrastrando = false; });
    }

    function registrarEventosCelda(elemento, idCelda) {
      elemento.onmousedown = (e) => {
        estaArrastrando = true;
        gestionarSeleccionCelda(idCelda, e.ctrlKey || e.metaKey, false);
      };

      elemento.onmouseenter = () => {
        if (estaArrastrando) {
          gestionarSeleccionCelda(idCelda, true, true);
        }
      };
    }

    function actualizarEncabezadosYSeparador() {
      const selectGrado = document.getElementById("selectGrado");
      const filaDir = document.getElementById("filaDirectores");
      const encabezado = document.getElementById("encabezadoTabla");

      selectGrado.innerHTML = "";
      filaDir.innerHTML = "";
      encabezado.innerHTML = "";

      // Celda superior izquierda DIR. GRADO
      const thLabelDir = document.createElement("th");
      thLabelDir.textContent = "DIR. GRADO";
      thLabelDir.id = "head-label-dir";
      registrarEventosCelda(thLabelDir, "head-label-dir");
      filaDir.appendChild(thLabelDir);

      // Celda superior izquierda GRADO
      const thLabelGrado = document.createElement("th");
      thLabelGrado.textContent = "GRADO";
      thLabelGrado.className = "hora-col";
      thLabelGrado.id = "head-label-grado";
      registrarEventosCelda(thLabelGrado, "head-label-grado");
      encabezado.appendChild(thLabelGrado);

      grados.forEach(g => {
        const option = document.createElement("option");
        option.value = g;
        option.textContent = g;
        selectGrado.appendChild(option);

        // Celdas de Directores
        const thDir = document.createElement("th");
        const idDir = `director-${g}`;
        thDir.id = idDir;
        thDir.textContent = directores[g] || "---";
        registrarEventosCelda(thDir, idDir);
        filaDir.appendChild(thDir);

        // Celdas de Grados (Encabezado)
        const thGrad = document.createElement("th");
        const idGrad = `grado-header-${g}`;
        thGrad.id = idGrad;
        thGrad.textContent = g;
        registrarEventosCelda(thGrad, idGrad);
        encabezado.appendChild(thGrad);
      });
    }

    function construirTabla() {
      const tbody = document.querySelector("#tablaHorario tbody");
      tbody.innerHTML = "";

      horas.forEach(hora => {
        const tr = document.createElement("tr");
        tr.id = `tr-hora-${hora}`;

        // Celdas de Horas
        const tdHora = document.createElement("td");
        tdHora.className = "hora-col";
        tdHora.textContent = hora;
        const idHora = `hora-label-${hora}`;
        tdHora.id = idHora;
        registrarEventosCelda(tdHora, idHora);
        tr.appendChild(tdHora);

        // Celdas de Contenido / Clases
        grados.forEach(grado => {
          const td = document.createElement("td");
          const idCelda = `celda-${grado}-${hora}`;
          td.id = idCelda;
          registrarEventosCelda(td, idCelda);
          tr.appendChild(td);
        });

        tbody.appendChild(tr);
      });
    }

    function gestionarSeleccionCelda(idCelda, esAcumulativo, esArrastre) {
      if (!esAcumulativo && !esArrastre) {
        celdasSeleccionadas.clear();
      }

      celdasSeleccionadas.add(idCelda);
      actualizarEstilosSeleccion();

      if (idCelda.startsWith("celda-")) {
        const [, grado, hora] = idCelda.split('-');
        document.getElementById("selectGrado").value = grado;
        document.getElementById("selectHora").value = hora;

        cargarDirectorEnInput();

        const diaSel = document.getElementById("selectDiaVista").value;
        const registro = asignaciones.find(a => a.dia === diaSel && a.grado === grado && a.hora === parseInt(hora));
        
        if (registro) {
          document.getElementById("inputMateria").value = registro.materia;
          document.getElementById("inputDocente").value = registro.docente;
        } else {
          document.getElementById("inputMateria").value = "";
          document.getElementById("inputDocente").value = "";
        }
      }
    }

    function actualizarEstilosSeleccion() {
      document.querySelectorAll('.celda-seleccionada').forEach(el => el.classList.remove('celda-seleccionada'));

      celdasSeleccionadas.forEach(idCelda => {
        const celda = document.getElementById(idCelda);
        if (celda) {
          celda.classList.add('celda-seleccionada');
        }
      });

      const texto = document.getElementById("textoSeleccionActual");
      if (celdasSeleccionadas.size === 0) {
        texto.textContent = "👈 Toca celdas para colorear";
        texto.style.color = "#d9534f";
      } else {
        texto.textContent = `📍 ${celdasSeleccionadas.size} celda(s) seleccionada(s)`;
        texto.style.color = "#0275d8";
      }
    }

    function aplicarColor(hexColor) {
      const diaSel = document.getElementById("selectDiaVista").value;

      if (celdasSeleccionadas.size === 0) {
        alert("Por favor selecciona una o varias celdas haciendo clic sobre ellas.");
        return;
      }

      document.getElementById("indicatorBar").style.backgroundColor = hexColor || '#ffffff';

      celdasSeleccionadas.forEach(idCelda => {
        const llaveColor = `${diaSel}_${idCelda}`;

        if (!hexColor) {
          delete coloresCasillas[llaveColor];
        } else {
          coloresCasillas[llaveColor] = hexColor;
        }
      });

      localStorage.setItem("horario_colores_datos", JSON.stringify(coloresCasillas));
      cargarHorarioActual();
    }

    function cargarHorarioActual() {
      const diaSel = document.getElementById("selectDiaVista").value;

      document.querySelectorAll('#tablaHorario th, #tablaHorario td').forEach(celda => {
        const idCelda = celda.id;
        if (idCelda) {
          const llaveColor = `${diaSel}_${idCelda}`;
          if (coloresCasillas[llaveColor]) {
            celda.style.setProperty('background-color', coloresCasillas[llaveColor], 'important');
          } else {
            celda.style.removeProperty('background-color');
          }
        }
      });

      horas.forEach(hora => {
        grados.forEach(grado => {
          const celda = document.getElementById(`celda-${grado}-${hora}`);
          if (celda) celda.innerHTML = "";
        });
      });

      const asignacionesDelDia = asignaciones.filter(a => a.dia === diaSel);
      asignacionesDelDia.forEach(a => {
        const celda = document.getElementById(`celda-${a.grado}-${a.hora}`);
        if (celda) {
          celda.innerHTML = `
            <span class="celda-materia">${a.materia}</span>
            <span class="celda-docente">${a.docente}</span>
          `;
        }
      });

      actualizarEstilosSeleccion();
    }

    function asignarClase() {
      const dia = document.getElementById("selectDiaVista").value;
      const grado = document.getElementById("selectGrado").value;
      const hora = parseInt(document.getElementById("selectHora").value);
      const materia = document.getElementById("inputMateria").value.trim();
      const docente = document.getElementById("inputDocente").value.trim();

      if (!materia || !docente) {
        alert("Por favor ingrese la materia y el docente.");
        return;
      }

      const conflicto = asignaciones.find(a => a.dia === dia && a.hora === hora && a.docente.toLowerCase() === docente.toLowerCase() && a.grado !== grado);

      if (conflicto) {
        alert(`¡CRUCE DE HORARIO EL ${dia}! El docente ${docente} ya tiene clase en el grado ${conflicto.grado} a la hora ${hora}.`);
        return;
      }

      const indexExistente = asignaciones.findIndex(a => a.dia === dia && a.grado === grado && a.hora === hora);
      if (indexExistente !== -1) {
        asignaciones[indexExistente] = { dia, grado, hora, materia, docente };
      } else {
        asignaciones.push({ dia, grado, hora, materia, docente });
      }

      guardarMemoria();
      cargarHorarioActual();
      limpiarFormulario();
    }

    function eliminarClase() {
      const dia = document.getElementById("selectDiaVista").value;
      const grado = document.getElementById("selectGrado").value;
      const hora = parseInt(document.getElementById("selectHora").value);

      const indexExistente = asignaciones.findIndex(a => a.dia === dia && a.grado === grado && a.hora === hora);

      if (indexExistente === -1) {
        alert(`No hay clase en ${grado} el ${dia} a la hora ${hora}.`);
        return;
      }

      if (confirm(`¿Desea borrar la clase del ${dia} (${grado} - Hora ${hora})?`)) {
        asignaciones.splice(indexExistente, 1);
        guardarMemoria();
        cargarHorarioActual();
        limpiarFormulario();
      }
    }

    function agregarGrupo() {
      const nuevoGrupoInput = document.getElementById("inputNuevoGrupo");
      const nombreGrupo = nuevoGrupoInput.value.trim().toUpperCase();

      if (!nombreGrupo) {
        alert("Ingrese el nombre del nuevo grupo.");
        return;
      }

      if (grados.includes(nombreGrupo)) {
        alert(`El grupo ${nombreGrupo} ya existe.`);
        return;
      }

      grados.push(nombreGrupo);
      ordenarGrados();
      guardarGrados();

      actualizarEncabezadosYSeparador();
      construirTabla();
      cargarHorarioActual();

      document.getElementById("selectGrado").value = nombreGrupo;
      cargarDirectorEnInput();
      nuevoGrupoInput.value = "";
    }

    function eliminarGrupo() {
      const gradoSeleccionado = document.getElementById("selectGrado").value;
      if (!gradoSeleccionado) return;

      if (confirm(`¿Está seguro de eliminar el grupo ${gradoSeleccionado}?`)) {
        grados = grados.filter(g => g !== gradoSeleccionado);
        ordenarGrados();
        guardarGrados();

        asignaciones = asignaciones.filter(a => a.grado !== gradoSeleccionado);
        guardarMemoria();

        delete directores[gradoSeleccionado];
        localStorage.setItem("horario_directores_datos", JSON.stringify(directores));

        actualizarEncabezadosYSeparador();
        construirTabla();
        cargarHorarioActual();
        cargarDirectorEnInput();
      }
    }

    function guardarGrados() {
      localStorage.setItem("horario_grados_lista", JSON.stringify(grados));
    }

    function cargarLogo(event) {
      const reader = new FileReader();
      reader.onload = function() {
        logoBase64 = reader.result;
        localStorage.setItem("horario_logo_base64", logoBase64);
        const img = document.getElementById("imgLogo");
        img.src = logoBase64;
        img.style.display = "block";
      };
      if (event.target.files[0]) {
        reader.readAsDataURL(event.target.files[0]);
      }
    }

    function guardarDirector() {
      const grado = document.getElementById("selectGrado").value;
      const nombreDirector = document.getElementById("inputDirector").value.trim().toUpperCase();

      if (!nombreDirector) {
        alert("Ingrese el nombre del Director de Grado.");
        return;
      }

      directores[grado] = nombreDirector;
      localStorage.setItem("horario_directores_datos", JSON.stringify(directores));
      document.getElementById(`director-${grado}`).textContent = nombreDirector;
    }

    function eliminarDirector() {
      const grado = document.getElementById("selectGrado").value;
      if (!directores[grado]) return;

      if (confirm(`¿Eliminar director de grado de ${grado}?`)) {
        delete directores[grado];
        localStorage.setItem("horario_directores_datos", JSON.stringify(directores));
        document.getElementById(`director-${grado}`).textContent = "---";
        document.getElementById("inputDirector").value = "";
      }
    }

    function cargarDirectorEnInput() {
      const grado = document.getElementById("selectGrado").value;
      document.getElementById("inputDirector").value = directores[grado] || "";
    }

    function cambiarDiaVista() {
      const diaSel = document.getElementById("selectDiaVista").value;
      document.getElementById("tituloDiaActual").textContent = `HORARIO DE: ${diaSel}`;
      celdasSeleccionadas.clear();
      limpiarFormulario();
      cargarHorarioActual();
    }

    function guardarMemoria() {
      localStorage.setItem("horario_escolar_datos", JSON.stringify(asignaciones));
    }

    function limpiarFormulario() {
      document.getElementById("inputMateria").value = "";
      document.getElementById("inputDocente").value = "";
      cargarDirectorEnInput();
    }

    function hexToRgb(hex) {
      if (!hex) return null;
      let c = hex.replace('#', '');
      if (c.length === 3) c = c.split('').map(x => x + x).join('');
      const num = parseInt(c, 16);
      return [ (num >> 16) & 255, (num >> 8) & 255, num & 255 ];
    }

    function descargarPDFSemanal() {
      const { jsPDF } = window.jspdf;
      const doc = new jsPDF({ orientation: 'landscape', format: 'a4' });

      diasSemana.forEach((dia, index) => {
        if (index > 0) doc.addPage();

        let startYTable = 16;

        if (logoBase64) {
          try {
            doc.addImage(logoBase64, 'JPEG', 14, 5, 15, 15);
            doc.setFontSize(11);
            doc.text("I. E. JOSÉ ANTONIO AGUILERA - SAN PEDRO VALLE", 32, 11);
            doc.setFontSize(10);
            doc.text(`HORARIO GENERAL DE CLASES - DÍA ${dia}`, 32, 17);
            startYTable = 22;
          } catch (e) {
            doc.setFontSize(12);
            doc.text(`I. E. JOSÉ ANTONIO AGUILERA - HORARIO DÍA ${dia}`, 14, 10);
          }
        } else {
          doc.setFontSize(12);
          doc.text(`I. E. JOSÉ ANTONIO AGUILERA - HORARIO DÍA ${dia}`, 14, 10);
        }

        const headerDirectores = ["DIR.", ...grados.map(g => directores[g] || "---")];
        const headerGrados = ["GRADO", ...grados];

        const dataRows = horas.map(hora => {
          const fila = [hora.toString()];

          grados.forEach(grado => {
            const reg = asignaciones.find(a => a.dia === dia && a.grado === grado && a.hora === hora);
            if (reg) {
              fila.push(`${reg.materia}\n(${reg.docente})`);
            } else {
              fila.push("");
            }
          });

          return fila;
        });

        doc.autoTable({
          startY: startYTable,
          head: [headerDirectores, headerGrados],
          body: dataRows,
          theme: 'grid',
          styles: { fontSize: 6.5, halign: 'center', cellPadding: 1.5, lineColor: [128, 128, 128], lineWidth: 0.2 },
          headStyles: { fillColor: [184, 204, 228], textColor: 0, fontStyle: 'bold' },
          didParseCell: function(data) {
            let idCelda = "";

            if (data.section === 'head') {
              if (data.row.index === 0) { 
                idCelda = data.column.index === 0 ? "head-label-dir" : `director-${grados[data.column.index - 1]}`;
              } else if (data.row.index === 1) { 
                idCelda = data.column.index === 0 ? "head-label-grado" : `grado-header-${grados[data.column.index - 1]}`;
              }
            } else if (data.section === 'body') {
              if (data.column.index === 0) { 
                idCelda = `hora-label-${data.row.index + 1}`;
              } else { 
                idCelda = `celda-${grados[data.column.index - 1]}-${data.row.index + 1}`;
              }
            }

            if (idCelda) {
              const llaveColor = `${dia}_${idCelda}`;
              const colorHex = coloresCasillas[llaveColor];
              if (colorHex) {
                const rgb = hexToRgb(colorHex);
                if (rgb) data.cell.styles.fillColor = rgb;
              }
            }
          }
        });
      });

      doc.save("Horario_Escolar_Semanal_Completo.pdf");
    }

    inicializarEstructura();
  </script>
</body>
</html>
