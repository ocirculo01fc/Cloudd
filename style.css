let salaAtual = null;
let codigoAtual = null;
let salaCodigoAtual = null;
let API = null;

// ==========================
// INICIALIZAÇÃO
// ==========================
document.addEventListener('DOMContentLoaded', () => {

  // botão selecionar servidor
  document.getElementById("EscolherS").onclick = escolherServidor;

  // tenta recuperar servidor salvo
  const apiSalva = localStorage.getItem("apiSalva");
  if (apiSalva) {
  API = apiSalva;

  document.getElementById("servidor").classList.add("hidden");
  document.getElementById("d").classList.remove("hidden");

  showToast("Servidor carregado automaticamente!", "success");
  carregarSalas();
}

  // fecha modais com ESC
  document.addEventListener('keydown', (e) => {
    if (e.key === 'Escape') {
      fecharModal();
      fecharModalCodigo();
    }
  });

  // auto refresh
  setInterval(() => {
    if (API) carregarSalas();
  }, 30000);
});


// ==========================
// SELECIONAR SERVIDOR
// ==========================
function escolherServidor() {
  const input = document.getElementById("input-server");
  let valor = input.value.trim();

  if (!valor) {
    showToast("Digite um servidor!", "error");
    return;
  }

  if (!valor.startsWith("http")) {
    valor = "https://" + valor;
  }

  valor = valor.replace(/\/+$/, "");

  API = valor;


  showToast("Servidor definido com sucesso!", "success");


  // esconde seleção (opcional)
  // esconde seleção
document.getElementById("servidor").classList.add("hidden");

// mostra app
document.getElementById("d").classList.remove("hidden");

  carregarSalas();
}


// ==========================
// VALIDADOR GLOBAL
// ==========================
function precisaAPI() {
  if (!API) {
    showToast("Selecione um servidor primeiro!", "warning");
    return false;
  }
  return true;
}


// ==========================
// TOAST
// ==========================
function showToast(message, type = 'success') {
  const container = document.getElementById('toastContainer');
  const toast = document.createElement('div');
  toast.className = `toast ${type}`;
  toast.innerHTML = `
    <span>${type === 'success' ? '✓' : type === 'error' ? '✕' : '⚠'}</span>
    <span>${message}</span>
  `;
  container.appendChild(toast);

  setTimeout(() => {
    toast.style.opacity = '0';
    toast.style.transform = 'translateX(100px)';
    setTimeout(() => toast.remove(), 300);
  }, 4000);
}


// ==========================
// UTILITÁRIOS
// ==========================
function formatCurrency(value) {
  return new Intl.NumberFormat('pt-BR', {
    style: 'currency',
    currency: 'BRL'
  }).format(value);
}

function formatDate(dateStr, timeStr) {
  if (!dateStr) return 'Data não informada';
  const [year, month, day] = dateStr.split('-');
  return `${day}/${month} • ${timeStr || '--:--'}`;
}

function formatarStatus(status) {
  const map = {
    'aberta': { text: 'Aberta', class: 'badge-open', icon: '🟢' },
    'iniciada': { text: 'Em andamento', class: 'badge-started', icon: '🔵' },
    'lotada': { text: 'Lotada', class: 'badge-full', icon: '🟡' },
    'fechada': { text: 'Encerrada', class: 'badge-closed', icon: '🔴' }
  };
  return map[status] || { text: status, class: 'badge-closed', icon: '⚪' };
}


