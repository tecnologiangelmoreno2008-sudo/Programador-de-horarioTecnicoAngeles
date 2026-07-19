
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
      padding: 15px;
      border-radius: 8px;
      margin-bottom: 15px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
      display: flex;
      gap: 10px;
      flex-wrap: wrap;
      align-items: center;
    }

    input, select, button {
      padding: 8px 10px;
      font-size: 13px;
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
      border: 1px solid #b0b0b0;
      padding: 5px;
      min-width: 85px;
      height: 40px;
    }

    /* Fila del Director de Grado */
    .fila-director th {
      background-color: #cbd5e0;
      color: #1a202c;
      font-weight: bold;
      font-size: 9px;
    }

    /* Fila del Grado */
    .fila-grado th {
      background-color: #2a3b5c;
      color: white;
      font-weight: bold;
    }

    .hora-col { background-color: #e2e8f0; font-weight: bold; color: #900; width: 35px; }

    .celda-materia { font-weight: bold; display: block; color: #1a202c; text-transform: uppercase; font-size: 11px; }
    .celda-docente { font-size: 9px; color: #4a5568; text-transform: uppercase; display: block; }

    .celda-activa { cursor: pointer; }
    .celda-activa:hover { background-color: #edf2f7; }

    .bloqueado { background-color: #002060; color: #002060; cursor: not-allowed; }
  </style>
</head>
<body>

  <!-- Header con Logo Institucional -->
  <div class="encabezado-institucional">
    <img id="imgLogo" class="logo-img" style="display:none;" alt="Logo Institución">
    <div class="titulo-contenedor">
      <h2>I. E. JOSÉ ANTONIO AGUILERA - SAN PEDRO VALLE</h2>
      <div class="subtitulo" id="tituloDiaActual">HORARIO DE: LUNES</div>
    </div>
  </div>

  <!-- Panel de control -->
  <div class="panel-control">
    <label><b>Cargar Logo:</b></label>
    <input type="file" id="inputLogo" accept="image/*" onchange="cargarLogo(event)" style="width: 180px;">

    <span style="border-left: 2px solid #ccc; height: 25px; margin: 0 5px;"></span>

    <label><b>Día:</b></label>
    <select id="selectDiaVista" onchange="cambiarDiaVista()">
      <option value="LUNES">LUNES</option>
      <option value="MARTES">MARTES</option>
      <option value="MIÉRCOLES">MIÉRCOLES</option>
      <option value="JUEVES">JUEVES</option>
      <option value="VIERNES">VIERNES</option>
    </select>

    <!-- Gestión de Grupos -->
    <input type="text" id="inputNuevoGrupo" placeholder="Nuevo Grupo (ej. 8-4)" style="width: 130px;">
    <button class="btn-grupo-add" onclick="agregarGrupo()">+ Grupo</button>
    <button class="btn-grupo-del" onclick="eliminarGrupo()">- Grupo</button>

    <span style="border-left: 2px solid #ccc; height: 25px; margin: 0 5px;"></span>

    <label><b>Grado:</b></label>
    <select id="selectGrado" onchange="cargarDirectorEnInput()"></select>

    <input type="text" id="inputDirector" placeholder="Director de Grado">
    <button class="btn-director" onclick="guardarDirector()">Asignar Dir.</button>
    <button class="btn-eliminar-dir" onclick="eliminarDirector()">Borrar Dir.</button>

    <span style="border-left: 2px solid #ccc; height: 25px; margin: 0 5px;"></span>

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

    <input type="text" id="inputMateria" placeholder="Materia (ej. ALGEBRA)">
    <input type="text" id="inputDocente" placeholder="Docente (ej. ANGELA MARIA)">

    <button class="btn-guardar" onclick="asignarClase()">Asignar Clase</button>
    <button class="btn-eliminar" onclick="eliminarClase()">Eliminar Clase</button>
    <button class="btn-limpiar" onclick="limpiarFormulario()">Limpiar</button>
    <button class="btn-pdf" onclick="descargarPDFSemanal()">📄 Descargar PDF Semanal</button>
  </div>

  <!-- Tabla HTML -->
  <div class="contenedor-tabla">
    <table id="tablaHorario">
      <thead>
        <!-- PRIMERA FILA: DIRECTOR DE GRADO -->
        <tr id="filaDirectores" class="fila-director">
          <th style="background-color: #a0aec0; color: #1a202c; font-weight: bold;">DIR. GRADO</th>
        </tr>
        <!-- SEGUNDA FILA: NOMBRE DEL GRADO -->
        <tr id="encabezadoTabla" class="fila-grado">
          <th class="hora-col" style="background-color: #2a3b5c; color: #900;">GRADO</th>
        </tr>
      </thead>
      <tbody></tbody>
    </table>
  </div>

  <script>
    const gradosPredeterminados = [
      "8-1", "8-2", "8-3",
      "9-1", "9-2", "9-3",
      "10-1", "10-2", "10-3",
      "11-1", "11-2", "11-3"
    ];

    let grados = JSON.parse(localStorage.getItem("horario_grados_lista")) || gradosPredeterminados;
    const horas = [1, 2, 3, 4, 5, 6, 7];
    const diasSemana = ["LUNES", "MARTES", "MIÉRCOLES", "JUEVES", "VIERNES"];
    
    let asignaciones = JSON.parse(localStorage.getItem("horario_escolar_datos")) || [];
    let directores = JSON.parse(localStorage.getItem("horario_directores_datos")) || {};
    let logoBase64 = localStorage.getItem("horario_logo_base64") || "";

    // Función para ordenar los grupos (ej. 8-1, 8-2, 8-3, 8-4, 9-1...)
    function ordenarGrados() {
      grados.sort((a, b) => a.localeCompare(b, undefined, { numeric: true, sensitivity: 'base' }));
    }

    function inicializarEstructura() {
      ordenarGrados();
      actualizarEncabezadosYSeparador();

      if (logoBase64) {
        const img = document.getElementById("imgLogo");
        img.src = logoBase64;
        img.style.display = "block";
      }

      construirTabla();
      cargarHorarioActual();
      cargarDirectorEnInput();
    }

    function actualizarEncabezadosYSeparador() {
      const selectGrado = document.getElementById("selectGrado");
      const filaDir = document.getElementById("filaDirectores");
      const encabezado = document.getElementById("encabezadoTabla");

      selectGrado.innerHTML = "";
      filaDir.innerHTML = '<th style="background-color: #a0aec0; color: #1a202c; font-weight: bold;">DIR. GRADO</th>';
      encabezado.innerHTML = '<th class="hora-col" style="background-color: #2a3b5c; color: #900;">GRADO</th>';

      grados.forEach(g => {
        const option = document.createElement("option");
        option.value = g;
        option.textContent = g;
        selectGrado.appendChild(option);

        const thDir = document.createElement("th");
        thDir.id = `director-${g}`;
        thDir.textContent = directores[g] || "---";
        filaDir.appendChild(thDir);

        const th = document.createElement("th");
        th.textContent = g;
        encabezado.appendChild(th);
      });
    }

    function agregarGrupo() {
      const nuevoGrupoInput = document.getElementById("inputNuevoGrupo");
      const nombreGrupo = nuevoGrupoInput.value.trim().toUpperCase();

      if (!nombreGrupo) {
        alert("Por favor ingrese el nombre del nuevo grupo (ej. 8-4).");
        return;
      }

      if (grados.includes(nombreGrupo)) {
        alert(`El grupo ${nombreGrupo} ya existe.`);
        return;
      }

      grados.push(nombreGrupo);
      ordenarGrados(); // Se ordena de forma automática
      guardarGrados();

      actualizarEncabezadosYSeparador();
      construirTabla();
      cargarHorarioActual();
      
      selectGrado.value = nombreGrupo;
      cargarDirectorEnInput();

      nuevoGrupoInput.value = "";
    }

    function eliminarGrupo() {
      const gradoSeleccionado = document.getElementById("selectGrado").value;

      if (!gradoSeleccionado) {
        alert("No hay ningún grupo seleccionado.");
        return;
      }

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

    function construirTabla() {
      const tbody = document.querySelector("#tablaHorario tbody");
      tbody.innerHTML = "";

      horas.forEach(hora => {
        const tr = document.createElement("tr");

        const tdHora = document.createElement("td");
        tdHora.className = "hora-col";
        tdHora.textContent = hora;
        tr.appendChild(tdHora);

        grados.forEach(grado => {
          const td = document.createElement("td");
          td.id = `celda-${grado}-${hora}`;

          const esOctavoONoveno = grado.startsWith("8-") || grado.startsWith("9-");
          if (esOctavoONoveno && (hora === 6 || hora === 7)) {
            td.className = "bloqueado";
          } else {
            td.className = "celda-activa";
            td.onclick = () => seleccionarCelda(grado, hora);
          }

          tr.appendChild(td);
        });

        tbody.appendChild(tr);
      });
    }

    function guardarDirector() {
      const grado = document.getElementById("selectGrado").value;
      const nombreDirector = document.getElementById("inputDirector").value.trim().toUpperCase();

      if (!nombreDirector) {
        alert("Por favor ingrese el nombre del Director de Grado.");
        return;
      }

      directores[grado] = nombreDirector;
      localStorage.setItem("horario_directores_datos", JSON.stringify(directores));

      document.getElementById(`director-${grado}`).textContent = nombreDirector;
    }

    function eliminarDirector() {
      const grado = document.getElementById("selectGrado").value;

      if (!directores[grado]) {
        alert(`El grado ${grado} no tiene un director asignado.`);
        return;
      }

      if (confirm(`¿Está seguro de eliminar al director de grado de ${grado}?`)) {
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
      limpiarFormulario();
      cargarHorarioActual();
    }

    function cargarHorarioActual() {
      const diaSel = document.getElementById("selectDiaVista").value;

      horas.forEach(hora => {
        grados.forEach(grado => {
          const celda = document.getElementById(`celda-${grado}-${hora}`);
          if (celda && !celda.classList.contains("bloqueado")) {
            celda.innerHTML = "";
          }
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
    }

    function seleccionarCelda(grado, hora) {
      document.getElementById("selectGrado").value = grado;
      document.getElementById("selectHora").value = hora;

      cargarDirectorEnInput();

      const diaSel = document.getElementById("selectDiaVista").value;
      const registro = asignaciones.find(a => a.dia === diaSel && a.grado === grado && a.hora === hora);
      
      if (registro) {
        document.getElementById("inputMateria").value = registro.materia;
        document.getElementById("inputDocente").value = registro.docente;
      } else {
        document.getElementById("inputMateria").value = "";
        document.getElementById("inputDocente").value = "";
      }
    }

    function asignarClase() {
      const dia = document.getElementById("selectDiaVista").value;
      const grado = document.getElementById("selectGrado").value;
      const hora = parseInt(document.getElementById("selectHora").value);
      const materia = document.getElementById("inputMateria").value.trim();
      const docente = document.getElementById("inputDocente").value.trim();

      const esOctavoONoveno = grado.startsWith("8-") || grado.startsWith("9-");

      if (esOctavoONoveno && (hora === 6 || hora === 7)) {
        alert(`El grado ${grado} solo tiene jornada hasta la 5ª hora.`);
        return;
      }

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

    function guardarMemoria() {
      localStorage.setItem("horario_escolar_datos", JSON.stringify(asignaciones));
    }

    function limpiarFormulario() {
      document.getElementById("inputMateria").value = "";
      document.getElementById("inputDocente").value = "";
      cargarDirectorEnInput();
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
            const esOctavoONoveno = grado.startsWith("8-") || grado.startsWith("9-");
            if (esOctavoONoveno && (hora === 6 || hora === 7)) {
              fila.push("---");
            } else {
              const reg = asignaciones.find(a => a.dia === dia && a.grado === grado && a.hora === hora);
              if (reg) {
                fila.push(`${reg.materia}\n(${reg.docente})`);
              } else {
                fila.push("");
              }
            }
          });

          return fila;
        });

        doc.autoTable({
          startY: startYTable,
          head: [headerDirectores, headerGrados],
          body: dataRows,
          theme: 'grid',
          styles: { fontSize: 6.5, halign: 'center', cellPadding: 1.5 },
          headStyles: { fillColor: [42, 59, 92], textColor: 255, fontStyle: 'bold' },
          alternateRowStyles: { fillColor: [245, 247, 250] }
        });
      });

      doc.save("Horario_Escolar_Semanal_Completo.pdf");
    }

    inicializarEstructura();
  </script>
</body>
</html>
