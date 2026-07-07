Transmite a câmera do celular pro PC via WebRTC (P2P), sem precisar digitar PIN nem depender de app de terceiros. Feito pra rodar direto no GitHub Pages.
O vídeo trafega direto entre os dois aparelhos (peer-to-peer). O único uso de internet é uma troca curta de "apresentação" entre os dois no início (sinalização), feita através do servidor público do PeerJS.

Como usar:
Acesse a página no celular e clique em "📱 Sou o Celular (Transmissor)".
O navegador vai pedir permissão de câmera — aceite.
Aponte a câmera frontal (selfie) pro que você quer transmitir.

Acesse a mesma página no PC (mesmo Wi-Fi/rede) e clique em "🖥️ Sou o PC (Receptor)".
O PC procura a câmera automaticamente, sem precisar copiar/colar nenhum código.
Adicione essa página como fonte de Navegador (Browser Source) no OBS, se quiser usar o vídeo recebido no PC como overlay/fonte de câmera na live.


Status na tela
No canto superior aparece um dos três estados:
CorTextoSignificado🟢 VerdeConectadoVídeo fluindo normalmente🟡 AmareloAguardando SinalConectando, procurando o outro aparelho, ou reconectando🔴 VermelhoFalha de ConexãoErro real — veja o console (F12) pra detalhes
Detalhes técnicos completos de cada evento (erro exato, estado do ICE, etc.) vão pro console do navegador (F12 → aba Console), mesmo quando a tela mostra só o texto simples.

Configuração
Tudo fica em um único arquivo (index.html). O único ajuste que você provavelmente vai querer mudar é o ID fixo da câmera, perto do topo do <script>:
jsconst CAMERA_ID = 'armster91-obs-cam';
Esse ID identifica seu celular no servidor de sinalização do PeerJS. Não precisa mudar pra funcionar, mas se um dia usar mais de uma câmera, duplique o arquivo e dê um ID diferente pra cada uma (ex: armster91-obs-cam-2).

Requisitos
HTTPS (GitHub Pages já serve assim — necessário pro navegador liberar a câmera).
Internet no momento de conectar, mesmo estando na mesma rede local — o PeerJS público (0.peerjs.com) precisa ser alcançado pra fazer a apresentação inicial entre os dois aparelhos. Depois de conectado, o vídeo em si não depende mais de internet.
Celular e PC na mesma rede Wi-Fi (recomendado, mas tecnicamente funciona em redes diferentes também, já que usa STUN).

Resolvendo problemas
Fica em "Aguardando Sinal" por muito tempo
O servidor público do PeerJS às vezes demora ou falha — é uma limitação conhecida do serviço gratuito deles. Espere um pouco; se não resolver, recarregue as duas páginas (primeiro o celular, depois o PC).

"Falha de Conexão" logo ao abrir
Abra o console (F12) pra ver o erro exato. Os casos mais comuns:
unavailable-id: já tem uma aba/sessão antiga do celular ainda registrada com esse ID no servidor. Feche todas as outras abas/telas do celular e recarregue.
network / server-error: sem internet no momento — confira a conexão do celular ou do PC.

Câmera conecta mas a imagem não aparece no PC
Confirme que o celular está com a tela ligada e a aba em primeiro plano — alguns navegadores mobile pausam a câmera quando a tela é bloqueada ou a aba vai pra segundo plano.

Imagem espelhada
Só o preview local no celular é espelhado (efeito selfie). O que o PC recebe já vem sem espelhamento. Se estiver invertido no PC, é sinal de bug — avise.

Como funciona por baixo dos panos
PeerJS abstrai a API nativa de WebRTC do navegador.
O celular abre uma conexão (Peer) com um ID fixo e fica esperando ligações (peer.on('call')).
O PC abre uma conexão sem ID fixo e liga automaticamente pro ID fixo do celular (peer.call(...)), tentando de novo a cada poucos segundos até conseguir.
Quando a ligação conecta, o vídeo passa a fluir direto entre os dois aparelhos — o servidor de sinalização não participa mais dali em diante.
O app monitora o estado da conexão ICE (oniceconnectionstatechange) pra detectar quedas e reconectar automaticamente.

Acesse através de: https://armster1991.github.io/digitalcam/