// ==========================
// CARREGAR SALAS
// ==========================
async function carregarSalas() {
  if (!precisaAPI()) return;

  const lista = document.getElementById('listaSalas');

  try {
    const res = await fetch(API + "/salas");

    if (!res.ok) throw new Error("Erro na API");

    const salas = await res.json();

    if (!salas || salas.length === 0) {
      lista.innerHTML = `
        <div class="empty-state">
          <span class="empty-state-icon">🎮</span>
          <h3>Nenhuma sala disponível</h3>
        </div>
      `;
      return;
    }

    lista.innerHTML = salas.map(s => {
      const status = formatarStatus(s.status);

      let botao = '';
      if (s.status === "aberta") {
        botao = `<button class="btn btn-success" onclick="abrirInscricao('${s.uid}')">🎯 Participar</button>`;
      } else if (s.status === "iniciada") {
        botao = `<button class="btn" onclick="abrirModalCodigo('${s.uid}')">🔑 Entrar</button>`;
      } else {
        botao = `<button disabled>Indisponível</button>`;
      }

      return `
        <div class="card">
          <div class="card-header">
            <span>${s.nome}</span>
            <span class="${status.class}">${status.icon} ${status.text}</span>
          </div>

          <div>
            ${formatDate(s.data, s.hora)}<br>
            ${formatCurrency(s.valor)}<br>
            ${s.tipo?.toUpperCase() || 'Solo'}<br>
            ${s.inscritos || 0}/${s.max_inscritos}
          </div>

          ${botao}
        </div>
      `;
    }).join('');

  } catch (err) {
    console.error(err);
    lista.innerHTML = `<p>Erro ao carregar salas</p>`;
  }
}


// ==========================
// INSCRIÇÃO
// ==========================
async function abrirInscricao(uid) {
  if (!precisaAPI()) return;

  salaAtual = uid;

  try {
    const [resSala, resCodigo] = await Promise.all([
      fetch(API + "/salas/" + uid),
      fetch(API + "/salas/" + uid + "/gerar-codigo")
    ]);

    const dataSala = await resSala.json();
    const dataCodigo = await resCodigo.json();

    if (!dataSala.ok) {
      showToast("Sala não encontrada", "error");
      return;
    }

    const sala = dataSala.sala || dataSala;
    const tipo = (sala.tipo || "solo").toLowerCase();

    codigoAtual = dataCodigo.codigo;

    const container = document.getElementById("carregarModal");

    // 🔥 DEFINE QUANTIDADE DE NICKS
    let nickFields = `
      <div class="form-group">
        <label>Nick Principal *</label>
        <input type="text" id="nick1" required>
      </div>
    `;

    if (tipo === "duo" || tipo === "squad") {
      nickFields += `
        <div class="form-group">
          <label>Nick 2</label>
          <input type="text" id="nick2">
        </div>
      `;
    }

    if (tipo === "squad") {
      nickFields += `
        <div class="form-group">
          <label>Nick 3</label>
          <input type="text" id="nick3">
        </div>
        <div class="form-group">
          <label>Nick 4</label>
          <input type="text" id="nick4">
        </div>
      `;
    }

    // 🔥 MONTA HTML COMPLETO
    container.innerHTML = `
      <div class="modal active" onclick="fecharInscricao(event)">
        <div class="modal-content" onclick="event.stopPropagation()">
          
          <div class="modal-header">
            <h3>📝 Inscrição na Sala</h3>
            <button class="modal-close" onclick="fecharInscricao()">&times;</button>
          </div>

          <div class="code-display">
            <div class="label">Seu código de inscrição</div>
            <div class="code">${codigoAtual}</div>
          </div>

          <form onsubmit="enviarComprovante(event)">
            
            <div class="form-group">
              <label>Nome Completo</label>
              <input type="text" id="nomeCompleto" required>
            </div>

            <div class="form-group">
              <label>Chave Pix</label>
              <input type="text" id="chavePix" required>
            </div>

            ${nickFields}

            <div class="form-group">
              <label>Comprovante</label>
              <input type="file" id="comprovante" required>
            </div>

            <button type="submit" class="btn btn-success">
              ✅ Enviar Inscrição
            </button>

          </form>
        </div>
      </div>
    `;

  } catch (err) {
    console.error(err);
    showToast("Erro ao abrir inscrição", "error");
  }
}


