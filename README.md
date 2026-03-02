// Substitua pelo seu link CSV da planilha (Arquivo > Compartilhar > Publicar na Web > CSV)
const csvUrl = 'https://docs.google.com/spreadsheets/d/1Ym-hTlTIDfiqxxo_PPiecmtSp0-4EshB02UHNsm_PWw/edit?usp=sharing';

async function fetchData() {
    Papa.parse(csvUrl, {
        download: true,
        header: true,
        complete: function(results) {
            const data = results.data;
            // Pega a última linha de dados da RB-JIPA
            const lastEntry = data[data.length - 1];
            
            if(lastEntry) {
                document.getElementById('cpu-val').innerText = lastEntry['CPU %'] + '%';
                document.getElementById('mem-val').innerText = lastEntry['Memória Livre (MB)'] + ' MB';
                document.getElementById('uptime-val').innerText = lastEntry['Uptime'];
                document.getElementById('active-val').innerText = lastEntry['Conexões Ativas'];
            }
        }
    });
}

// Inicia a atualização automática a cada 30 segundos
setInterval(fetchData, 30000);
fetchData();
