// Memoria inicial local en el iPhone
if (!localStorage.getItem("db_alimentos")) localStorage.setItem("db_alimentos", JSON.stringify([]));
if (!localStorage.getItem("db_registros")) localStorage.setItem("db_registros", JSON.stringify([]));

// Estado temporal de la calculadora
let platoConstruido = [];

document.addEventListener("DOMContentLoaded", () => {
  actualizarApp();
});

function actualizarApp() {
  renderizarBiblioteca();
  poblarTodosLosSelectores();
  actualizarPanelInicio();
}

function cambiarTab(tabId) {
  document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
  document.querySelectorAll('.nav-btn').forEach(el => el.classList.remove('active'));
  document.getElementById(`tab-${tabId}`).classList.add('active');
  document.getElementById(`btn-${tabId}`).classList.add('active');
}

// ==========================================
// LÓGICA DEL MÓDULO 1 (BIBLIOTECA)
// ==========================================
function guardarAlimento() {
  const nombre = document.getElementById("bib-nombre").value.trim();
  const categoria = document.getElementById("bib-categoria").value.trim() || "General";
  const carbos = parseFloat(document.getElementById("bib-carbos").value) || 0;
  const absorcion = document.getElementById("bib-absorcion").value;
  const notas = document.getElementById("bib-notas").value.trim();

  if (!nombre) return alert("El nombre es obligatorio.");

  const nuevo = { id: Date.now().toString(), nombre, categoria, carbosHabituales: carbos, absorcion, notas };
  let alimentos = JSON.parse(localStorage.getItem("db_alimentos"));
  alimentos.push(nuevo);
  localStorage.setItem("db_alimentos", JSON.stringify(alimentos));

  document.getElementById("bib-nombre").value = "";
  document.getElementById("bib-carbos").value = "";
  document.getElementById("bib-notas").value = "";
  document.querySelector("details").removeAttribute("open");
  
  actualizarApp();
}

function obtenerEstadisticasAlimento(alimentoId) {
  const registros = JSON.parse(localStorage.getItem("db_registros")) || [];
  const filtrados = registros.filter(r => r.alimentoId === alimentoId);
  if(filtrados.length === 0) return null;

  let sumaPre = 0, sumaInsulina = 0, cBoloNormal = 0, cBoloDual = 0;
  let sumaG1 = 0, sumaG2 = 0, sumaG3 = 0, sumaG4 = 0, c1=0, c2=0, c3=0, c4=0;

  filtrados.forEach(r => {
    sumaPre += parseFloat(r.glucemiaPre || 0);
    sumaInsulina += parseFloat(r.insulinaInyectada || 0);
    if(r.tipoBolo === "Dual") cBoloDual++; else cBoloNormal++;
    if(r.g1) { sumaG1 += parseFloat(r.g1); c1++; }
    if(r.g2) { sumaG2 += parseFloat(r.g2); c2++; }
    if(r.g3) { sumaG3 += parseFloat(r.g3); c3++; }
    if(r.g4) { sumaG4 += parseFloat(r.g4); c4++; }
  });

  return {
    veces: filtrados.length,
    glucemiaPreMedia: Math.round(sumaPre / filtrados.length),
    insulinaMedia: (sumaInsulina / filtrados.length).toFixed(1),
    boloComun: cBoloDual >= cBoloNormal ? "Dual" : "Normal",
    g1m: c1 ? Math.round(sumaG1/c1) : '--', g2m: c2 ? Math.round(sumaG2/c2) : '--',
    g3m: c3 ? Math.round(sumaG3/c3) : '--', g4m: c4 ? Math.round(sumaG4/c4) : '--'
  };
}

