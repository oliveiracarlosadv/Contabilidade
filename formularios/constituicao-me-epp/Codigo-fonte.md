# Código-fonte: Formulário Sara Oliveira, Constituição Empresarial (ME e EPP)

Arquivo: Sara Oliveira - Constituição Empresarial.html (no repositório GitHub: formularios/constituicao-me-epp/index.html)
Versão: 9 (26/11/2026)
Tecnologia: HTML, CSS e JavaScript em arquivo único, sem conexão com a internet. Codificação UTF-8. Biblioteca incorporada: PDF.js 3.11.174 (Mozilla, licença Apache 2.0), usada para converter os anexos em PDF em páginas de imagem.

## Estrutura
O arquivo reúne a tela de apresentação (splash), o estilo (bloco style, com regras de tela, de responsividade em 900 px e 600 px e de impressão em A4 com margens 3/2/2/3 cm), o formulário (seções I a V, declaração e assinaturas), o contêiner das páginas anexas (anexosImpressao), o modelo de bloco de sócio (elemento template), dois blocos script com a biblioteca PDF.js e o script do formulário.

Principais funções: adicionarSocio, removerSocio e renumerar (gestão dos sócios); alternarRegime (regime de bens para sócio casado); coletar e aplicar (leitura e preenchimento dos campos); salvar (apenas registra que houve alteração, para o aviso ao fechar a página; nenhum dado é gravado no navegador, e a chave antiga checklist_constituicao_me_epp_v1 é apagada ao abrir); iniciar (exibe a tela de apresentação, ou splash); nomeArquivo e validarResponsavel (nome padrão "dd-mm-aaaa Primeiro Último"); salvarArquivo e carregarArquivo (.json, incluindo os anexos); validarPrimeiroSocio; prepararImpressao, limparImpressao e encerrarImpressao (na impressão, cada campo é substituído por um bloco de texto com quebra de linha automática, para que nenhuma palavra seja cortada); adicionarArquivos, pdfParaPaginas, imagemParaPagina, renderizarListaAnexos e montarAnexosImpressao (anexos: cada página de PDF e cada imagem é convertida em JPEG e impressa em página própria ao final do formulário, com legenda); exportarPDF (valida, aplica o nome padrão, monta a impressão e aguarda o carregamento das imagens antes de abrir a janela de impressão); mascaraCPF, mascaraFone e mascaraMoeda.

