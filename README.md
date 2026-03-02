<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title>Monitoramento RB Jipa</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.3.0/papaparse.min.js"></script>
    <style>
        body { font-family: sans-serif; background: #121212; color: white; text-align: center; padding: 20px; }
        .grid { display: flex; justify-content: center; gap: 20px; margin-top: 20px; }
        .card { background: #1e1e1e; padding: 20px; border-radius: 10px; border-bottom: 4px solid #00d4ff; min-width: 150px; }
        h1 { color: #00d4ff; }
        p { font-size: 1.5rem; font-weight: bold; }
    </style>
</head>
<body>
    <h1>Monitoramento RB Jipa</h1>
    <div class="grid">
        <div class="card"><h3>CPU</h3><p id="cpu-val">--</p></div>
        <div class="card"><h3>Memória Livre</h3><p id="mem-val">--</p></div>
        <div class="card"><h3>Uptime</h3><p id="uptime-val" style="font-size: 1rem;">--</p></div>
        <div class="card"><h3>Ativos</h3><p id="active-val">--</p></div>
    </div>

    <script>
        // Link CSV da sua planilha (ajustado para exportação automática)
        const csvUrl = 'https://docs.google.com/spreadsheets/d/1Ym-hTITIDfiqxxo_PPiecmtSp0-4EshB02UHnsm_PWw/export?format=csv';

        async function fetchData() {
            Papa.parse(csvUrl, {
                download: true,
                header: true,
                complete: function(results) {
                    const data = results.data.filter(row => row.Hostname);
                    const lastEntry = data[data.length - 1];
                    
                    if (lastEntry) {
                        document.getElementById('cpu-val').innerText = lastEntry['CPU %'] + '%';
                        document.getElementById('mem-val').innerText = lastEntry['Memória Livre (MB)'] + ' MB';
                        document.getElementById('uptime-val').innerText = lastEntry['Uptime'];
                        document.getElementById('active-val').innerText = lastEntry['Conexões Ativas'];
                    }
                }
            });
        }

        setInterval(fetchData, 30000);
        fetchData();
    </script>
</body>
</html>
