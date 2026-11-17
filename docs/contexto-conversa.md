# Contexto do desenvolvimento — Leitor de PDF (Portal da Transparência de Granito)

Registro do andamento do projeto e das decisões tomadas durante o desenvolvimento.
O planejamento completo está em `planejamento_leitor_pdf_portal_granito(1) copy.md`.

**Última atualização:** 17/11/2026

---

## Andamento das etapas

| # | Etapa | Situação |
|---|---|---|
| 1 | Criar o projeto Vue 3 com Vite | Concluída |
| 2 | Reproduzir cabeçalho e rodapé do portal | Concluída (`Header.vue`, `Footer.vue`) |
| 3 | Criar a estrutura da página de documentos | Concluída |
| 4 | Instalar e configurar `vue-pdf-embed` | Concluída (v2.1.7, com PDF.js 6.5) |
| 5 | Carregar um PDF de teste | Concluída |
| 6 | Barra de ferramentas e controles de navegação | Concluída |
| 7 | Download, impressão e zoom | Concluída |
| 8 | Pesquisa dentro do documento | Concluída |
| 9 | Ajustes para celulares e tablets | Concluída (testada em 1366, 820 e 390 px de largura) |
| 10 | Publicar na Vercel e testar o link no Portal | **Preparada.** A publicação depende do login na conta da Vercel (ver abaixo) |

---

## Como o leitor funciona

### Componentes

```text
App.vue
├── Header.vue
├── views/DocumentoView.vue      página do documento (dados, caminho de navegação, botões do topo)
│   ├── Breadcrumb.vue
│   └── PdfViewer.vue            carregado sob demanda (defineAsyncComponent)
│       ├── PdfToolbar.vue       só interface: recebe valores (props) e avisa as ações (eventos)
│       └── VuePdfEmbed          renderização (PDF.js)
└── Footer.vue
```

- **`DocumentoView.vue`** guarda os dados do documento no objeto `documento` (título, arquivo, páginas, data). Para trocar o PDF, basta mudar `titulo` e `arquivo`.
- **`PdfViewer.vue`** concentra a lógica: carrega o PDF (`usePdfDocument`), controla o zoom, acompanha a página atual e cuida da impressão e da tela cheia. Emite `carregado` com `{ paginas, atualizadoEm }` e expõe `imprimir()`, usado pelo botão "Imprimir" do topo.
- **`PdfToolbar.vue`** não tem lógica de PDF, só recebe valores e emite eventos (`anterior`, `proxima`, `ir-para`, `zoom-mais`, `zoom-menos`, `ajustar-largura`, `imprimir`, `tela-cheia`).

### Funcionalidades

| Funcionalidade | Como foi feita |
|---|---|
| Exibir o PDF | Todas as páginas em rolagem contínua, com camada de texto (permite selecionar e copiar) e camada de anotações (links do PDF). |
| Página atual | Calculada durante a rolagem: é a página que cruza a linha a 30% da altura da área. |
| Anterior, próxima e campo "1 de 7" | Rola a área até a página escolhida. O campo aceita digitar o número e Enter. |
| Zoom | Níveis de 25% a 300%, em relação ao tamanho real da página. A posição de leitura é mantida ao mudar o zoom. |
| Ajustar à largura | Modo padrão: a página ocupa a largura da área e se adapta quando a janela muda de tamanho. |
| Pesquisa | **Removida a pedido.** Chegou a funcionar com `usePdfSearch` do `vue-pdf-embed` (ignorava maiúsculas e acentos, destacava os resultados e navegava entre eles). Para voltar a ter a busca: usar `usePdfSearch(doc)` no `PdfViewer`, passar `findController` para o `<VuePdfEmbed :find-controller>` e pôr de volta na barra o campo de busca e os botões de resultado anterior e próximo. A área de rolagem precisa continuar com `position: relative`. A camada de texto continua ativa, então ainda dá para selecionar e copiar o texto, e o Ctrl+F do navegador encontra parte do texto. |
| Imprimir | `print()` do `vue-pdf-embed`, a 200 dpi, com todas as páginas. Mostra o aviso "Preparando impressão…". |
| Baixar | Link com o atributo `download`, que funciona mesmo sem JavaScript. |
| Tela cheia | O `vue-pdf-embed` não oferece esse recurso; foi feito com a API de tela cheia do navegador (`requestFullscreen`) sobre o leitor inteiro (barra + páginas). No Safari do iPhone, que não aceita tela cheia em elementos, o leitor passa a ocupar a janela toda via CSS (`.leitor--tela-cheia`). Esc sai nos dois casos. |
| Páginas e data | Lidas do PDF. A data vem do campo `ModDate` dos metadados; se `documento.atualizadoEm` estiver preenchido, ele tem prioridade. |
| Carregamento e erro | Mostra o progresso em %. Se o PDF falhar, aparece uma mensagem com o botão "Baixar o PDF". |
| Responsivo | Até 640 px o leitor ocupa a largura toda, "Ajustar à largura" mostra só o ícone e a barra quebra em duas linhas (páginas e zoom em cima; imprimir, baixar e tela cheia embaixo). |
| Acessibilidade | `role="toolbar"`, rótulos em todos os botões, `aria-live` no zoom e no resultado da busca, foco visível em amarelo, a área do documento rolável pelo teclado, animação desligada para quem prefere menos movimento. |

