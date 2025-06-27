# jogo-da-cobrinha

<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Jogo da Cobrinha</title>
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    body {
      background-color: #111;
      color: white;
      font-family: sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-start;
      height: 100vh;
      overflow: hidden;
    }

    h1 {
      margin: 20px 0 10px;
    }

    canvas {
      background-color: black;
      border: 5px solid green;
      display: block;
    }

    #painel-controle {
      margin: 10px 0;
    }

    select, button {
      padding: 4px 8px;
      margin: 0 5px;
    }
  </style>
</head>
<body>
  <h1>Jogo da Cobrinha</h1>
  <canvas id="tela" width="400" height="400">Seu navegador não suporta Canvas.</canvas>

  <div id="painel-controle">
    <label for="dificuldade">Dificuldade:</label>
    <select id="dificuldade">
      <option value="facil">Fácil</option>
      <option value="medio" selected>Médio</option>
      <option value="dificil">Difícil</option>
    </select>
    <button onclick="iniciarJogo()">Iniciar Jogo</button>
    <p>Pontuação: <span id="pontuacao">0</span></p>
  </div>

  <script>
    const tela = document.getElementById("tela");
    const contexto = tela.getContext("2d");
    const tamanhoBloco = 20;
    const totalBlocos = tela.width / tamanhoBloco;

    let cobrinha = [{ x: 5, y: 5 }];
    let direcao = "direita";
    let comida = gerarComida();
    let pontuacao = 0;
    let intervaloJogo;

    document.addEventListener("keydown", function (evento) {
      if (evento.key === "ArrowUp" && direcao !== "baixo") direcao = "cima";
      if (evento.key === "ArrowDown" && direcao !== "cima") direcao = "baixo";
      if (evento.key === "ArrowLeft" && direcao !== "direita") direcao = "esquerda";
      if (evento.key === "ArrowRight" && direcao !== "esquerda") direcao = "direita";
    });

    function iniciarJogo() {
      cobrinha = [{ x: 5, y: 5 }];
      direcao = "direita";
      comida = gerarComida();
      pontuacao = 0;
      atualizarPontuacao();

      let dificuldade = document.getElementById("dificuldade").value;
      let velocidade = { facil: 200, medio: 120, dificil: 70 }[dificuldade];

      clearInterval(intervaloJogo);
      intervaloJogo = setInterval(() => {
        desenharCenario();
        moverCobrinha();
        desenharCobrinha();
        desenharComida();
        verificarComida();
        verificarColisoes();
      }, velocidade);
    }

    function desenharCenario() {
      contexto.fillStyle = "black";
      contexto.fillRect(0, 0, tela.width, tela.height);
    }

    function desenharCobrinha() {
      for (let parte of cobrinha) {
        contexto.fillStyle = "lime";
        contexto.fillRect(parte.x * tamanhoBloco, parte.y * tamanhoBloco, tamanhoBloco, tamanhoBloco);
      }
    }

    function moverCobrinha() {
      let cabeca = { ...cobrinha[0] };

      if (direcao === "direita") cabeca.x++;
      if (direcao === "esquerda") cabeca.x--;
      if (direcao === "cima") cabeca.y--;
      if (direcao === "baixo") cabeca.y++;

      cobrinha.unshift(cabeca);
      cobrinha.pop();
    }

    function gerarComida() {
      return {
        x: Math.floor(Math.random() * totalBlocos),
        y: Math.floor(Math.random() * totalBlocos)
      };
    }

    function desenharComida() {
      contexto.fillStyle = "red";
      contexto.fillRect(comida.x * tamanhoBloco, comida.y * tamanhoBloco, tamanhoBloco, tamanhoBloco);
    }

    function verificarComida() {
      if (cobrinha[0].x === comida.x && cobrinha[0].y === comida.y) {
        comida = gerarComida();
        cobrinha.push({});
        pontuacao++;
        atualizarPontuacao();
      }
    }

    function atualizarPontuacao() {
      document.getElementById("pontuacao").innerText = pontuacao;
    }

    function verificarColisoes() {
      let cabeca = cobrinha[0];

      if (
        cabeca.x < 0 || cabeca.x >= totalBlocos ||
        cabeca.y < 0 || cabeca.y >= totalBlocos
      ) {
        gameOver();
      }

      for (let i = 1; i < cobrinha.length; i++) {
        if (cabeca.x === cobrinha[i].x && cabeca.y === cobrinha[i].y) {
          gameOver();
        }
      }
    }

    function gameOver() {
      clearInterval(intervaloJogo);
      alert("Fim de jogo! Sua pontuação: " + pontuacao);
    }
  </script>
</body>
</html>
