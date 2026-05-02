<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>FuelTrack | Gestão de Combustível</title>
    <!-- Google Fonts & Font Awesome -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,300;14..32,400;14..32,600;14..32,700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <!-- jsPDF + autoTable (mais elegante para relatórios) -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.31/jspdf.plugin.autotable.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
        }

        body {
            background: radial-gradient(circle at 10% 20%, #0a0f1c, #03060c);
            padding: 2rem 1.5rem;
            min-height: 100vh;
        }

        /* container principal com efeito glassmorphism + neon sutil */
        .dashboard {
            max-width: 1300px;
            margin: 0 auto;
        }

        .glass-card {
            background: rgba(18, 25, 40, 0.75);
            backdrop-filter: blur(12px);
            border-radius: 2rem;
            border: 1px solid rgba(0, 255, 136, 0.25);
            box-shadow: 0 20px 35px -12px rgba(0, 0, 0, 0.5), 0 0 0 1px rgba(0, 255, 136, 0.1);
            transition: all 0.2s ease;
        }

        .header {
            text-align: center;
            margin-bottom: 2rem;
            padding: 1.2rem 0;
        }

        .header h1 {
            font-size: 2.4rem;
            font-weight: 700;
            background: linear-gradient(135deg, #E0FFEA, #00ff88);
            background-clip: text;
            -webkit-background-clip: text;
            color: transparent;
            letter-spacing: -0.5px;
            display: inline-flex;
            align-items: center;
            gap: 12px;
        }

        .header h1 i {
            background: none;
            color: #00ff88;
            font-size: 2rem;
            text-shadow: 0 0 8px #00ff8855;
        }

        .header p {
            color: #a0ecce;
            margin-top: 8px;
            font-weight: 400;
            font-size: 0.95rem;
            opacity: 0.8;
        }

        /* grid de formulário + estatísticas */
        .form-stats-grid {
            display: flex;
            flex-wrap: wrap;
            gap: 1.8rem;
            margin-bottom: 2.5rem;
        }

        .form-panel {
            flex: 2;
            min-width: 280px;
            padding: 1.8rem;
        }

        .stats-panel {
            flex: 1.2;
            min-width: 220px;
            padding: 1.5rem;
            display: flex;
            flex-direction: column;
            justify-content: center;
            gap: 1rem;
        }

        .stat-box {
            background: rgba(0, 0, 0, 0.5);
            border-radius: 1.5rem;
            padding: 1rem 1.2rem;
            border-left: 4px solid #00ff88;
        }

        .stat-box .stat-label {
            font-size: 0.8rem;
            text-transform: uppercase;
            letter-spacing: 1px;
            color: #8cf0bc;
            font-weight: 500;
        }

        .stat-box .stat-value {
            font-size: 2rem;
            font-weight: 800;
            color: #ffffff;
            line-height: 1.2;
            word-break: break-word;
        }

        .stat-value small {
            font-size: 0.9rem;
            font-weight: 400;
            color: #a0ffcc;
        }

        .form-group {
            margin-bottom: 1.4rem;
        }

        .form-group label {
            display: flex;
            align-items: center;
            gap: 8px;
            color: #ccffe6;
            font-weight: 500;
            margin-bottom: 8px;
            font-size: 0.85rem;
        }

        .form-group label i {
            width: 1.25rem;
            color: #00ff88;
        }

        input {
            width: 100%;
            padding: 12px 16px;
            background: #0a0f1c;
            border: 1.5px solid #2a3a4a;
            border-radius: 1rem;
            color: #eef5ff;
            font-size: 1rem;
            transition: all 0.2s;
            outline: none;
        }

        input:focus {
            border-color: #00ff88;
            box-shadow: 0 0 0 3px rgba(0, 255, 136, 0.2);
        }

        .button-group {
            display: flex;
            flex-wrap: wrap;
            gap: 12px;
            margin-top: 0.8rem;
        }

        .btn {
            flex: 1;
            padding: 12px 8px;
            border-radius: 2rem;
            font-weight: 600;
            border: none;
            cursor: pointer;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            font-size: 0.9rem;
            transition: all 0.2s ease;
            background: rgba(30, 45, 60, 0.8);
            color: #d0f0e0;
            border: 1px solid rgba(0, 255, 136, 0.3);
        }

        .btn-primary {
            background: #00ff88;
            color: #031009;
            box-shadow: 0 4px 12px rgba(0, 255, 136, 0.3);
            border: none;
        }

        .btn-primary:hover {
            background: #0cef88;
            transform: scale(1.01);
            box-shadow: 0 6px 16px rgba(0, 255, 136, 0.4);
        }

        .btn-danger {
            background: rgba(220, 60, 80, 0.85);
            color: white;
            border-color: #ff7780;
        }

        .btn-danger:hover {
            background: #d64555;
        }

        .btn:hover {
            transform: translateY(-2px);
        }

        /* tabela moderna */
        .table-wrapper {
            overflow-x: auto;
            border-radius: 1.5rem;
            background: rgba(10, 18, 28, 0.6);
            padding: 0.1rem;
        }

        .fuel-table {
            width: 100%;
            border-collapse: collapse;
            font-size: 0.85rem;
        }

        .fuel-table th {
            background: #0a1a1a;
            color: #00ffaa;
            padding: 1rem 0.8rem;
            font-weight: 600;
            text-align: center;
            border-bottom: 2px solid #00ff8855;
        }

        .fuel-table td {
            padding: 0.9rem 0.5rem;
            text-align: center;
            border-bottom: 1px solid rgba(0, 255, 136, 0.2);
            color: #daf6e8;
        }

        .fuel-table tr:hover td {
            background: rgba(0, 255, 136, 0.05);
        }

        .empty-row td {
            text-align: center;
            padding: 2rem;
            color: #7e9e8e;
            font-style: italic;
        }

        .total-footer {
            margin-top: 1rem;
            text-align: right;
            font-weight: 500;
            color: #b1ffe0;
            background: rgba(0, 0, 0, 0.4);
            padding: 0.7rem 1rem;
            border-radius: 1rem;
            display: inline-block;
            width: auto;
            float: right;
        }

        @media (max-width: 760px) {
            body { padding: 1rem; }
            .button-group { flex-direction: column; }
            .stat-value { font-size: 1.4rem; }
        }

        i.fa, i.far, i.fas {
            pointer-events: none;
        }

        .clear-icon {
            cursor: pointer;
        }
    </style>
</head>
<body>
<div class="dashboard">
    <div class="header">
        <h1><i class="fas fa-gas-pump"></i> FuelTrack <span style="font-weight:300;">|</span> Controle Inteligente</h1>
        <p>Registre abastecimentos, acompanhe litros totais e exporte relatórios PDF profissionais</p>
    </div>

    <div class="form-stats-grid">
        <div class="glass-card form-panel">
            <div style="display: flex; gap: 0.5rem; align-items: center; margin-bottom: 1rem;">
                <i class="fas fa-pen-alt" style="color:#00ff88"></i>
                <h3 style="font-weight:500;">Novo abastecimento</h3>
            </div>
            <div class="form-group">
                <label><i class="fas fa-truck"></i> Placa do veículo</label>
                <input type="text" id="placa" placeholder="Ex: ABC-1D23" autocomplete="off">
            </div>
            <div class="form-group">
                <label><i class="fas fa-tachometer-alt"></i> KM atual (odômetro)</label>
                <input type="number" id="km" placeholder="Quilometragem" step="1">
            </div>
            <div class="form-group">
                <label><i class="fas fa-fill-drip"></i> Bomba Inicial (litros)</label>
                <input type="number" id="bombaInicial" placeholder="0.00" step="any">
            </div>
            <div class="form-group">
                <label><i class="fas fa-fill"></i> Bomba Final (litros)</label>
                <input type="number" id="bombaFinal" placeholder="0.00" step="any">
            </div>
            <div class="button-group">
                <button class="btn btn-primary" id="btnSalvar"><i class="fas fa-save"></i> Salvar registro</button>
                <button class="btn" id="btnPDF"><i class="fas fa-file-pdf"></i> Relatório PDF</button>
                <button class="btn btn-danger" id="btnLimparTudo"><i class="fas fa-trash-alt"></i> Limpar dados</button>
            </div>
        </div>

        <div class="glass-card stats-panel">
            <div class="stat-box">
                <div class="stat-label"><i class="fas fa-chart-line"></i> Total de abastecimentos</div>
                <div class="stat-value" id="totalAbastecimentos">0</div>
            </div>
            <div class="stat-box">
                <div class="stat-label"><i class="fas fa-oil-can"></i> Litros totais</div>
                <div class="stat-value" id="totalLitros">0.00 <small>L</small></div>
            </div>
            <div class="stat-box">
                <div class="stat-label"><i class="fas fa-calendar-week"></i> Status</div>
                <div class="stat-value" id="statusMsg">Pronto</div>
            </div>
        </div>
    </div>

    <!-- tabela de registros -->
    <div class="glass-card" style="padding: 1rem; margin-top: 0.5rem;">
        <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 1rem; flex-wrap: wrap;">
            <h3 style="font-weight: 500;"><i class="fas fa-list-ul"></i> Histórico de abastecimentos</h3>
            <span style="font-size: 0.75rem; background: #11221f; padding: 6px 12px; border-radius: 40px;"><i class="fas fa-database"></i> Registros em memória</span>
        </div>
        <div class="table-wrapper">
            <table class="fuel-table" id="tabelaRegistros">
                <thead>
                    <tr><th>Placa</th><th>KM (km)</th><th>Bomba Inicial (L)</th><th>Bomba Final (L)</th><th>Litros (L)</th><th style="width: 50px;"></th></tr>
                </thead>
                <tbody id="tabelaBody">
                    <tr class="empty-row"><td colspan="6">Nenhum registro encontrado. Adicione um abastecimento.</td></tr>
                </tbody>
            </table>
        </div>
        <div id="totalRodape" style="margin-top: 1rem; text-align: right; font-size: 0.85rem;"></div>
    </div>
</div>

<script>
    // --- dados em memória
    let registros = [];

    // Elementos DOM
    const placaInput = document.getElementById('placa');
    const kmInput = document.getElementById('km');
    const inicialInput = document.getElementById('bombaInicial');
    const finalInput = document.getElementById('bombaFinal');
    const tbody = document.getElementById('tabelaBody');
    const totalAbastecimentosSpan = document.getElementById('totalAbastecimentos');
    const totalLitrosSpan = document.getElementById('totalLitros');
    const statusMsgSpan = document.getElementById('statusMsg');
    const totalRodape = document.getElementById('totalRodape');

    // Função para atualizar toda UI + estatísticas
    function atualizarInterface() {
        // atualizar tabela
        if (registros.length === 0) {
            tbody.innerHTML = '<tr class="empty-row"><td colspan="6"><i class="fas fa-gas-pump"></i> Nenhum registro. Adicione o primeiro abastecimento!</td></tr>';
            totalRodape.innerHTML = '';
        } else {
            let html = '';
            registros.forEach((reg, idx) => {
                html += `
                    <tr>
                        <td>${escapeHtml(reg.placa)}</td>
                        <td>${Number(reg.km).toLocaleString()}</td>
                        <td>${reg.inicial.toFixed(2)}</td>
                        <td>${reg.final.toFixed(2)}</td>
                        <td style="font-weight:600; color:#bbffdd;">${reg.litros.toFixed(2)}</td>
                        <td><button class="btn-remover" data-index="${idx}" style="background: none; border: none; color: #ff8877; font-size: 1.2rem; cursor:pointer;"><i class="fas fa-trash-alt"></i></button></td>
                    </tr>
                `;
            });
            tbody.innerHTML = html;
            // adicionar eventos de remoção
            document.querySelectorAll('.btn-remover').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    const idx = btn.getAttribute('data-index');
                    if (idx !== null) removerRegistro(parseInt(idx));
                });
            });
            const totalLitrosCalculado = registros.reduce((acc, r) => acc + r.litros, 0);
            totalRodape.innerHTML = `<i class="fas fa-charging-station"></i> <strong>Total abastecido:</strong> ${totalLitrosCalculado.toFixed(2)} litros  |  ${registros.length} registro(s)`;
        }

        // estatísticas
        const qtd = registros.length;
        totalAbastecimentosSpan.innerText = qtd;
        const somaLitros = registros.reduce((acc, r) => acc + r.litros, 0);
        totalLitrosSpan.innerHTML = `${somaLitros.toFixed(2)} <small>L</small>`;
        statusMsgSpan.innerText = qtd ? `${qtd} abastecimento(s) ativo` : "Aguardando dados";
    }

    // pequena função anti-XSS
    function escapeHtml(str) {
        if (!str) return '';
        return str.replace(/[&<>]/g, function(m) {
            if (m === '&') return '&amp;';
            if (m === '<') return '&lt;';
            if (m === '>') return '&gt;';
            return m;
        });
    }

    // adicionar registro com validação
    function adicionarRegistro() {
        const placa = placaInput.value.trim();
        const kmRaw = kmInput.value.trim();
        const inicialRaw = inicialInput.value.trim();
        const finalRaw = finalInput.value.trim();

        if (!placa) {
            statusMsgSpan.innerText = "❌ Informe a placa";
            setTimeout(() => atualizarInterface(), 1500);
            return;
        }
        if (!kmRaw || isNaN(Number(kmRaw)) || Number(kmRaw) <= 0) {
            statusMsgSpan.innerText = "❌ KM inválido";
            return;
        }
        if (inicialRaw === '' || finalRaw === '' || isNaN(parseFloat(inicialRaw)) || isNaN(parseFloat(finalRaw))) {
            statusMsgSpan.innerText = "❌ Preencha os valores da bomba";
            return;
        }

        let inicial = parseFloat(inicialRaw);
        let final = parseFloat(finalRaw);

        if (inicial < 0 || final < 0) {
            statusMsgSpan.innerText = "❌ Valores não podem ser negativos";
            return;
        }
        if (final <= inicial) {
            statusMsgSpan.innerText = "❌ Bomba final deve ser maior que inicial";
            return;
        }

        const litros = final - inicial;
        if (litros <= 0 || litros > 500) {
            statusMsgSpan.innerText = "⚠️ Litros inválidos (máx 500L)";
            return;
        }

        const kmNum = parseFloat(kmRaw);

        const novoRegistro = {
            placa: placa,
            km: kmNum,
            inicial: inicial,
            final: final,
            litros: litros,
            timestamp: new Date().toISOString()
        };
        registros.push(novoRegistro);
        // limpar campos
        placaInput.value = '';
        kmInput.value = '';
        inicialInput.value = '';
        finalInput.value = '';
        statusMsgSpan.innerText = "✅ Registro adicionado!";
        atualizarInterface();
        setTimeout(() => {
            if (registros.length) statusMsgSpan.innerText = "Pronto";
            else statusMsgSpan.innerText = "Pronto";
        }, 1800);
    }

    function removerRegistro(index) {
        if (index >= 0 && index < registros.length) {
            registros.splice(index, 1);
            statusMsgSpan.innerText = "🗑️ Registro removido";
            atualizarInterface();
            setTimeout(() => { if(statusMsgSpan.innerText.includes("removido")) statusMsgSpan.innerText = "Pronto"; }, 1500);
        }
    }

    function limparTodosRegistros() {
        if (registros.length === 0) {
            statusMsgSpan.innerText = "ℹ️ Nada para limpar";
            return;
        }
        if (confirm("Deseja realmente apagar TODOS os registros de abastecimento?")) {
            registros = [];
            atualizarInterface();
            statusMsgSpan.innerText = "🧹 Todos os dados foram removidos";
            setTimeout(() => statusMsgSpan.innerText = "Pronto", 2000);
        }
    }

    // --- Geração de PDF moderno com autoTable (jspdf + plugin)
    async function gerarPDFProfissional() {
        if (registros.length === 0) {
            alert("Não há registros para gerar o relatório. Adicione alguns abastecimentos.");
            return;
        }

        // usando jsPDF + autoTable já carregados
        const { jsPDF } = window.jspdf;
        const doc = new jsPDF({ orientation: 'portrait', unit: 'mm', format: 'a4' });

        // Cabeçalho visual com gradiente simulado
        doc.setFillColor(10, 25, 35);
        doc.rect(0, 0, 210, 45, 'F');
        doc.setTextColor(0, 255, 136);
        doc.setFontSize(22);
        doc.setFont("helvetica", "bold");
        doc.text("RELATÓRIO DE CONSUMO - FUELTRACK", 20, 18);
        doc.setFontSize(10);
        doc.setTextColor(180, 240, 210);
        doc.text("Sistema de controle de abastecimento | Dados precisos de bomba", 20, 28);
        
        // Data e hora do relatório
        const now = new Date();
        const dataHora = now.toLocaleString('pt-BR', { dateStyle: 'full', timeStyle: 'short' });
        doc.setFontSize(9);
        doc.setTextColor(170, 220, 200);
        doc.text(`Gerado em: ${dataHora}`, 150, 38);
        
        // resumo estatístico
        const totalLitrosRel = registros.reduce((acc, r) => acc + r.litros, 0);
        const mediaLitros = (totalLitrosRel / registros.length).toFixed(2);
        
        doc.setFontSize(11);
        doc.setTextColor(210, 255, 230);
        doc.text(`📊 Resumo: ${registros.length} abastecimento(s) | Total: ${totalLitrosRel.toFixed(2)} L | Média: ${mediaLitros} L/abastecimento`, 20, 50);
        
        // preparar dados para tabela
        const tableData = registros.map((reg, idx) => [
            idx + 1,
            reg.placa,
            reg.km.toLocaleString(),
            reg.inicial.toFixed(2) + ' L',
            reg.final.toFixed(2) + ' L',
            reg.litros.toFixed(2) + ' L'
        ]);
        
        // configuração da tabela bonita
        doc.autoTable({
            startY: 58,
            head: [['#', 'Placa', 'KM', 'Bomba Inicial', 'Bomba Final', 'Litros']],
            body: tableData,
            theme: 'grid',
            headStyles: {
                fillColor: [0, 80, 60],
                textColor: [220, 255, 220],
                fontStyle: 'bold',
                halign: 'center',
                fontSize: 10,
                lineWidth: 0.1,
                lineColor: [0, 200, 130]
            },
            bodyStyles: {
                textColor: [210, 240, 230],
                fillColor: [18, 25, 38],
                lineColor: [40, 80, 70],
                fontSize: 9,
                halign: 'center'
            },
            alternateRowStyles: {
                fillColor: [12, 20, 30]
            },
            margin: { left: 15, right: 15 },
            columnStyles: {
                0: { cellWidth: 12, halign: 'center' },
                1: { cellWidth: 30, halign: 'center' },
                2: { cellWidth: 28, halign: 'center' },
                3: { cellWidth: 32, halign: 'center' },
                4: { cellWidth: 32, halign: 'center' },
                5: { cellWidth: 30, halign: 'center', fontStyle: 'bold', textColor: [100, 255, 180] }
            },
            didDrawPage: function(data) {
                // rodapé personalizado em cada página
                const pageCount = doc.internal.getNumberOfPages();
                doc.setFontSize(8);
                doc.setTextColor(100, 150, 120);
                doc.text(`FuelTrack • Página ${data.pageNumber} de ${pageCount}`, doc.internal.pageSize.getWidth() / 2, doc.internal.pageSize.getHeight() - 8, { align: 'center' });
            }
        });
        
        // página extra com detalhes finais e mensagem
        const finalY = doc.lastAutoTable.finalY + 10;
        if (finalY < 260) {
            doc.setFontSize(9);
            doc.setTextColor(70, 200, 150);
            doc.text("✔️ Relatório emitido pelo sistema FuelTrack – Controle eficiente de combustível.", 20, finalY + 6);
            doc.text("🔧 Dica: mantenha sempre os registros de bomba inicial/final para maior precisão.", 20, finalY + 14);
        } else {
            doc.addPage();
            doc.setFontSize(9);
            doc.setTextColor(70, 200, 150);
            doc.text("✔️ Relatório finalizado. Salve o arquivo para seu histórico de frotas.", 20, 30);
        }
        
        // Nome do arquivo
        doc.save(`relatorio_combustivel_${now.toISOString().slice(0,19).replace(/:/g, '-')}.pdf`);
        statusMsgSpan.innerText = "📄 PDF gerado com sucesso!";
        setTimeout(() => statusMsgSpan.innerText = "Pronto", 2000);
    }

    // Inicializar eventos
    document.getElementById('btnSalvar').addEventListener('click', adicionarRegistro);
    document.getElementById('btnPDF').addEventListener('click', gerarPDFProfissional);
    document.getElementById('btnLimparTudo').addEventListener('click', limparTodosRegistros);
    
    // extra: permitir enter no campo final
    const inputs = [placaInput, kmInput, inicialInput, finalInput];
    inputs.forEach(inp => inp.addEventListener('keypress', (e) => {
        if (e.key === 'Enter') adicionarRegistro();
    }));
    
    // Inicializa interface
    atualizarInterface();
</script>
</body>
</html>
