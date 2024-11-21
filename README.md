- 👋 Hi, I’m @TVRINGSPORTS
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
TVRINGSPORTS/TVRINGSPORTS is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulador de Controle de Acesso</title>
    <style>
        body { font-family: Arial, sans-serif; }
        #camara { width: 100%; height: auto; }
        #resultado { margin-top: 50px; padding: 120px; font-weight: bold; font-size: 2.55em; }
        .liberado { background-color: green; color: white; }
        .negado { background-color: red; color: white; }
    </style>
</head>
<body>
    <h1>Controle de Acesso via QR Code - ARENA NORDESTINA</h1>
    <video id="camara" autoplay playsinline></video> <!-- 'playsinline' para compatibilidade móvel -->
    <div id="resultado"></div>

    <script src="https://cdn.jsdelivr.net/npm/jsqr/dist/jsQR.js"></script>
    <script>
        async function iniciarCamera() {
            const video = document.getElementById('camara');
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" } });
                video.srcObject = stream;

                video.onloadedmetadata = () => {
                    video.play();
                    detectarQRCode(video);
                };
            } catch (error) {
                document.getElementById('resultado').textContent = 'Erro ao acessar a câmera. Verifique as permissões e o HTTPS.';
                console.error("Erro ao acessar a câmera:", error);
            }
        }

        function detectarQRCode(video) {
            const canvas = document.createElement('canvas');
            const context = canvas.getContext('2d');

            setInterval(() => {
                canvas.width = video.videoWidth;
                canvas.height = video.videoHeight;
                context.drawImage(video, 0, 0, canvas.width, canvas.height);

                const imagemData = context.getImageData(0, 0, canvas.width, canvas.height);
                const qrCode = jsQR(imagemData.data, canvas.width, canvas.height);

                if (qrCode) {
                    verificarAcesso(qrCode.data);
                } else {
                    verificarAcesso(null);
                }
            }, 100);
        }

        // Configuração dos 500 acessos
        const acessos = {
            "https://www.conselhointernacionaldeartesmarciais.com/tipo1": { nome: "Visitante", limite: 5 },
            "https://www.conselhointernacionaldeartesmarciais.com/tipo2": { nome: "Equipe Técnica", limite: 3 },
            "https://www.conselhointernacionaldeartesmarciais.com/tipo3": { nome: "Lutador", limite: 7 },
            "https://www.conselhointernacionaldeartesmarciais.com/tipo4": { nome: "VIP", limite: 10 },
            "https://www.conselhointernacionaldeartesmarciais.com/tipo5": { nome: "Patrocinador", limite: 4 },
            // Continue adicionando URLs até "tipo500" conforme necessário
            "https://www.conselhointernacionaldeartesmarciais.com/tipo500": { nome: "Acesso 500", limite: 10 }
        };

        // Inicializando contadores de acesso
        const contadores = {};
        for (const url in acessos) {
            contadores[url] = 0;
        }

        function verificarAcesso(url) {
            const resultadoDiv = document.getElementById('resultado');

            if (url === null) {
                resultadoDiv.textContent = 'Acesso Negado (QR Code não encontrado)';
                resultadoDiv.className = 'negado';
            } else if (acessos[url]) {
                const acesso = acessos[url];
                if (contadores[url] < acesso.limite) {
                    contadores[url]++;
                    resultadoDiv.textContent = `Acesso Liberado: ${acesso.nome}`;
                    resultadoDiv.className = 'liberado';
                } else {
                    resultadoDiv.textContent = `Acesso Esgotado para: ${acesso.nome}`;
                    resultadoDiv.className = 'negado';
                }
            } else {
                resultadoDiv.textContent = 'Acesso Negado (QR Code não reconhecido)';
                resultadoDiv.className = 'negado';
            }
        }

        window.onload = iniciarCamera;
    </script>
</body>
</html>

