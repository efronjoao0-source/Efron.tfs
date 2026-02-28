<!DOCTYPE html>
<html lang="pt">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap');
        :root { --primary: #4A90E2; --bg: #F4F7FA; --surface: #FFFFFF; --text: #2D3436; --success: #00B894; --danger: #D63031; --warning: #FDCB6E; }
        body.dark-mode { --bg: #121212; --surface: #1E1E1E; --text: #FFFFFF; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        body { background-color: var(--bg); color: var(--text); font-family: 'Inter', sans-serif; margin: 0; padding: 20px; padding-bottom: 120px; transition: 0.4s; overflow-x: hidden; }
        .stats-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-bottom: 25px; animation: slideUp 0.5s ease; }
        .stat-box { background: var(--surface); padding: 20px; border-radius: 24px; box-shadow: 0 10px 20px rgba(0,0,0,0.03); text-align: center; }
        .stat-val { display: block; font-size: 22px; font-weight: 700; color: var(--primary); }
        .stat-lbl { font-size: 10px; text-transform: uppercase; color: #888; margin-top: 5px; font-weight: 600; }
        .glass-input { background: var(--surface); padding: 25px; border-radius: 28px; box-shadow: 0 15px 35px rgba(0,0,0,0.05); margin-bottom: 30px; animation: slideUp 0.7s ease; }
        input { width: 100%; background: #F8F9FB; border: 1px solid #EDF2F7; padding: 15px; border-radius: 12px; margin-bottom: 12px; box-sizing: border-box; font-family: 'Inter'; transition: 0.3s; color: inherit; }
        input:focus { border-color: var(--primary); outline: none; background: #FFF; }
        .btn-lancar { width: 100%; padding: 18px; background: var(--primary); color: white; border: none; border-radius: 15px; font-weight: 700; cursor: pointer; box-shadow: 0 8px 15px rgba(74, 144, 226, 0.3); }
        .task-card { background: var(--surface); padding: 20px; border-radius: 22px; margin-bottom: 15px; box-shadow: 0 5px 15px rgba(0,0,0,0.02); animation: slideUp 0.5s ease backwards; border: 1px solid rgba(0,0,0,0.02); }
        .t-head { display: flex; justify-content: space-between; align-items: center; }
        .t-name { font-weight: 700; font-size: 16px; }
        .t-cli { font-size: 12px; color: #999; margin-top: 3px; }
        .status-pill { display: inline-block; padding: 6px 14px; border-radius: 12px; font-size: 10px; font-weight: 700; margin-top: 12px; }
        .st-and { background: #E3F2FD; color: #1E88E5; }
        .st-ok { background: #E8F5E9; color: #2E7D32; }
        .st-fail { background: #FFEBEE; color: #C62828; }
        .action-grid { display: grid; grid-template-columns: 1fr 1fr 0.5fr; gap: 10px; margin-top: 15px; }
        .btn-act { padding: 12px; border: none; border-radius: 10px; font-size: 11px; font-weight: 700; cursor: pointer; transition: 0.2s; }
        .btn-act:active { transform: scale(0.95); }
        #configPage { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: var(--bg); z-index: 999; padding: 30px; box-sizing: border-box; overflow-y: auto; }
        .nav-bottom { position: fixed; bottom: 25px; left: 25px; right: 25px; background: rgba(255,255,255,0.85); backdrop-filter: blur(15px); height: 70px; border-radius: 25px; display: flex; justify-content: space-around; align-items: center; box-shadow: 0 15px 35px rgba(0,0,0,0.1); z-index: 1000; }
    </style>
</head>
<body id="mainBody">
    <div id="home">
        <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:25px;">
            <h2 style="margin:0; font-weight:700;">efRon <span style="color:var(--primary)">Tasks</span></h2>
            <div id="performance" style="font-size:10px; font-weight:700; color:var(--success); background:white; padding:5px 12px; border-radius:15px; box-shadow:0 4px 10px rgba(0,0,0,0.05);">TAXA: 0%</div>
        </div>
        <div class="stats-grid">
            <div class="stat-box"><span class="stat-val" id="totalMtn">0.00</span><span class="stat-lbl">Ganhos (MT)</span></div>
            <div class="stat-box"><span class="stat-val" id="activeCount" style="color:var(--warning)">0</span><span class="stat-lbl">Pendentes</span></div>
        </div>
        <div class="glass-input">
            <input type="text" id="cli" placeholder="Nome do Cliente">
            <input type="text" id="srv" placeholder="Tarefa (ex: Ativação)">
            <div style="display:flex; gap:10px;">
                <input type="number" id="prc" placeholder="Valor MT">
                <input type="number" id="tim" placeholder="Minutos">
            </div>
            <button class="btn-lancar" onclick="addTask()">Lançar no Sistema</button>
        </div>
        <div id="taskList"></div>
    </div>
    <div id="configPage">
        <h1 style="font-weight:700;">Ajustes</h1>
        <div style="background:var(--surface); padding:20px; border-radius:20px; margin-bottom:20px;">
            <p style="margin:0 0 15px 0; font-weight:600;">Personalização</p>
            <button onclick="setMode('light')" style="padding:10px; border-radius:8px; border:1px solid #ddd;">Modo Claro</button>
            <button onclick="setMode('dark')" style="padding:10px; border-radius:8px; border:1px solid #ddd; background:#333; color:white;">Modo Noite</button>
        </div>
        <div style="background:var(--surface); padding:20px; border-radius:20px; border: 1px solid rgba(214, 48, 49, 0.2);">
            <p style="color:var(--danger); font-weight:700;">Zona de Perigo</p>
            <button onclick="wipeData()" style="width:100%; background:var(--danger); color:white; border:none; padding:15px; border-radius:12px; font-weight:700;">LIMPAR TUDO</button>
        </div>
        <button class="btn-lancar" style="margin-top:30px; background:#636e72;" onclick="showConfig(false)">Voltar ao Início</button>
    </div>
    <div class="nav-bottom">
        <div onclick="showConfig(false)" id="nav-home" style="color:var(--primary); font-weight:700; cursor:pointer;">PAINEL</div>
        <div onclick="showConfig(true)" id="nav-cfg" style="color:#B2BEC3; font-weight:700; cursor:pointer;">AJUSTES</div>
    </div>
<script>
    let db = JSON.parse(localStorage.getItem('efron_final_v1') || '[]');
    let theme = localStorage.getItem('efron_theme') || 'light';
    function init() { setMode(theme); render(); }
    function setMode(m) { theme = m; localStorage.setItem('efron_theme', m); document.getElementById('mainBody').className = (m === 'dark' ? 'dark-mode' : ''); }
    function addTask() {
        const c = document.getElementById('cli').value, s = document.getElementById('srv').value, p = document.getElementById('prc').value, t = document.getElementById('tim').value;
        if(!c || !s || !p || !t) return alert("Preencha tudo, bebê!");
        db.unshift({ id: Date.now(), cliente: c, servico: s, preco: p, fim: Date.now() + (t * 60000), status: 'andamento' });
        save(); document.querySelectorAll('input').forEach(i => i.value = '');
    }
    function save() { localStorage.setItem('efron_final_v1', JSON.stringify(db)); render(); }
    function render() {
        const list = document.getElementById('taskList'); list.innerHTML = '';
        let total = 0, ativas = 0, concluidas = 0;
        db.forEach((t, i) => {
            if(t.status === 'executada') { total += parseFloat(t.preco); concluidas++; }
            if(t.status === 'andamento') ativas++;
            const card = document.createElement('div'); card.className = 'task-card'; card.style.animationDelay = (i * 0.1) + 's';
            card.innerHTML = `<div class="t-head"><div><div class="t-name">${t.servico}</div><div class="t-cli">👤 ${t.cliente}</div></div><div style="font-weight:700; color:var(--success);">${t.preco} MT</div></div>
                <div class="status-pill ${t.status === 'andamento' ? 'st-and' : (t.status === 'executada' ? 'st-ok' : 'st-fail')}">${t.status.toUpperCase()}</div>
                <div id="tmr-${t.id}" style="font-size:11px; margin-top:8px; font-weight:600; color:#888;"></div>
                <div class="action-grid">
                    <button class="btn-act" style="background:#E8F5E9; color:#2E7D32;" onclick="updateStatus(${t.id}, 'executada')">FEITO</button>
                    <button class="btn-act" style="background:#E3F2FD; color:#1565C0;" onclick="copyZap(${t.id})">COPIAR</button>
                    <button class="btn-act" style="background:#F5F5F5; color:#666;" onclick="deleteTask(${t.id})">🗑️</button>
                </div>`;
            list.appendChild(card);
        });
        document.getElementById('totalMtn').innerText = total.toFixed(2);
        document.getElementById('activeCount').innerText = ativas;
        document.getElementById('performance').innerText = `TAXA: ${db.length > 0 ? (concluidas / db.length * 100).toFixed(0) : 0}%`;
    }
    setInterval(() => {
        db.forEach(t => {
            if(t.status === 'andamento') {
                const rest = t.fim - Date.now();
                const el = document.getElementById('tmr-' + t.id);
                if(rest > 0) {
                    const m = Math.floor(rest/60000), s = Math.floor((rest%60000)/1000);
                    if(el) el.innerText = `⏳ Tempo: ${m}m ${s}s`;
                } else { t.status = 'falha'; save(); }
            }
        });
    }, 1000);
    function updateStatus(id, s) { db.find(x => x.id === id).status = s; save(); }
    function deleteTask(id) { if(confirm("Apagar?")) { db = db.filter(x => x.id !== id); save(); } }
    function wipeData() { if(confirm("Limpar tudo mesmo?")) { db = []; save(); } }
    function showConfig(s) { 
        document.getElementById('configPage').style.display = s ? 'block' : 'none';
        document.getElementById('nav-home').style.color = s ? '#B2BEC3' : 'var(--primary)';
        document.getElementById('nav-cfg').style.color = s ? 'var(--primary)' : '#B2BEC3';
    }
    function copyZap(id) {
        const t = db.find(x => x.id === id);
        const emoji = t.status === 'executada' ? '✅' : (t.status === 'andamento' ? '⚡' : '❌');
        
        // CORREÇÃO AQUI: Usando template literal com quebras reais para evitar o "\n"
        const msg = `╔══════════════════╗
║   💎 *efRon PREMIUM REPORT* ║
╚══════════════════╝

👤 *CLIENTE:* ${t.cliente.toUpperCase()}
🛠 *SERVIÇO:* ${t.servico}
💰 *VALOR:* ${t.preco} MT
🚦 *STATUS:* ${emoji} ${t.status.toUpperCase()}

━━━━━━━━━━━━━━━━━━━━
_Gerado por efron.IA.megic_`;

        const a = document.createElement('textarea');
        a.value = msg; 
        document.body.appendChild(a);
        a.select();
        document.execCommand('copy');
        document.body.removeChild(a);
        alert("Cópia de Elite Realizada! ✨");
    }
    init();
</script>
</body>
</html>