function renderizarBiblioteca() {
  const alimentos = JSON.parse(localStorage.getItem("db_alimentos"));
  const filtro = document.getElementById("buscador-bib").value.toLowerCase();
  const contenedor = document.getElementById("lista-alimentos");
  contenedor.innerHTML = "";

  alimentos.filter(a => a.nombre.toLowerCase().includes(filtro)).forEach(alimento => {
    const stats = obtenerEstadisticasAlimento(alimento.id);
    const div = document.createElement("div");
    div.className = "alimento-item";
    div.innerHTML = `
      <div class="alimento-header"><strong>${alimento.nombre}</strong><span class="badge">${alimento.categoria}</span></div>
      <div style="margin-top: 5px;">
        <span class="badge absorcion">⏳ Abs. ${alimento.absorcion}</span>
        <span class="badge" style="background:#fff3cd; color:#664d03;">🍞 ${alimento.carbosHabituales} Raciones</span>
      </div>
      ${alimento.notas ? `<p style="font-size:13px; margin:6px 0; color:#555; font-style:italic;">📌 ${alimento.notas}</p>` : ''}
      <div class="stats-grid" style="border-top:1px dashed #eee; margin-top:8px; padding-top:8px;">
        <div>🔄 Consumido: ${stats ? stats.veces : 0} vec</div>
        <div>🩸 Pre Media: ${stats ? stats.glucemiaPreMedia : '--'}</div>
        <div>💉 Bolo: ${stats ? stats.boloComun : '--'}</div>
        <div>💉 Insulina M: ${stats ? stats.insulinaMedia+'U' : '--'}</div>
        <div style="grid-column: span 2;">⏳ Glucemias Post (+1h a +4h): <b>${stats ? `${stats.g1m}|${stats.g2m}|${stats.g3m}|${stats.g4m}` : '--|--|--|--'}</b></div>
      </div>
    `;
    contenedor.innerHTML += div.outerHTML;
  });
}

function poblarTodosLosSelectores() {
  const alimentos = JSON.parse(localStorage.getItem("db_alimentos"));
  const selectores = ["reg-alimento-id", "calcu-alimento-id", "rec-alimento-id"];
  
  selectores.forEach(id => {
    const select = document.getElementById(id);
    if (!select) return;
    const valorGuardado = select.value;
    select.innerHTML = '<option value="">-- Selecciona --</option>';
    alimentos.forEach(a => {
      select.innerHTML += `<option value="${a.id}">${a.nombre}</option>`;
    });
    select.value = valorGuardado;
  });
}

// ==========================================
// LÓGICA DEL MÓDULO 2 (REGISTRO)
// ==========================================
function actualizarPrevisualizacionHistorica() {
  const id = document.getElementById("reg-alimento-id").value;
  const panel = document.getElementById("previsualizacion-historica");
  if(!id) { panel.style.display = "none"; return; }
  
  const stats = obtenerEstadisticasAlimento(id);
  if(stats) {
    panel.style.display = "grid";
    panel.innerHTML = `<div>🔄 Veces: ${stats.veces}</div><div>🩸 Pre M: ${stats.glucemiaPreMedia}</div><div>💉 Insulina M: ${stats.insulinaMedia}U</div><div>⏱️ Hist: ${stats.g1m}|${stats.g2m}|${stats.g3m}|${stats.g4m}</div>`;
  } else {
    panel.style.display = "block"; panel.innerHTML = "✨ Primera vez que registras este alimento.";
  }
}

function guardarRegistroComida() {
  const alimentoId = document.getElementById("reg-alimento-id").value;
  const glucemiaPre = document.getElementById("reg-glucemia-pre").value;
  const raciones = document.getElementById("reg-raciones").value;
  const insulinaInyectada = document.getElementById("reg-insulina-inyectada").value;
  const tipoBolo = document.getElementById("reg-tipo-bolo").value;
  
  if(!alimentoId || !glucemiaPre || !raciones || !insulinaInyectada) return alert("Rellena los campos obligatorios.");

  const nuevoRegistro = {
    id: Date.now().toString(), alimentoId, fecha: new Date().toLocaleString(), timestamp: Date.now(),
    glucemiaPre, insulinaActiva: document.getElementById("reg-insulina-activa").value,
    deportePrevio: document.getElementById("reg-deporte-previo").value, raciones, insulinaInyectada, tipoBolo,
    comentarios: document.getElementById("reg-comentarios").value, g1: null, g2: null, g3: null, g4: null
  };

  let registros = JSON.parse(localStorage.getItem("db_registros"));
  registros.unshift(nuevoRegistro);
  localStorage.setItem("db_registros", JSON.stringify(registros));

  document.getElementById("reg-glucemia-pre").value = "";
  document.getElementById("reg-raciones").value = "";
  document.getElementById("reg-insulina-inyectada").value = "";
  document.getElementById("reg-comentarios").value = "";
  
  alert("¡Comida registrada con éxito!");
  cambiarTab('inicio');
  actualizarApp();
}

// ==========================================
// LÓGICA DEL MÓDULO 3 (CALCULADORA)
// ==========================================
function agregarALaCalculadora() {
  const id = document.getElementById("calcu-alimento-id").value;
  const mult = parseFloat(document.getElementById("calcu-multiplicador").value) || 1;
  if(!id) return alert("Selecciona un ingrediente.");

  const alimentos = JSON.parse(localStorage.getItem("db_alimentos"));
  const al = alimentos.find(a => a.id === id);

  platoConstruido.push({ ...al, multiplicador: mult, hcCalculados: al.carbosHabituales * mult });
  renderizarCalculadora();
}