### Detalhes técnicos importantes

- A área de rolagem (`.leitor__area`) precisa de `position: relative`. Sem isso, a busca do PDF.js não consegue rolar até o resultado.
- As cores do destaque são variáveis CSS declaradas no próprio `.highlight`, por isso a sobrescrita é feita em `:deep(.textLayer .highlight)`.
- Em zoom alto, a resolução do canvas é reduzida automaticamente (limite de 16 milhões de pixels) para não estourar a memória em celulares, principalmente iPhones.
- O PDF.js deixa o pacote do leitor com cerca de 2,5 MB (830 KB com gzip). Por isso o `PdfViewer` é carregado em separado e a página inicial fica com 85 KB. O aviso de tamanho foi ajustado em `vite.config.js` (`chunkSizeWarningLimit`).

---

## Registro

### Página em branco no `localhost:5173`

- **Causa:** o `index.html` estava vazio (0 bytes). O primeiro arquivo do planejamento também tinha sido salvo vazio, então provavelmente houve uma falha ao salvar no editor.
- **Solução:** o `index.html` foi recriado com `lang="pt-BR"`, o título da página e a fonte **Raleway**.
- **Dica:** se a página voltar a aparecer em branco sem erro no console, confira se algum arquivo foi salvo vazio.

### Etapa 3 — Estrutura da página

- Criados `Breadcrumb.vue` e `DocumentoView.vue`. O `App.vue` monta Header → DocumentoView → Footer.
- Caminho de navegação ajustado para **Início › Alfabetiza Granito › Plano Municipal de Comunicação da Política de Alfabetização do Território**. O nível "Documentos" foi removido.
- "Início" é mostrado como um **ícone de casinha** preenchido em azul, desenhado em SVG no próprio `Breadcrumb.vue`. Para usar o ícone num item, basta acrescentar `icone: 'casa'`. O texto "Início" continua escondido na tela para leitores de tela e aparece como dica ao passar o mouse.
- Os botões "Baixar PDF" e "Imprimir" do topo foram comentados, porque a barra do leitor já tem as mesmas ações.

### Ícone da aba do navegador (favicon)

- Gerado a partir do brasão da `logo_granito.png`, recortado da parte esquerda da logo. A logo inteira é muito larga e ficaria ilegível em 16–32 px.
- Arquivos em `public/`: `favicon-32.png` (aba), `favicon-192.png` (Android e telas de alta resolução) e `favicon-180.png` (atalho na tela inicial do iPhone). Eles são ligados no `index.html`.
- O `favicon.ico` antigo, que era o logo padrão do Vue, foi apagado.

### Barra superior do cabeçalho (decorativa)