// ==========================
// ENVIAR COMPROVANTE
// ==========================
async function enviarComprovante(e) {
  e.preventDefault();

  if (!precisaAPI()) return;

  const btn = e.target.querySelector('button[type="submit"]');
  const original = btn.innerHTML;

  btn.innerHTML = "Enviando...";
  btn.disabled = true;

  try {
    const form = new FormData();

    // 🔥 DADOS
    form.append("codigo", codigoAtual);
    form.append("nomeCompleto", document.getElementById("nomeCompleto").value);
    form.append("chavePix", document.getElementById("chavePix").value);
    form.append("nick", document.getElementById("nick1").value);

    // opcionais
    const nick2 = document.getElementById("nick2")?.value;
    const nick3 = document.getElementById("nick3")?.value;
    const nick4 = document.getElementById("nick4")?.value;

    if (nick2) form.append("nick2", nick2);
    if (nick3) form.append("nick3", nick3);
    if (nick4) form.append("nick4", nick4);

    // 📎 arquivo
    const file = document.getElementById("comprovante").files[0];

    if (!file) {
      showToast("Selecione o comprovante", "warning");
      return;
    }

    // 🔥 valida tamanho (10MB)
    if (file.size > 10 * 1024 * 1024) {
      showToast("Arquivo muito grande (máx 10MB)", "error");
      return;
    }

    form.append("comprovante", file);

    const res = await fetch(API + "/salas/" + salaAtual + "/enviar-comprovante", {
      method: "POST",
      body: form
    });

    if (!res.ok) throw new Error("Erro no servidor");

    let data;
    try {
      data = await res.json();
    } catch {
      throw new Error("Resposta inválida da API");
    }

    if (data.ok) {
      showToast("Inscrição enviada!");
      fecharInscricao(); // ✅ corrigido
      carregarSalas();
    } else {
      showToast(data.msg || "Erro", "error");
    }

  } catch (err) {
    console.error(err);
    showToast(err.message || "Erro de conexão", "error");
  } finally {
    btn.innerHTML = original;
    btn.disabled = false;
  }
}




// ==========================
// CÓDIGO DA SALA
// ==========================
async function validarCodigoSala() {
  if (!precisaAPI()) return;
  
  const codigo = document.getElementById('codigoSala').value.trim();
  
  try {
    const res = await fetch(API + "/salas/" + salaCodigoAtual + "/validar-codigo", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ codigo })
    });
    
    const data = await res.json();
    
    const resposta = document.getElementById("respostaCodigo");
    if (data.ok) {
  showToast("Código válido!", "success");

  const salaId = data.config?.sala_id;
  const senha = data.config?.senha;

  resposta.innerHTML = `
    <div class="card" style="margin-top:15px;text-align:center">
      <h3>🎮 Dados da Sala</h3>
      
      <p><strong>ID da Sala:</strong></p>
      <div style="font-size:1.5rem;font-family:monospace">${salaId}</div>
      
      <p style="margin-top:10px"><strong>Senha:</strong></p>
      <div style="font-size:1.5rem;font-family:monospace">${senha}</div>
    </div>
  `;
}else {
      showToast("Código inválido", "error");
      
      resposta.innerHTML = `
        <div class="text-center" style="margin-top:10px;color:#ef4444">
          ❌ Código inválido
        </div>
      `;
    }
    
  } catch (err) {
    console.error(err);
    showToast("Erro de conexão", "error");
  }
}
// ==========================  
// MODAL CÓDIGO  
// ==========================  
function abrirModalCodigo(uid) {  
  salaCodigoAtual = uid;  
  document.getElementById('codigoSala').value = '';  
  document.getElementById('respostaCodigo').innerHTML = '';  
  document.getElementById('modalCodigo').classList.add('active');  
  document.getElementById('codigoSala').focus();  
}  

function fecharModalCodigo(e) {  
  if (!e || e.target === document.getElementById('modalCodigo')) {  
    document.getElementById('modalCodigo').classList.remove('active');  
  }  
}  
function fecharInscricao(e) {
  const modal = document.querySelector("#carregarModal .modal");

  if (!e || e.target === modal) {
    document.getElementById("carregarModal").innerHTML = "";

    salaAtual = null;
    codigoAtual = null;
  }
}