function renderizarCalculadora() {
  const lista = document.getElementById("calcu-lista-items");
  if(platoConstruido.length === 0) {
    lista.innerHTML = "<li>El plato está vacío</li>";
    document.getElementById("calcu-total-hc").innerText = "0";
    document.getElementById("calcu-absorcion-sugerida").innerText = "--";
    return;
  }

  lista.innerHTML = "";
  let totalHc = 0;
  let tieneLenta = false;

  platoConstruido.forEach((item, index) => {
    totalHc += item.hcCalculados;
    if(item.absorcion === "Lenta/Grasas") tieneLenta = true;
    lista.innerHTML += `<li>${item.nombre} (x${item.multiplicador}) ➡️ ${(item.hcCalculados).toFixed(1)} raciones <button onclick="eliminarDeCalculadora(${index})" style="width:auto; padding:2px 6px; background:red; font-size:10px; margin-left:10px;">X</button></li>`;
  });

  document.getElementById("calcu-total-hc").innerText = totalHc.toFixed(1);
  document.getElementById("calcu-absorcion-sugerida").innerText = tieneLenta ? "Lenta (Sugerido Bolo Dual)" : "Media/Rápida (Bolo Normal)";
}

function eliminarDeCalculadora(index) {
  platoConstruido.splice(index, 1);
  renderizarCalculadora();
}

function volcarCalculadoraARegistro() {
  if(platoConstruido.length === 0) return alert("Tu plato está vacío.");
  const totalHc = document.getElementById("calcu-total-hc").innerText;
  
  cambiarTab('registro');
  document.getElementById("reg-alimento-id").value = ""; 
  document.getElementById("reg-raciones").value = totalHc;
  actualizarPrevisualizacionHistorica();
}

function guardarComboComoPlatoHabitual() {
  if(platoConstruido.length === 0) return alert("Agrega cosas al plato primero.");
  const nombreCombo = prompt("Introduce un nombre para este plato combinado:");
  if(!nombreCombo) return;

  const totalHc = parseFloat(document.getElementById("calcu-total-hc").innerText);
  const tieneLenta = document.getElementById("calcu-absorcion-sugerida").innerText.includes("Lenta");

  const nuevoPlato = {
    id: Date.now().toString(), nombre: nombreCombo, categoria: "Plato Combinado",
    carbosHabituales: totalHc, absorcion: tieneLenta ? "Lenta/Grasas" : "Media", notas: "Creado desde la calculadora."
  };

  let alimentos = JSON.parse(localStorage.getItem("db_alimentos"));
  alimentos.push(nuevoPlato);
  localStorage.setItem("db_alimentos", JSON.stringify(alimentos));
  
  alert("¡Combo guardado en tu biblioteca como plato frecuente!");
  platoConstruido = [];
  renderizarCalculadora();
  actualizarApp();
}

// ==========================================
// LÓGICA DEL MÓDULO 4 (RECOMENDACIONES)
// ==========================================
function generarRecomendacionInteligente() {
  const id = document.getElementById("rec-alimento-id").value;
  const contenedor = document.getElementById("rec-resultado");
  if(!id) { contenedor.innerHTML = "<p>Selecciona un alimento para analizar.</p>"; return; }

  const registros = JSON.parse(localStorage.getItem("db_registros")) || [];
  const filtrados = registros.filter(r => r.alimentoId === id);

  if(filtrados.length === 0) {
    contenedor.innerHTML = "<div class='card'>💡 <b>Sin histórico:</b> Aún no hay datos de inyecciones para este alimento. Registra comidas para activar la inteligencia.</div>";
    return;
  }

  // Buscar el "mejor" registro (el que tenga menor variabilidad postprandial y termine lo más estable posible)
  // Evaluamos el éxito si las glucemias de la +2h y +4h están lo más cercanas a 100-140 mg/dL posibles
  let mejorRegistro = null;
  let puntuacionMinima = 999;

  filtrados.forEach(r => {
    if(r.g2 && r.g4) {
      let desvio = Math.abs(r.g2 - 120) + Math.abs(r.g4 - 110);
      if(desvio < puntuacionMinima) { puntuacionMinima = desvio; mejorRegistro = r; }
    }
  });

  if(mejorRegistro) {
    contenedor.innerHTML = `
      <div class="card" style="background:#e3f2fd; border-left:4px solid #007aff;">
        <h4>💡 Patrón de Éxito Encontrado</h4>
        <p>Has consumido este alimento <b>${filtrados.length} veces</b>. Tu respuesta glucémica más estable fue con la siguiente pauta:</p>
        <ul>
          <li><b>Raciones reales:</b> ${mejorRegistro.raciones} HC</li>
          <li><b>Dosis aplicada:</b> ${mejorRegistro.insulinaInyectada} Unidades</li>
          <li><b>Estrategia de bolo:</b> Bolo ${mejorRegistro.tipoBolo}</li>
          <li><b>Glucemia previa:</b> ${mejorRegistro.glucemiaPre} mg/dL</li>
        </ul>
        <p style="font-size:12px; color:#555; font-style:italic;">Resultados de ese día: +1h: ${mejorRegistro.g1 || '--'} | +2h: ${mejorRegistro.g2} | +3h: ${mejorRegistro.g3 || '--'} | +4h: ${mejorRegistro.g4} mg/dL.</p>
      </div>
    `;
  } else {
    contenedor.innerHTML = `<div class='card'>💡 El alimento se ha registrado <b>${filtrados.length} veces</b>, pero necesitas completar las glucemias de <b>+2h y +4h</b> en el Inicio para que el algoritmo pueda darte la pauta óptima exacta.</div>`;
  }
}