- Adicionada ao `Header.vue` a faixa fina acima da logomarca, igual à do portal oficial. À esquerda ficam os atalhos (Ir para o conteúdo, o menu e o rodapé). À direita: FAQ, Mapa do Site, Acessibilidade | A- A A+ | Alto Contraste, Webmail e o cadeado.
- **Só os três atalhos "Ir para" funcionam.** Eles levam a `#conteudo` (o `<main>`), `#menu` (os links do cabeçalho) e `#rodape` (o `<footer>`). Os outros itens são só visuais, como foi pedido.
- Responsivo: abaixo de 1400 px os atalhos somem (em 1366 px eles encostavam no "FAQ"); abaixo de 800 px a barra inteira some.

### Etapas 4 a 9 — Leitor

- Instaladas as dependências `vue-pdf-embed` e `lucide-vue-next` (ícones).
- Criados `PdfViewer.vue` e `PdfToolbar.vue`. O `DocumentoView.vue` passou a usar o leitor e os ícones do Lucide.
- O PDF de teste foi renomeado para `public/documentos/plano-municipal-comunicacao-alfabetizacao.pdf` (sem espaços nem acentos), para evitar problemas de link.
- **Testes feitos no Chrome (sem janela), com o build de produção:** carregamento das 7 páginas; metadados ("7 páginas · Atualizado em 03/11/2026"); próxima página; ir para a página 5; zoom +/− e ajuste à largura; busca "alfabetizacao" (31 resultados, destaque e navegação entre eles); busca sem resultado; download (200, `application/pdf`); impressão sem erros; layout a 1366, 820 e 390 px sem rolagem horizontal da página. Nenhum erro no console.
- **Ainda não testado:** a janela de impressão real (só dá para conferir num navegador com tela) e aparelhos físicos (iPhone e Android).

---

## Etapa 10 — Publicação na Vercel

O projeto é um Vite estático: a Vercel detecta tudo sozinha (build `npm run build`, saída `dist`) e não precisa de `vercel.json`.

### Opção A — pela linha de comando (mais rápida)

```bash
npx vercel login      # entrar na conta (abre o navegador)
npx vercel            # primeira publicação (cria o projeto e gera uma URL de prévia)
npx vercel --prod     # publica a versão oficial
```

### Opção B — pelo GitHub (atualiza sozinho a cada `git push`)

1. `git init`, `git add .`, `git commit -m "Leitor de PDF"` (o `.gitignore` já ignora `node_modules` e `dist`).
2. Criar um repositório no GitHub e enviar o código (`git push`).
3. Em vercel.com → **Add New → Project**, importar o repositório e clicar em **Deploy**.

### Depois de publicar

1. Abrir a URL da Vercel e testar: carregamento, busca, zoom, impressão e download, no computador e no celular.
2. No editor do Portal da Transparência, transformar o texto **"Acessar documento em PDF"** num link para a URL da Vercel.
3. Clicar no link a partir do Portal para confirmar o acesso.

> A conta da Vercel deve ser, de preferência, uma conta institucional ou autorizada pela Prefeitura, e não pessoal (ver seção 7 do planejamento).

---

## Pendências e sugestões

- **Links do caminho de navegação:** a casinha (Início) leva a `https://granito.pe.gov.br/` (endereço deduzido; confirmar) e "Alfabetiza Granito" leva a `https://granito.pe.gov.br/paginas/alfabetiza-granito`.
- **Logo do cabeçalho:** `logo_granito.png` tem 358 KB. Convertê-lo para WebP ou SVG deixaria a página mais leve.
- **Vários documentos:** hoje o leitor mostra um documento fixo. Para vários, a ideia é ler o documento pela URL (ex.: `?doc=plano-municipal`) a partir de uma lista em JSON. É uma mudança pequena, e o `PdfViewer` já recebe a `url` por prop.
- **Decisões em aberto** (seção 11 do planejamento):
  - um documento por endereço ou uma página com catálogo;
  - onde os PDFs vão ficar hospedados;
  - qual conta institucional vai administrar os documentos e a hospedagem;
  - se será possível usar um subdomínio oficial da Prefeitura;
  - se o painel administrativo entra na primeira versão.
