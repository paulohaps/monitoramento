<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Monitoramento RB Jipa | Operacional</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.3.0/papaparse.min.js"></script>
    <style>
        body { font-family: 'Inter', sans-serif; background: #121212; color: #e0e0e0; margin: 0; padding: 20px; }
        .container { max-width: 1100px; margin: auto; }
        h1 { color: #00d4ff; text-align: center; text-transform: uppercase; letter-spacing: 2px; }
        .grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 20px; margin-bottom: 30px; }
        .card { background: #1e1e1e; padding: 20px; border-radius: 12px; text-align: center; border-bottom: 4px solid #00d4ff; }
        .card h3 { margin: 0; font-size: 0.9rem; color: #aaa; }
        .card p { font-size: 1.8rem; font-weight: bold; margin: 10px 0 0; }
        .chart-container { background: #1e1e1e; padding: 20px; border-radius: 12px; height: 400px; }
    </style>
</head>
<body>

<div class="container">
    <h1>Dashboard Operacional - RB Jipa</h1>
    
    <div class="grid">
        <div class="card">
            <h3>CPU Load</h3>
            <p id="cpu-val">--%</p>
        </div>
        <div class="card">
            <h3>Memória Livre</h3>
            <p id="mem-val">-- MB</p>
        </div>
        <div class="card">
            <h3>Uptime</h3>
            <p id="uptime-val" style="font-size: 1.2rem;">--</p>
        </div>
        <div class="card">
            <h3>Conexões Ativas</h3>
            <p id="active-val">--</p>
        </div>
    </div>

    <div class="chart-container">
        <canvas id="historyChart"></canvas>
    </div>
</div>

<script>
    // 1. COLE AQUI O LINK DO CSV (Arquivo > Compartilhar > Publicar na Web > CSV)
    const csvUrl = 'INSIRA_AQUI_O_LINK_DO_SEU_CSV_DA_PLANILHA';

    async function updateDashboard() {
        Papa.parse(csvUrl, {
            download: true,
            header: true,
            complete: function(results) {
                const data = results.data.filter(row => row.Hostname); // Filtra linhas vazias
                if (data.length === 0) return;

                const last = data[data.length - 1];

                // Atualizando os Cards
                document.getElementById('cpu-val').innerText = last['CPU %'] + '%';
                document.getElementById('mem-val').innerText = last['Memória Livre (MB)'] + ' MB';
                document.getElementById('uptime-val').innerText = last['Uptime'];
                document.getElementById('active-val').innerText = last['Conexões Ativas'] || '0';

                // Preparando o Gráfico (Últimas 20 leituras)
                const history = data.slice(-20);
                const labels = history.map(h => h['Data/Hora'].split(' ')[1]); // Pega apenas a hora
                const cpuPoints = history.map(h => parseFloat(h['CPU %']));

                renderChart(labels, cpuPoints);
            }
        });
    }

    let chartInstance;
    function renderChart(labels, values) {
        const ctx = document.getElementById('historyChart').getContext('2d');
        if (chartInstance) chartInstance.destroy();

        chartInstance = new Chart(ctx, {
            type: 'line',
            data: {
                labels: labels,
                datasets: [{
                    label: 'Uso de CPU (%)',
                    data: values,
                    borderColor: '#00d4ff',
                    backgroundColor: 'rgba(0, 212, 255, 0.1)',
                    fill: true,
                    tension: 0.3
                }]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                scales: {
                    y: { beginAtZero: true, max: 100, grid: { color: '#333' } },
                    x: { grid: { display: false } }
                },
                plugins: { legend: { labels: { color: '#fff' } } }
            }
        });
    }

    // Atualiza o dashboard a cada 30 segundos
    updateDashboard();
    setInterval(updateDashboard, 30000);
</script>

</body>
</html>