## Código completo
No código abaixo, os dois blocos script marcados com /*PDFJS_LIB*/ e /*PDFJS_WORKER*/ correspondem, no arquivo em uso, ao conteúdo integral de pdf.min.js e pdf.worker.min.js do pacote pdfjs-dist 3.11.174 (cerca de 1,4 MB), omitidos aqui por tamanho.

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Checklist para Constituição de Empresa ME e EPP</title>
<style>
  :root{
    --verde:#233310; --verde2:#2F4F4F; --folha:#556B2F;
    --fundo:#F4F5F1; --linha:#C9CEC0; --texto:#1E1E1E;
  }
  *{box-sizing:border-box}
  body{margin:0;background:var(--fundo);font-family:Arial, Helvetica, sans-serif;font-size:12pt;color:var(--texto)}
  .barra{position:sticky;top:0;z-index:10;background:var(--verde);padding:10px 16px;display:flex;flex-wrap:wrap;gap:8px;justify-content:center}
  .barra a{font-family:inherit;font-size:11pt;border:1px solid #fff;color:#fff;padding:7px 14px;border-radius:4px;text-decoration:none}
  .barra button,.barra label{font-family:inherit;font-size:11pt;border:1px solid #fff;background:transparent;color:#fff;padding:7px 14px;border-radius:4px;cursor:pointer}
  .barra button.principal{background:#fff;color:var(--verde)}
  .barra button:hover,.barra label:hover{opacity:.85}
  .folha{max-width:820px;margin:22px auto;background:#fff;padding:36px 44px;border-radius:6px;box-shadow:0 1px 4px rgba(0,0,0,.08)}
  header{text-align:justify;border-bottom:2px solid var(--verde);padding-bottom:14px;margin-bottom:22px}
  h1{font-size:15pt;font-weight:normal;color:var(--verde);margin:12px 0 4px;letter-spacing:.5px;text-transform:uppercase}
  .responsavel-form{font-size:13pt;color:var(--verde);letter-spacing:.3px}
  .responsavel-form span{font-size:11pt;color:var(--folha)}
  .coordenadora{font-size:11.5pt;color:var(--folha);letter-spacing:.3px}
  h2{break-after:avoid;page-break-after:avoid;font-size:12.5pt;font-weight:normal;color:#fff;background:var(--verde2);padding:7px 12px;margin:26px 0 14px;border-radius:3px;text-transform:uppercase;letter-spacing:.4px}
  h3{font-size:12pt;font-weight:normal;color:var(--folha);margin:6px 0 12px;border-bottom:1px solid var(--linha);padding-bottom:4px;display:flex;justify-content:space-between;align-items:center}
  .grade{display:grid;grid-template-columns:repeat(6,1fr);gap:12px 14px}
  .c2{grid-column:span 2}.c3{grid-column:span 3}.c4{grid-column:span 4}.c6{grid-column:span 6}
  .campo label{display:block;font-size:10pt;color:#444;margin-bottom:3px}
  .campo input,.campo select,.campo textarea{width:100%;font-family:inherit;font-size:11.5pt;padding:6px 8px;border:1px solid var(--linha);border-radius:3px;background:#FCFCFA;color:var(--texto)}
  .campo textarea{resize:vertical;min-height:58px;line-height:1.4}
  .campo input:focus,.campo select:focus,.campo textarea:focus{outline:none;border-color:var(--folha);background:#fff}
  .num{color:var(--folha);margin-right:4px}
  .socio{border:1px solid var(--linha);border-radius:4px;padding:14px 16px 16px;margin-bottom:16px}
  .remover{font-family:inherit;font-size:10pt;background:none;border:1px solid #A33;color:#A33;padding:3px 10px;border-radius:3px;cursor:pointer}
  .add{font-family:inherit;font-size:11pt;background:var(--folha);color:#fff;border:none;padding:8px 16px;border-radius:4px;cursor:pointer}
  .regime.oculto{display:none}
  .socio:first-child .remover{display:none}
  .socio:first-child .campo label:not(.opc)::after{content:" *";color:#A33}
  label.req::after{content:" *";color:#A33}
  .erro{border-color:#A33 !important;background:#FFF4F4 !important}
  .obrig{font-size:10pt;color:#555;margin:-4px 0 12px}
  @media print{.socio:first-child .campo label:not(.opc)::after,label.req::after{content:""}}
  .declaracao{margin-top:28px;font-size:11pt;text-align:justify;line-height:1.5}
  .assinatura{margin-top:110px;display:grid;grid-template-columns:1fr 1fr;gap:40px;text-align:justify;font-size:10.5pt}
  .assinatura div{border-top:1px solid #333;padding-top:5px}
  .aviso{max-width:820px;margin:0 auto 24px;font-size:10pt;color:#555;text-align:justify}
  @media (max-width:900px){.folha{margin:14px 12px;padding:28px 24px}.grade{grid-template-columns:repeat(2,1fr)}.c2,.c3{grid-column:span 1}.c4,.c6{grid-column:span 2}}
  @media (max-width:600px){
    body{font-size:11pt}
    .folha{margin:10px 6px;padding:18px 14px}
    .grade{grid-template-columns:1fr}.c2,.c3,.c4,.c6{grid-column:span 1}
    .campo input,.campo select,.campo textarea{font-size:16px}
    .barra{padding:8px}.barra button,.barra label,.barra a{flex:1 1 45%;text-align:center;padding:9px 6px}
    .assinatura{grid-template-columns:1fr;gap:100px;margin-top:100px}
    h1{font-size:13pt}.add{width:100%}
  }
  .folha,.folha p,.folha label,.folha h1,.folha h2,.folha h3,.folha li,.folha div{text-align:justify}
  .vi{display:none;text-align:justify}
  body.com-splash{overflow:hidden}
  .splash{position:fixed;inset:0;z-index:100;background:rgba(20,30,10,.72);display:flex;align-items:center;justify-content:center;padding:16px;overflow-y:auto}
  .splash.fechado{display:none}
  .splash-caixa{background:#fff;max-width:640px;width:100%;border-radius:8px;padding:30px 34px;box-shadow:0 10px 40px rgba(0,0,0,.3);text-align:justify;max-height:calc(100vh - 32px);overflow-y:auto}
  .splash-topo{font-size:11pt;color:var(--folha);letter-spacing:.5px;text-align:justify}
  .splash h2{background:none;color:var(--verde);padding:0;margin:6px 0 14px;font-size:14pt;text-align:justify;letter-spacing:.3px}
  .splash h4{font-weight:normal;color:var(--verde2);font-size:11.5pt;margin:14px 0 4px;text-transform:uppercase;letter-spacing:.4px;text-align:justify}
  .splash p{font-size:11pt;line-height:1.5;margin:0 0 6px;text-align:justify;color:#333}
  .splash .creditos{margin-top:16px;padding-top:10px;border-top:1px solid var(--linha);font-size:10pt;color:#555}
  .splash-acoes{display:flex;flex-wrap:wrap;gap:10px;margin-top:16px}
  .splash-acoes button{font-family:inherit;font-size:11.5pt;background:var(--folha);color:#fff;border:none;padding:10px 20px;border-radius:4px;cursor:pointer}
  .splash-acoes a{font-size:11pt;color:var(--folha);padding:10px 6px;align-self:center}
  @media (max-width:600px){.splash-caixa{padding:22px 18px}.splash-acoes button{width:100%}}
  @media print{.splash{display:none !important}body.com-splash{overflow:visible !important}}
  .anexos-box{border:2px dashed var(--folha);border-radius:6px;padding:22px 16px;text-align:justify;color:#444;background:#FAFBF7;cursor:pointer;transition:background .15s}
  .anexos-box.arrastando{background:#EEF3E2}
  .anexos-box p{margin:4px 0;font-size:11pt}
  .anexos-box .dica{font-size:10pt;color:#666}
  .lista-anexos{list-style:none;padding:0;margin:12px 0 0}
  .lista-anexos li{display:flex;align-items:center;gap:8px;border:1px solid var(--linha);border-radius:4px;padding:7px 10px;margin-bottom:6px;font-size:11pt;flex-wrap:wrap}
  .lista-anexos li .nome-anexo{flex:1 1 220px;overflow-wrap:anywhere}
  .lista-anexos li .pag{font-size:10pt;color:#666}
  .lista-anexos li button{font-family:inherit;font-size:10pt;background:none;border:1px solid var(--linha);border-radius:3px;padding:2px 8px;cursor:pointer}
  .lista-anexos li button.rem{border-color:#A33;color:#A33}
  .lista-anexos li.processando{color:#666;font-style:italic}
  #anexosImpressao{display:none}
  .resumo-anexos{display:none}
  @media print{
    @page{size:A4;margin:3cm 2cm 2cm 3cm}
    .campo input,.campo select,.campo textarea{display:none !important}
    .vi{display:block;font-size:12pt;border-bottom:1px solid #999;min-height:1.45em;padding:2px 2px 3px;white-space:pre-wrap;overflow-wrap:anywhere;word-break:normal;line-height:1.35}
    .campo.longo{break-inside:auto !important;page-break-inside:auto !important}
    .campo.longo .vi{orphans:3;widows:3}
    .anexos-box,.lista-anexos{display:none !important}
    .resumo-anexos{display:block;font-size:12pt;line-height:1.5}
    .resumo-anexos ol{margin:4px 0 0;padding-left:22px}
    #anexosImpressao{display:block}
    .pagina-anexo{break-before:page;page-break-before:always;text-align:justify}
    .pagina-anexo .leg{font-size:10pt;color:#444;margin:0 0 3mm;text-align:justify}
    .pagina-anexo img{max-width:100%;max-height:238mm;object-fit:contain;display:block;margin:0 auto}
    html,body{background:#fff;font-family:Arial, Helvetica, sans-serif !important;font-size:12pt}
    body *{font-family:Arial, Helvetica, sans-serif !important}
    .campo label,.campo input,.campo select,.campo textarea,.declaracao,.assinatura,.coordenadora,.responsavel-form,.responsavel-form span,h2,h3{font-size:12pt}
    h1{font-size:14pt}
    .grade{grid-template-columns:repeat(6,1fr) !important}
    .c2{grid-column:span 2 !important}.c3{grid-column:span 3 !important}.c4{grid-column:span 4 !important}.c6{grid-column:span 6 !important}
    .assinatura{grid-template-columns:1fr 1fr !important}
    .barra,.remover,.add,.aviso,.nao-imprimir{display:none !important}
    .folha{box-shadow:none;margin:0;padding:0;max-width:none}
    h2{-webkit-print-color-adjust:exact;print-color-adjust:exact}
    .campo input,.campo select,.campo textarea{border:none;border-bottom:1px solid #999;border-radius:0;background:#fff;padding:3px 2px;-webkit-appearance:none;appearance:none}
    .campo textarea{resize:none;overflow:hidden}
    .campo,.assinatura,.declaracao{break-inside:avoid;page-break-inside:avoid}
    h2,h3{break-after:avoid;page-break-after:avoid}
    ::placeholder{color:transparent}
  }
</style>
</head>
<body class="com-splash">

<div class="splash" id="splash" role="dialog" aria-modal="true" aria-labelledby="splashTitulo">
  <div class="splash-caixa">
    <div class="splash-topo">COORDENAÇÃO SOCIETÁRIA</div>
    <h2 id="splashTitulo">CHECKLIST PARA CONSTITUIÇÃO DE EMPRESA ME E EPP</h2>
    <h4>Para que serve</h4>
    <p>Este formulário reúne as informações necessárias para a constituição de empresas enquadradas como Microempresa (ME) ou Empresa de Pequeno Porte (EPP): dados do estabelecimento, dados da empresa e dados dos sócios. Também permite anexar os documentos pessoais e do imóvel. Ao final, gera um único arquivo PDF com o formulário preenchido e os anexos, pronto para ser enviado à Coordenação Societária.</p>
    <h4>Privacidade</h4>
    <p>Nenhum dado digitado e nenhum documento anexado é enviado pela internet, compartilhado ou armazenado por este sistema. Todo o processamento acontece somente no seu navegador, e as informações são apagadas quando a página é fechada ou recarregada. Se quiser guardar o preenchimento para continuar depois, use o botão "Salvar dados (arquivo)", que cria um arquivo apenas no seu computador.</p>
    <h4>Como usar</h4>
    <p>Preencha os campos (os marcados com asterisco são obrigatórios), inclua os demais sócios pelo botão "Incluir mais sócios", anexe os documentos na seção V e clique em "Exportar PDF". Na janela de impressão, escolha "Salvar como PDF" e mantenha as margens no padrão.</p>
    <p class="creditos">Idealização: Sara Oliveira, Coordenação Societária. Desenvolvimento: Carlos Alexandre Albuquerque Oliveira.</p>
    <div class="splash-acoes">
      <button type="button" id="iniciarPreenchimento">Iniciar preenchimento</button>
      <a href="manual.html" target="_blank" rel="noopener">Ler o manual</a>
    </div>
  </div>
</div>

<div class="barra">
  <button type="button" class="principal" onclick="exportarPDF()">Exportar PDF</button>
  <button type="button" onclick="salvarArquivo()">Salvar dados (arquivo)</button>
  <label>Carregar dados<input type="file" accept=".json" onchange="carregarArquivo(event)" hidden></label>
  <button type="button" onclick="limpar()">Limpar formulário</button>
  <button type="button" id="abrirApresentacao">Sobre</button>
  <a href="manual.html" target="_blank" rel="noopener">Manual</a>
  <a href="https://oliveiracarlosadv.github.io/Contabilidade/">Todas as soluções</a>
</div>

<main class="folha" id="formulario">
  <header>
    <div class="responsavel-form">COORDENAÇÃO SOCIETÁRIA</div>
    <h1>CHECKLIST PARA CONSTITUIÇÃO DE EMPRESA ME E EPP</h1>
    <div class="coordenadora">COORDENADORA: SARA OLIVEIRA</div>
  </header>

  <div class="grade">
    <div class="campo c4"><label class="req">Cliente / responsável pelas informações</label><input data-k="responsavel"></div>
    <div class="campo c2"><label class="req">Data do preenchimento</label><input type="date" data-k="data_preenchimento"></div>
  </div>

  <h2>I. Dados do estabelecimento</h2>
  <div class="grade bloco">
    <div class="campo c6 longo"><label><span class="num">01.</span>Endereço comercial (logradouro, número, complemento, bairro, município/UF e CEP)</label><textarea data-k="endereco_comercial" rows="2"></textarea></div>
    <div class="campo c6"><label><span class="num">02.</span>Ponto de referência</label><input data-k="ponto_referencia"></div>
    <div class="campo c3"><label><span class="num">03.</span>Perímetro: entre a rua / avenida</label><input data-k="perimetro_1"></div>
    <div class="campo c3"><label>e a rua / avenida</label><input data-k="perimetro_2"></div>
    <div class="campo c3"><label><span class="num">04.</span>Número do IPTU comercial (inscrição imobiliária)</label><input data-k="iptu"></div>
    <div class="campo c3"><label><span class="num">05.</span>Área utilizada (m²)</label><input data-k="area_m2" inputmode="decimal"></div>
    <div class="campo c2"><label><span class="num">06.</span>Vagas de garagem</label><input data-k="vagas" type="number" min="0"></div>
    <div class="campo c2"><label><span class="num">07.</span>Quantidade de pavimentos</label><input data-k="pavimentos" type="number" min="0"></div>
    <div class="campo c2"><label><span class="num">08.</span>Capacidade (pessoas)</label><input data-k="capacidade" type="number" min="0"></div>
    <div class="campo c3"><label><span class="num">09.</span>Situação do imóvel</label>
      <select data-k="situacao_imovel"><option value=""></option><option>Próprio</option><option>Alugado</option><option>Cedido</option></select></div>
  </div>

  <h2>II. Dados da empresa</h2>
  <div class="grade bloco">
    <div class="campo c2"><label><span class="num">10.</span>Capital social (R$)</label><input data-k="capital_social" class="moeda" inputmode="numeric"></div>
    <div class="campo c4"><label><span class="num">11.</span>Nome empresarial</label><input data-k="nome_empresarial"></div>
    <div class="campo c6"><label><span class="num">12.</span>Nome de fantasia</label><input data-k="nome_fantasia"></div>
    <div class="campo c6 longo"><label><span class="num">13.</span>Objeto social (atividades)</label><textarea data-k="objeto_social" rows="4"></textarea></div>
    <div class="campo c2"><label><span class="num">14.</span>Porte da empresa</label>
      <select data-k="porte"><option value=""></option><option>Microempresa (ME)</option><option>Empresa de Pequeno Porte (EPP)</option><option>Demais</option></select></div>
    <div class="campo c2"><label><span class="num">15.</span>E-mail da empresa</label><input data-k="email_empresa" type="email"></div>
    <div class="campo c2"><label><span class="num">16.</span>Telefone comercial</label><input data-k="telefone_empresa" class="fone" inputmode="numeric"></div>
  </div>

  <h2>III. Dados dos sócios</h2>
  <p class="obrig nao-imprimir">O preenchimento do primeiro sócio é obrigatório (campos marcados com *). Para os demais, utilize o botão "Incluir mais sócios".</p>
  <div id="socios"></div>
  <button type="button" class="add" onclick="adicionarSocio()">Incluir mais sócios</button>

  <h2>IV. Outras informações</h2>
  <div class="grade bloco">
    <div class="campo c6 longo"><label class="opc">Observações, documentos pendentes ou demais informações relevantes</label><textarea data-k="outras_informacoes" rows="5"></textarea></div>
  </div>

  <h2>V. Documentos anexos</h2>
  <div class="anexos-box nao-imprimir" id="caixaAnexos" tabindex="0" role="button" aria-label="Anexar documentos">
    <p>Clique aqui ou arraste os documentos para anexar</p>
    <p class="dica">Aceita PDF, JPG e PNG. Os anexos serão incluídos ao final do PDF exportado, em um único documento.</p>
    <input type="file" id="arquivoAnexo" accept="application/pdf,image/jpeg,image/png,image/webp,.pdf,.jpg,.jpeg,.png,.webp" multiple hidden>
  </div>
  <ul class="lista-anexos" id="listaAnexos"></ul>
  <div class="resumo-anexos" id="resumoAnexos"></div>

  <p class="declaracao">Declaro, para os devidos fins, que as informações prestadas neste formulário são verdadeiras e autorizo sua utilização para os atos de constituição e registro da empresa perante os órgãos competentes.</p>
  <div class="grade nao-quebrar">
    <div class="campo c3"><label>Local</label><input data-k="local_assinatura" value="Belém/PA"></div>
    <div class="campo c3"><label>Data</label><input type="date" data-k="data_assinatura"></div>
  </div>
  <div class="assinatura">
    <div>Assinatura do responsável pelos dados</div>
    <div>Sara Oliveira<br>Coordenação Societária</div>
  </div>
</main>
<div id="anexosImpressao"></div>

<p class="aviso">Nenhum dado é enviado pela internet nem armazenado por este sistema: tudo é apagado ao fechar ou recarregar a página. Para guardar o preenchimento, use "Salvar dados (arquivo)", que cria um arquivo apenas no seu computador. Para exportar, clique em "Exportar PDF" e escolha "Salvar como PDF" na janela de impressão.</p>

<template id="modeloSocio">
  <div class="socio">
    <h3><span class="titulo">Sócio</span><button type="button" class="remover" onclick="removerSocio(this)">Remover</button></h3>
    <div class="grade">
      <div class="campo c6"><label>Nome completo</label><input data-s="nome"></div>
      <div class="campo c2"><label><span class="num">1.</span>Número do RG</label><input data-s="rg"></div>
      <div class="campo c4"><label><span class="num">2.</span>Filiação: nome da mãe</label><input data-s="mae"></div>
      <div class="campo c4"><label class="opc">Filiação: nome do pai</label><input data-s="pai" data-opc="1"></div>
      <div class="campo c2"><label><span class="num">3.</span>Data de nascimento</label><input type="date" data-s="nascimento"></div>
      <div class="campo c2"><label><span class="num">4.</span>Número do CPF</label><input data-s="cpf" class="cpf" inputmode="numeric"></div>
      <div class="campo c2"><label><span class="num">5.</span>Data de expedição do RG</label><input type="date" data-s="rg_expedicao"></div>
      <div class="campo c2"><label><span class="num">6.</span>Órgão expedidor/UF</label><input data-s="rg_orgao"></div>
      <div class="campo c3"><label><span class="num">7.</span>Estado civil</label>
        <select data-s="estado_civil" onchange="alternarRegime(this)">
          <option value=""></option><option>Solteiro(a)</option><option>Casado(a)</option><option>União estável</option>
          <option>Divorciado(a)</option><option>Separado(a) judicialmente</option><option>Viúvo(a)</option>
        </select></div>
      <div class="campo c3 regime oculto"><label>Regime de bens</label>
        <select data-s="regime">
          <option value=""></option><option>Comunhão parcial de bens</option><option>Comunhão universal de bens</option>
          <option>Separação convencional de bens</option><option>Separação obrigatória de bens</option><option>Participação final nos aquestos</option>
        </select></div>
      <div class="campo c6"><label><span class="num">8.</span>Endereço residencial (logradouro, número, complemento, bairro, município/UF e CEP)</label><textarea data-s="endereco" rows="2"></textarea></div>
      <div class="campo c6"><label><span class="num">9.</span>Profissão</label><input data-s="profissao"></div>
      <div class="campo c2"><label class="opc"><span class="num">10.</span>Nº do registro profissional</label><input data-s="reg_prof_numero" data-opc="1"></div>
      <div class="campo c2"><label class="opc"><span class="num">11.</span>Órgão expedidor do registro</label><input data-s="reg_prof_orgao" data-opc="1" placeholder="Ex.: CRC/PA, CREA/PA, OAB/PA"></div>
      <div class="campo c2"><label class="opc"><span class="num">12.</span>Data de expedição do registro</label><input type="date" data-s="reg_prof_expedicao" data-opc="1"></div>
      <div class="campo c3"><label><span class="num">13.</span>E-mail</label><input type="email" data-s="email"></div>
      <div class="campo c3"><label><span class="num">14.</span>Telefone</label><input data-s="telefone" class="fone" inputmode="numeric"></div>
    </div>
  </div>
</template>

<script>/*PDFJS_LIB*/</script>
<script>/*PDFJS_WORKER*/</script>
<script>
const CHAVE = 'checklist_constituicao_me_epp_v1';
const area = document.getElementById('socios');

function adicionarSocio(dados){
  const n = document.getElementById('modeloSocio').content.cloneNode(true);
  const el = n.querySelector('.socio');
  if (dados) el.querySelectorAll('[data-s]').forEach(c => { c.value = dados[c.dataset.s] || ''; });
  area.appendChild(el);
  alternarRegime(el.querySelector('[data-s="estado_civil"]'));
  renumerar(); ajustarTextos(); salvar();
}
function removerSocio(btn){
  if (area.children.length <= 1) { alert('É necessário manter ao menos um sócio.'); return; }
  if (!confirm('Remover este sócio?')) return;
  btn.closest('.socio').remove(); renumerar(); salvar();
}
function renumerar(){
  [...area.children].forEach((s,i) => s.querySelector('.titulo').textContent = i === 0 ? 'Sócio 01 (obrigatório)' : 'Sócio ' + String(i+1).padStart(2,'0'));
}
function alternarRegime(sel){
  const casado = sel.value === 'Casado(a)';
  const bloco = sel.closest('.socio').querySelector('.regime');
  bloco.classList.toggle('oculto', !casado);
  if (!casado) bloco.querySelector('select').value = '';
}

function coletar(){
  const d = {empresa:{}, socios:[]};
  document.querySelectorAll('[data-k]').forEach(c => d.empresa[c.dataset.k] = c.value);
  [...area.children].forEach(s => {
    const o = {}; s.querySelectorAll('[data-s]').forEach(c => o[c.dataset.s] = c.value); d.socios.push(o);
  });
  return d;
}
function aplicar(d){
  document.querySelectorAll('[data-k]').forEach(c => { if (d.empresa && d.empresa[c.dataset.k] !== undefined) c.value = d.empresa[c.dataset.k]; });
  area.innerHTML = '';
  (d.socios && d.socios.length ? d.socios : [null]).forEach(s => adicionarSocio(s));
  if (Array.isArray(d.anexos)) { anexos = d.anexos.map(a => ({nome: a.nome, paginas: a.paginas || []})); renderizarListaAnexos(); }
}
let alterado = false;
/* Nenhum dado é gravado no navegador: a função apenas registra que houve alteração, para avisar antes de fechar a página. */
function salvar(){ alterado = true; }
window.addEventListener('beforeunload', e => { if (alterado || anexos.length) { e.preventDefault(); e.returnValue = ''; } });

function nomeArquivo(){
  const campoData = document.querySelector('[data-k="data_preenchimento"]');
  if (!campoData.value) {
    const h = new Date();
    campoData.value = h.getFullYear() + '-' + String(h.getMonth()+1).padStart(2,'0') + '-' + String(h.getDate()).padStart(2,'0');
    salvar();
  }
  const [a, m, d] = campoData.value.split('-');
  const partes = document.querySelector('[data-k="responsavel"]').value.trim().split(/\s+/).filter(Boolean);
  const nome = partes.length > 1 ? partes[0] + ' ' + partes[partes.length - 1] : (partes[0] || '');
  return (d + '-' + m + '-' + a + ' ' + nome).replace(/[\\/:*?"<>|]/g, '').trim();
}
function validarResponsavel(){
  const c = document.querySelector('[data-k="responsavel"]');
  c.classList.remove('erro');
  if (c.value.trim()) return true;
  c.classList.add('erro'); c.focus(); c.scrollIntoView({block:'center'});
  alert('Informe o nome do responsável pelas informações. Ele compõe o nome do arquivo exportado.');
  return false;
}
function salvarArquivo(){
  if (!validarResponsavel()) return;
  const d = coletar();
  d.anexos = anexos.filter(a => !a.processando).map(a => ({nome: a.nome, paginas: a.paginas}));
  const blob = new Blob([JSON.stringify(d,null,2)], {type:'application/json;charset=utf-8'});
  const a = document.createElement('a'); a.href = URL.createObjectURL(blob); a.download = nomeArquivo() + '.json'; a.click();
  setTimeout(() => URL.revokeObjectURL(a.href), 1000);
}
function carregarArquivo(ev){
  const f = ev.target.files[0]; if (!f) return;
  const r = new FileReader();
  r.onload = () => { try { aplicar(JSON.parse(r.result)); salvar(); } catch(e){ alert('Arquivo inválido.'); } };
  r.readAsText(f, 'UTF-8'); ev.target.value = '';
}
function limpar(){
  if (!confirm('Apagar todos os dados preenchidos?')) return;
  document.querySelectorAll('[data-k]').forEach(c => c.value = c.defaultValue || '');
  area.innerHTML = ''; adicionarSocio();
  anexos = []; renderizarListaAnexos();
  alterado = false;
}

function ajustarTextos(){
  document.querySelectorAll('textarea').forEach(t => { t.style.height = 'auto'; t.style.height = (t.scrollHeight + 2) + 'px'; });
}
function validarPrimeiroSocio(){
  const s = area.firstElementChild; const faltando = [];
  s.querySelectorAll('[data-s]').forEach(c => {
    c.classList.remove('erro');
    if (c.dataset.opc) return;
    if (c.dataset.s === 'regime' && s.querySelector('[data-s="estado_civil"]').value !== 'Casado(a)') return;
    if (!c.value.trim()) { c.classList.add('erro'); faltando.push(c); }
  });
  if (faltando.length) {
    faltando[0].focus(); faltando[0].scrollIntoView({block:'center'});
    alert('Preencha todos os campos obrigatórios do primeiro sócio antes de exportar (' + faltando.length + ' pendente(s)).');
    return false;
  }
  return true;
}
document.addEventListener('input', e => e.target.classList.remove('erro'));
function exportarPDF(){
  if (!validarResponsavel() || !validarPrimeiroSocio()) return;
  if (anexos.some(a => a.processando)) { alert('Aguarde o processamento dos anexos antes de exportar.'); return; }
  const titulo = document.title;
  document.title = nomeArquivo();
  const restaurar = () => { document.title = titulo; window.removeEventListener('afterprint', restaurar); };
  window.addEventListener('afterprint', restaurar);
  ajustarTextos(); prepararImpressao();
  aguardarImagens().then(() => window.print());
}
function fmtData(v){ if (!v) return ''; const [a,m,d] = v.split('-'); return d + '/' + m + '/' + a; }
let impressaoPronta = false;
function prepararImpressao(){
  limparImpressao(); impressaoPronta = true;
  document.querySelectorAll('#formulario .campo').forEach(campo => {
    const c = campo.querySelector('input:not([type=file]),select,textarea'); if (!c) return;
    let v = c.value;
    if (c.tagName === 'SELECT') v = c.selectedIndex > 0 ? c.options[c.selectedIndex].text : '';
    else if (c.type === 'date') v = fmtData(v);
    const span = document.createElement('div'); span.className = 'vi'; span.textContent = v || '\u00a0';
    c.after(span);
  });
  montarAnexosImpressao();
}
function limparImpressao(){ document.querySelectorAll('.vi').forEach(e => e.remove()); }
function encerrarImpressao(){ limparImpressao(); document.getElementById('anexosImpressao').innerHTML = ''; impressaoPronta = false; }
window.addEventListener('beforeprint', () => { ajustarTextos(); if (!impressaoPronta) prepararImpressao(); });
window.addEventListener('afterprint', encerrarImpressao);

/* ---------- Anexos ---------- */
let anexos = [];
const caixa = document.getElementById('caixaAnexos');
const entradaAnexo = document.getElementById('arquivoAnexo');
const listaAnexos = document.getElementById('listaAnexos');
caixa.addEventListener('click', () => entradaAnexo.click());
caixa.addEventListener('keydown', e => { if (e.key === 'Enter' || e.key === ' ') { e.preventDefault(); entradaAnexo.click(); } });
entradaAnexo.addEventListener('change', e => { adicionarArquivos(e.target.files); e.target.value = ''; });
['dragenter','dragover'].forEach(t => caixa.addEventListener(t, e => { e.preventDefault(); caixa.classList.add('arrastando'); }));
['dragleave','drop'].forEach(t => caixa.addEventListener(t, e => { e.preventDefault(); caixa.classList.remove('arrastando'); }));
caixa.addEventListener('drop', e => adicionarArquivos(e.dataTransfer.files));

function lerComo(arq, modo){ return new Promise((ok, erro) => { const r = new FileReader(); r.onload = () => ok(r.result); r.onerror = erro; r[modo](arq); }); }
function carregarImagem(src){ return new Promise((ok, erro) => { const i = new Image(); i.onload = () => ok(i); i.onerror = erro; i.src = src; }); }
async function imagemParaPagina(src){
  const img = await carregarImagem(src);
  const max = 2000, k = Math.min(1, max / Math.max(img.naturalWidth, img.naturalHeight));
  const cv = document.createElement('canvas'); cv.width = Math.round(img.naturalWidth * k); cv.height = Math.round(img.naturalHeight * k);
  const ctx = cv.getContext('2d'); ctx.fillStyle = '#fff'; ctx.fillRect(0,0,cv.width,cv.height); ctx.drawImage(img, 0, 0, cv.width, cv.height);
  return cv.toDataURL('image/jpeg', 0.88);
}
async function pdfParaPaginas(buf, progresso){
  const pdf = await pdfjsLib.getDocument({data: new Uint8Array(buf), isEvalSupported:false}).promise;
  const paginas = [];
  for (let i = 1; i <= pdf.numPages; i++) {
    progresso(i, pdf.numPages);
    const pg = await pdf.getPage(i);
    const v0 = pg.getViewport({scale:1});
    const escala = Math.min(2.5, 1900 / Math.max(v0.width, v0.height));
    const vp = pg.getViewport({scale:escala});
    const cv = document.createElement('canvas'); cv.width = Math.ceil(vp.width); cv.height = Math.ceil(vp.height);
    const ctx = cv.getContext('2d'); ctx.fillStyle = '#fff'; ctx.fillRect(0,0,cv.width,cv.height);
    await pg.render({canvasContext: ctx, viewport: vp}).promise;
    paginas.push(cv.toDataURL('image/jpeg', 0.88));
  }
  return paginas;
}
async function adicionarArquivos(lista){
  for (const arq of [...lista]) {
    const ehPDF = arq.type === 'application/pdf' || /\.pdf$/i.test(arq.name);
    const ehImg = /^image\/(jpeg|png|webp)$/.test(arq.type) || /\.(jpe?g|png|webp)$/i.test(arq.name);
    if (!ehPDF && !ehImg) { alert('O arquivo "' + arq.name + '" não é PDF, JPG ou PNG e não foi anexado.'); continue; }
    const item = {nome: arq.name, paginas: [], processando: true};
    anexos.push(item); renderizarListaAnexos();
    try {
      if (ehPDF) {
        if (typeof pdfjsLib === 'undefined') throw new Error('leitor de PDF indisponível');
        item.paginas = await pdfParaPaginas(await lerComo(arq, 'readAsArrayBuffer'), (i, n) => { item.status = 'processando página ' + i + ' de ' + n; renderizarListaAnexos(); });
      } else {
        item.paginas = [await imagemParaPagina(await lerComo(arq, 'readAsDataURL'))];
      }
      item.processando = false;
    } catch (e) {
      anexos = anexos.filter(a => a !== item);
      alert('Não foi possível ler o arquivo "' + arq.name + '". Verifique se ele não está protegido por senha ou corrompido.');
    }
    renderizarListaAnexos();
  }
}
function renderizarListaAnexos(){
  listaAnexos.innerHTML = '';
  anexos.forEach((a, i) => {
    const li = document.createElement('li');
    if (a.processando) li.className = 'processando';
    const nome = document.createElement('span'); nome.className = 'nome-anexo'; nome.textContent = 'Anexo ' + (i+1) + ': ' + a.nome;
    const pag = document.createElement('span'); pag.className = 'pag';
    pag.textContent = a.processando ? (a.status || 'processando...') : a.paginas.length + (a.paginas.length === 1 ? ' página' : ' páginas');
    li.append(nome, pag);
    if (!a.processando) {
      const sobe = document.createElement('button'); sobe.type = 'button'; sobe.textContent = 'Subir'; sobe.disabled = i === 0;
      sobe.onclick = () => { [anexos[i-1], anexos[i]] = [anexos[i], anexos[i-1]]; renderizarListaAnexos(); };
      const desce = document.createElement('button'); desce.type = 'button'; desce.textContent = 'Descer'; desce.disabled = i === anexos.length - 1;
      desce.onclick = () => { [anexos[i+1], anexos[i]] = [anexos[i], anexos[i+1]]; renderizarListaAnexos(); };
      const rem = document.createElement('button'); rem.type = 'button'; rem.className = 'rem'; rem.textContent = 'Remover';
      rem.onclick = () => { if (confirm('Remover o anexo "' + a.nome + '"?')) { anexos.splice(i, 1); renderizarListaAnexos(); } };
      li.append(sobe, desce, rem);
    }
    listaAnexos.appendChild(li);
  });
}
function montarAnexosImpressao(){
  const destino = document.getElementById('anexosImpressao'); destino.innerHTML = '';
  const resumo = document.getElementById('resumoAnexos'); resumo.innerHTML = '';
  const prontos = anexos.filter(a => !a.processando);
  if (!prontos.length) { resumo.textContent = 'Nenhum documento anexado.'; return; }
  const ol = document.createElement('ol');
  prontos.forEach((a, i) => {
    const li = document.createElement('li'); li.textContent = a.nome + ' (' + a.paginas.length + (a.paginas.length === 1 ? ' página)' : ' páginas)'); ol.appendChild(li);
    a.paginas.forEach((src, j) => {
      const div = document.createElement('div'); div.className = 'pagina-anexo';
      const leg = document.createElement('p'); leg.className = 'leg'; leg.textContent = 'Anexo ' + (i+1) + ': ' + a.nome + ' (página ' + (j+1) + ' de ' + a.paginas.length + ')';
      const img = document.createElement('img'); img.src = src; img.alt = leg.textContent;
      div.append(leg, img); destino.appendChild(div);
    });
  });
  resumo.append('Documentos anexados ao final deste formulário:', ol);
}
function aguardarImagens(){
  return Promise.all([...document.querySelectorAll('#anexosImpressao img')].map(i => i.complete ? null : new Promise(ok => { i.onload = i.onerror = ok; })));
}

function mascaraCPF(v){ v = v.replace(/\D/g,'').slice(0,11);
  return v.replace(/(\d{3})(\d)/,'$1.$2').replace(/(\d{3})(\d)/,'$1.$2').replace(/(\d{3})(\d{1,2})$/,'$1-$2'); }
function mascaraFone(v){ v = v.replace(/\D/g,'').slice(0,11);
  if (v.length <= 10) return v.replace(/(\d{2})(\d)/,'($1) $2').replace(/(\d{4})(\d)/,'$1-$2');
  return v.replace(/(\d{2})(\d)/,'($1) $2').replace(/(\d{5})(\d)/,'$1-$2'); }
function mascaraMoeda(v){ v = v.replace(/\D/g,''); if (!v) return '';
  return (parseInt(v,10)/100).toLocaleString('pt-BR',{minimumFractionDigits:2, maximumFractionDigits:2}); }

document.addEventListener('input', e => {
  const t = e.target;
  if (t.classList.contains('cpf')) t.value = mascaraCPF(t.value);
  else if (t.classList.contains('fone')) t.value = mascaraFone(t.value);
  else if (t.classList.contains('moeda')) t.value = mascaraMoeda(t.value);
  if (t.tagName === 'TEXTAREA') { t.style.height = 'auto'; t.style.height = (t.scrollHeight + 2) + 'px'; }
  salvar();
});
document.addEventListener('change', salvar);

(function iniciar(){
  try { localStorage.removeItem(CHAVE); } catch(e){}
  adicionarSocio(); alterado = false;
  const splash = document.getElementById('splash');
  const fechar = () => { splash.classList.add('fechado'); document.body.classList.remove('com-splash'); document.querySelector('[data-k="responsavel"]').focus(); };
  document.getElementById('iniciarPreenchimento').addEventListener('click', fechar);
  document.getElementById('abrirApresentacao').addEventListener('click', () => { splash.classList.remove('fechado'); document.body.classList.add('com-splash'); });
  document.addEventListener('keydown', e => { if (e.key === 'Escape' && !splash.classList.contains('fechado')) fechar(); });
})();
</script>
</body>
</html>
```
