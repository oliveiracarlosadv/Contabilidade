# Contabilidade

<p align="center">
  <a href="https://oliveiracarlosadv.github.io/Contabilidade/formularios/constituicao-me-epp/"><img src="https://img.shields.io/badge/ACESSAR%20O%20FORMUL%C3%81RIO-Constitui%C3%A7%C3%A3o%20de%20Empresa%20ME%20e%20EPP-556B2F?style=for-the-badge" alt="Acessar o formulário de Constituição de Empresa ME e EPP"></a>
</p>
<p align="center">
  <a href="https://oliveiracarlosadv.github.io/Contabilidade/"><img src="https://img.shields.io/badge/TODAS%20AS%20SOLU%C3%87%C3%95ES-2F4F4F?style=for-the-badge" alt="Todas as soluções"></a>
  <a href="https://oliveiracarlosadv.github.io/Contabilidade/formularios/constituicao-me-epp/manual.html"><img src="https://img.shields.io/badge/MANUAL%20DE%20USO-233310?style=for-the-badge" alt="Manual de uso"></a>
</p>

Repositório de soluções e formulários de acesso direto, desenvolvidos para apoiar as rotinas de contabilidade e de registro empresarial. Cada solução fica em uma pasta própria e é aberta por um link direto, a partir da página inicial.

Página inicial (todas as soluções): https://oliveiracarlosadv.github.io/Contabilidade/

## Soluções disponíveis

### Checklist para Constituição de Empresa ME e EPP

Área: Coordenação Societária. Coordenadora: Sara Oliveira.

Formulário para levantamento dos dados do estabelecimento, da empresa e dos sócios necessários à constituição de Microempresas (ME) e Empresas de Pequeno Porte (EPP). Permite incluir quantos sócios forem necessários, anexar documentos em PDF, JPG ou PNG e exportar tudo em um único arquivo PDF, em papel A4, fonte Arial 12, texto justificado e margens de 3 cm (superior e esquerda) e 2 cm (inferior e direita). O arquivo exportado recebe automaticamente o nome "dd-mm-aaaa Primeiro Último", formado pela data do preenchimento e pelo nome do responsável pelas informações.

Link direto: https://oliveiracarlosadv.github.io/Contabilidade/formularios/constituicao-me-epp/

Manual de uso: [formularios/constituicao-me-epp/MANUAL.md](formularios/constituicao-me-epp/MANUAL.md), também disponível em https://oliveiracarlosadv.github.io/Contabilidade/formularios/constituicao-me-epp/manual.html

Documentação técnica: [Pedidos](formularios/constituicao-me-epp/Pedidos.md), [Histórico de versões](formularios/constituicao-me-epp/Historico.md) e [Código-fonte comentado](formularios/constituicao-me-epp/Codigo-fonte.md).

## Privacidade

As soluções funcionam inteiramente no navegador de quem as utiliza. Nenhum dado digitado e nenhum documento anexado é enviado pela internet, compartilhado ou armazenado pelo sistema, e as informações são apagadas quando a página é fechada. A única forma de guardar um preenchimento é o botão "Salvar dados (arquivo)", que cria um arquivo exclusivamente no computador do usuário.

## Estrutura do repositório

O arquivo index.html, na raiz, é a página inicial com o botão e o link direto de cada solução. A pasta formularios reúne os formulários, cada um em sua própria subpasta, com o arquivo index.html da solução, o manual e a documentação. A pasta assets guarda o ícone e as imagens de pré-visualização usadas nas metatags (Open Graph e Twitter Card), e os arquivos robots.txt e sitemap.xml orientam os mecanismos de busca. O arquivo .nojekyll informa ao GitHub Pages que os arquivos devem ser publicados sem processamento.

## Como incluir uma nova solução

Crie uma pasta para a solução (por exemplo, formularios/nome-da-solucao/) com um arquivo index.html autônomo. Em seguida, acrescente um item na lista SOLUCOES do index.html da raiz, informando a área, o título, a descrição, o caminho da pasta e, se houver, o caminho do manual. Copie para o cabeçalho da nova página o bloco de metatags de uma solução existente, ajustando título, descrição, endereço e imagem, e inclua o novo endereço no sitemap.xml. O botão e o link direto da nova solução passam a aparecer automaticamente na página inicial, no endereço https://oliveiracarlosadv.github.io/Contabilidade/nome-da-pasta/.

## Publicação

Os links diretos dependem do GitHub Pages ativado para este repositório, em Settings, Pages, com a origem "Deploy from a branch", ramo main e pasta / (root). Em repositórios privados, o GitHub Pages exige plano pago da conta (GitHub Pro, Team ou Enterprise); na conta gratuita, o repositório precisa ser público para publicar as páginas. Mesmo sem o GitHub Pages, cada formulário pode ser usado baixando o arquivo index.html da sua pasta e abrindo-o no navegador.

## Componentes de terceiros

O formulário de constituição incorpora a biblioteca PDF.js 3.11.174, da Mozilla, distribuída sob a licença Apache 2.0, usada para ler os anexos em PDF diretamente no navegador.

## Créditos

Idealização: Sara Oliveira, Coordenação Societária.

Criação e desenvolvimento: Carlos Alexandre Albuquerque Oliveira.
