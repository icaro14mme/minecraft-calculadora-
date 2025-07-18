# minecraft-calculadora-
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Calculadora de Dano & Eficiência – Minecraft</title>
  <style>
    body { font-family: Arial, sans-serif; background: #1e1e1e; color: white; padding: 20px; }
    h1 { text-align: center; color: #00ff99; }
    label, select, input, button { display: block; margin: 10px auto; max-width: 300px; }
    select, input { padding: 5px; width: 100%; }
    button { background-color: #00ff99; color: black; padding: 10px; border: none; cursor: pointer; }
    .result { text-align: center; margin-top: 20px; font-size: 1.2em; color: #00ff99; }
    .info { margin-top: 10px; font-size: 0.9em; color: #ccc; text-align: center; }
  </style>
</head>
<body>
  <h1>Calculadora – Minecraft</h1>

  <label for="versao">Versão do Minecraft:</label>
  <select id="versao" onchange="selecionarVersao()">
    <option value="">-- Escolha a Versão --</option>
    <option value="bedrock">Bedrock</option>
    <option value="java">Java</option>
  </select>

  <div id="selecaoPrincipal" style="display:none;">
    <label for="tipo">Tipo:</label>
    <select id="tipo" onchange="atualizarOpcoes()">
      <option value="espada">Espada</option>
      <option value="picareta">Picareta</option>
      <option value="pá">Pá</option>
      <option value="machado">Machado</option>
      <option value="armadura">Armadura</option>
    </select>

    <div id="armaduraExtra" style="display:none;">
      <label><input type="checkbox" id="armaduraCompleta" onchange="atualizarOpcoes()"> Armadura Completa</label>
      <div id="parteIndividual">
        <label for="parte">Parte da armadura:</label>
        <select id="parte">
          <option value="Capacete">Capacete</option>
          <option value="Casco de Tartaruga">Casco de Tartaruga</option>
          <option value="Peitoral">Peitoral</option>
          <option value="Calça">Calça</option>
          <option value="Bota">Bota</option>
        </select>
      </div>
    </div>

    <label for="item">Material:</label>
    <select id="item"></select>

    <label for="encantamento">Encantamento:</label>
    <select id="encantamento" onchange="mostrarDescricaoEncantamento()"></select>

    <label><input type="checkbox" id="critico"> Ataque Crítico (Arma)</label>

    <div class="info" id="descricaoEncantamento"></div>

    <button onclick="calcular()">Calcular</button>
    <div class="result" id="resultado"></div>
  </div>

  <script>
    const opcoes = {
      espada: { itens:["Madeira","Pedra","Ferro","Ouro","Diamante","Netherite"], encantamentos:["Afiação I","II","III","IV","V","Ruína dos Artropodes I-V","Aniquilação de Mortos‑Vivos I-V"] },
      machado: { itens:["Madeira","Pedra","Ferro","Ouro","Diamante","Netherite"], encantamentos:opcoes.espada.encantamentos },
      picareta: { itens:["Madeira","Pedra","Ferro","Ouro","Diamante","Netherite"], encantamentos:["Eficiência I-V","Fortuna I-III","Toque Suave I","Inquebrável I-III","Remendo I","Maldição do Elo I","Maldição do Desaparecimento I"] },
      pá: { itens:["Madeira","Pedra","Ferro","Ouro","Diamante","Netherite"], encantamentos:opcoes.picareta.encantamentos },
      armadura: { itens:["Couro","Ferro","Ouro","Diamante","Netherite"], encantamentos:["Proteção I-IV","Proteção Fogo I-IV","Explosões I-IV","Projéteis I-IV","Inquebrável I-III","Espinhos I-III","Queda Suave I-IV","Respiração I-III","Passos Profundos I-III","Passos Gelados I-II","Velocidade das Almas I-III","Sintonia Aquática I","Remendo I","Maldição do Elo I","Maldição do Desaparecimento I"] }
    };

    const descricoes = {
      Eficiência: "Aumenta a velocidade de mineração.",
      Fortuna: "Aumenta chance de drops extras.",
      Toque: "Minerar blocos inteiros (Ex: vidro).",
      Afiação: "Aumenta dano na arma.",
      Ruína: "Só contra artropodes.",
      Aniquilação: "Só contra mortos-vivos.",
      Proteção: "Reduz todo tipo de dano.",
      Espinhos: "Retribui dano ao atacante.",
      Remendo: "Repara item com XP.",
      Inquebrável:"Diminui desgaste.",
      Maldição: "Tem efeito negativo."
    };

    function selecionarVersao() {
      document.getElementById("selecaoPrincipal").style.display = document.getElementById("versao").value ? "block":"none";
      atualizarOpcoes();
    }

    function atualizarOpcoes(){
      const tipo=document.getElementById("tipo").value;
      const itemSelect=document.getElementById("item");
      const encSelect=document.getElementById("encantamento");
      const armExt=document.getElementById("armaduraExtra");
      const parte=document.getElementById("parteIndividual");

      itemSelect.innerHTML=""; encSelect.innerHTML="";
      armExt.style.display= tipo=="armadura"?"block":"none";
      parte.style.display=document.getElementById("armaduraCompleta").checked?"none":"block";

      opcoes[tipo].itens.forEach(it=>{
        itemSelect.innerHTML+=`<option>${it}</option>`;
      });
      opcoes[tipo].encantamentos.forEach(enc=>{
        encSelect.innerHTML+=`<option>${enc}</option>`;
      });
      mostrarDescricaoEncantamento();
    }

    function mostrarDescricaoEncantamento(){
      const s=document.getElementById("encantamento").value.split(" ")[0];
      document.getElementById("descricaoEncantamento").innerText = descricoes[s]||"";
    }

    function calcular(){
      const tipo=document.getElementById("tipo").value;
      const item=document.getElementById("item").value;
      const enc=document.getElementById("encantamento").value;
      const crit=document.getElementById("critico").checked;
      const armComp=document.getElementById("armaduraCompleta").checked;
      const parte=document.getElementById("parte")?.value||"";

      let msg="";
      if(tipo=="picareta"||tipo=="pá"){
        const lvl=+enc.split(" ")[1];
        msg=`🛠️ ${enc.split(" ")[0]} melhora muito. Eficiência ${lvl} ≈ x${(1+lvl*0.3).toFixed(2)} velocidade.`;
      } else if(tipo=="espada"||tipo=="machado"){
        // cálculo de dano
        msg="🔪 Dano calculado...";
      } else {
        msg="🛡️ Redução calculada...";
      }
      document.getElementById("resultado").innerText=msg;
    }
  </script>
</body>
</html>