// ==========================================
// LÓGICA DEL MÓDULO 5 (PANEL DE INICIO)
// ==========================================
function actualizarPanelInicio() {
  const registros = JSON.parse(localStorage.getItem("db_registros")) || [];
  const alimentos = JSON.parse(localStorage.getItem("db_alimentos")) || [];
  
  const ultCard = document.getElementById("inicio-ultimo");
  if(registros.length > 0) {
    const ult = registros[0]; const al = alimentos.find(a => a.id === ult.alimentoId);
    ultCard.innerHTML = `<b>${ult.fecha}</b><br>🍔 ${al ? al.nombre : 'Plato combinado'} (${ult.raciones} raciones)<br>🩸 Previa: ${ult.glucemiaPre} mg/dL | 💉 ${ult.insulinaInyectada}U (${ult.tipoBolo})`;
  } else {
    ultCard.innerHTML = "Aún no has registrado ninguna comida.";
  }

  const pendCard = document.getElementById("inicio-pendientes");
  const pendientes = registros.filter(r => (Date.now() - r.timestamp < 5 * 60 * 60 * 1000) && (!r.g1 || !r.g2 || !r.g3 || !r.g4));

  if(pendientes.length > 0) {
    pendCard.innerHTML = "";
    pendientes.forEach(p => {
      const al = alimentos.find(a => a.id === p.alimentoId);
      const div = document.createElement("div");
      div.className = "card"; div.style = "background:#fff; border:1px solid #ff9500; margin-top:8px; font-size:14px; padding:10px;";
      div.innerHTML = `
        <strong>${al ? al.nombre : 'Plato Combinado'}</strong><br><span style="font-size:11px; color:#666;">${p.fecha}</span>
        <div style="margin-top:8px; display:grid; grid-template-columns: repeat(4, 1fr); gap:4px;">
          <div>+1h: <input type="number" id="g1-${p.id}" value="${p.g1||''}" style="padding:4px; font-size:12px;" onchange="actualizarHoraPost('${p.id}', 1)"></div>
          <div>+2h: <input type="number" id="g2-${p.id}" value="${p.g2||''}" style="padding:4px; font-size:12px;" onchange="actualizarHoraPost('${p.id}', 2)"></div>
          <div>+3h: <input type="number" id="g3-${p.id}" value="${p.g3||''}" style="padding:4px; font-size:12px;" onchange="actualizarHoraPost('${p.id}', 3)"></div>
          <div>+4h: <input type="number" id="g4-${p.id}" value="${p.g4||''}" style="padding:4px; font-size:12px;" onchange="actualizarHoraPost('${p.id}', 4)"></div>
        </div>
      `;
      pendCard.appendChild(div);
    });
  } else {
    pendCard.innerHTML = "No tienes mediciones postprandiales pendientes de las últimas 5 horas.";
  }
}

function actualizarHoraPost(registroId, horaNum) {
  let registros = JSON.parse(localStorage.getItem("db_registros"));
  const index = registros.findIndex(r => r.id === registroId);
  if(index !== -1) {
    const valor = document.getElementById(`g${horaNum}-${registroId}`).value;
    registros[index][`g${horaNum}`] = valor ? parseFloat(valor) : null;
    localStorage.setItem("db_registros", JSON.stringify(registros));
    actualizarApp();
  }
}
