# Projeto: Leitor de PDF — Portal da Transparência de Granito

## 1. Contexto

Atualmente, a página institucional do programa **Alfabetiza Granito** é editada por meio de um painel que permite inserir principalmente textos, imagens e links. O editor também oferece uma visualização do código HTML do conteúdo, mas a publicação continua limitada aos recursos disponibilizados pelo próprio portal.

Uma possibilidade é manter a página institucional atual e criar uma página independente para leitura de documentos PDF. Essa nova página seria hospedada na **Vercel** e acessada por meio de um link publicado no Portal da Transparência.

A ideia é aproveitar a identidade visual do site oficial, principalmente o cabeçalho e o rodapé, mas desenvolver uma nova área central dedicada à leitura de documentos.

## 2. Objetivo do projeto

Desenvolver uma página web responsiva que permita ao público consultar documentos PDF diretamente no navegador, sem precisar sair para uma plataforma externa de visualização.

O primeiro objetivo é criar o frontend do leitor. Em uma etapa posterior, o projeto poderá receber um painel administrativo para cadastrar documentos e atualizar arquivos.

## 3. Referência visual

A proposta visual deve preservar a identidade institucional do Portal da Transparência de Granito.

### Elementos que devem ser mantidos como referência

- **Cabeçalho:** logomarca da Prefeitura, faixas em tons de azul, acessibilidade, links institucionais, navegação e campo de busca.
- **Rodapé:** fundo azul-escuro, identificação da Prefeitura, links úteis, endereço, contato e redes sociais.

### Área central — novo leitor de PDF

A área central será redesenhada. A proposta inclui:

1. **Caminho de navegação (breadcrumb):** indicar que o visitante está na área de documentos e mostrar o título do arquivo.
2. **Identificação do documento:** título, formato, quantidade de páginas e data de atualização, quando essas informações estiverem disponíveis.
3. **Ações principais:** botões para baixar e imprimir o PDF.
4. **Visualizador integrado:** exibir o documento dentro da própria página.
5. **Barra de ferramentas:** navegação entre páginas, zoom, ajuste à largura, pesquisa, impressão e download.

A interface deve ser limpa, institucional e adaptável a computadores, tablets e celulares.

## 4. Tecnologias recomendadas

A stack sugerida para a primeira versão é:

| Tecnologia | Utilização |
|---|---|
| **HTML** | Estrutura dos elementos da página. |
| **CSS** | Estilização, layout e responsividade. |
| **JavaScript** | Lógica dos controles e interações do leitor. |
| **Vue 3** | Organização da interface em componentes reutilizáveis. |
| **Vite** | Criação do projeto, ambiente de desenvolvimento e build. |
| **PDF.js** | Tecnologia de renderização de documentos PDF no navegador. |
| **vue-pdf-embed** | Componente Vue para incorporar e renderizar páginas de um PDF. |
| **Lucide Icons** | Ícones para ações como download, impressão, busca e navegação. |
| **Vercel** | Hospedagem e publicação do frontend. |

### Biblioteca para visualizar PDFs

A recomendação é experimentar a biblioteca **vue-pdf-embed**, que permite renderizar documentos PDF em projetos Vue 3 e utiliza a tecnologia PDF.js.

Documentação:

- [vue-pdf-embed — npm](https://www.npmjs.com/package/vue-pdf-embed)
- [PDF.js — documentação](https://mozilla.github.io/pdf.js/)

Instalação:

```bash
npm install vue-pdf-embed
```

Exemplo básico de utilização:

```vue
<script setup>
import VuePdfEmbed from 'vue-pdf-embed'

const pdfSource = '/documentos/arquivo.pdf'
</script>

<template>
  <div class="visualizador">
    <VuePdfEmbed :source="pdfSource" />
  </div>
</template>
```

Esse exemplo apresenta apenas a renderização básica do documento. A barra de ferramentas, os controles de navegação e os demais recursos da interface serão desenvolvidos separadamente.

## 5. Funcionalidades previstas

| Funcionalidade | Implementação prevista |
|---|---|
| Exibir o PDF | PDF.js por meio do vue-pdf-embed. |
| Ir para a próxima página | Estado e eventos no Vue, integrados ao visualizador. |
| Voltar à página anterior | Estado e eventos no Vue, integrados ao visualizador. |
| Zoom aumentar/diminuir | Controles próprios conectados à renderização do PDF. |
| Ajustar à largura | CSS e configuração do visualizador. |
| Pesquisar no documento | Avaliar a API de texto do PDF.js ou uma solução compatível. |
| Baixar o documento | Link para o arquivo PDF. |
| Imprimir | Ação de impressão do navegador ou do visualizador. |
| Layout responsivo | CSS e media queries. |

## 6. Organização inicial dos arquivos

Uma estrutura possível para o projeto:

```text
portal-granito/
│
├── public/
│   └── documentos/
│       └── arquivo.pdf
│
├── src/
│   ├── assets/
│   │
│   ├── components/
│   │   ├── Header.vue
│   │   ├── Footer.vue
│   │   ├── Breadcrumb.vue
│   │   ├── PdfToolbar.vue
│   │   └── PdfViewer.vue
│   │
│   ├── views/
│   │   └── DocumentoView.vue
│   │
│   ├── App.vue
│   └── main.js
│
└── package.json
```

### Responsabilidade dos componentes

- `Header.vue`: cabeçalho institucional.
- `Footer.vue`: rodapé institucional.
- `Breadcrumb.vue`: caminho de navegação.
- `PdfToolbar.vue`: controles de página, zoom, ajuste, busca, impressão e download.
- `PdfViewer.vue`: área que renderiza o documento PDF.
- `DocumentoView.vue`: organiza a página de leitura e reúne os componentes.

## 7. Armazenamento dos documentos

Para o primeiro protótipo, é possível colocar um PDF de teste na pasta `public/documentos` e carregá-lo pelo caminho correspondente.

Quando o projeto passar a disponibilizar vários documentos ou precisar de atualizações frequentes, será interessante avaliar um serviço de armazenamento de arquivos. A escolha deve considerar as regras da Prefeitura, a titularidade da conta, a capacidade de armazenamento, os limites de tráfego e a estabilidade dos links.

Para documentos institucionais, deve-se priorizar uma conta ou serviço autorizado pela administração municipal, evitando depender de contas pessoais.

## 8. Hospedagem e acesso pelo Portal da Transparência

A primeira versão do frontend poderá ser publicada na Vercel. Depois da publicação, será possível inserir o endereço da página no conteúdo do Portal da Transparência, utilizando o editor de texto para transformar uma frase ou botão disponível em link.

Exemplo de texto para o link:

> **Acessar documento em PDF**

O visitante clicaria nesse link na página institucional e seria direcionado ao leitor de PDF.

Em uma etapa futura, pode-se avaliar o uso de um domínio ou subdomínio institucional, desde que isso seja autorizado e configurado pelos responsáveis técnicos da Prefeitura.

## 9. Etapas de desenvolvimento

1. Criar o projeto Vue 3 com Vite.
2. Reproduzir o cabeçalho e o rodapé do portal com base na referência visual.
3. Criar a estrutura da página de documentos.
4. Instalar e configurar `vue-pdf-embed`.
5. Carregar um PDF de teste.
6. Desenvolver a barra de ferramentas e os controles de navegação.
7. Implementar download, impressão e ajuste de zoom.
8. Avaliar e implementar a pesquisa dentro do documento.
9. Ajustar o layout para celulares e tablets.
10. Publicar na Vercel e testar o acesso pelo link do Portal da Transparência.

## 10. Evoluções futuras

Depois que o leitor estiver funcionando, o projeto poderá evoluir para um painel administrativo protegido, permitindo cadastrar documentos, alterar títulos, atualizar arquivos e organizar publicações por categoria.

Essa etapa deve ser planejada separadamente, pois envolve autenticação, permissões de acesso e armazenamento dos arquivos.

## 11. Decisões que ainda precisam ser definidas

- O leitor exibirá um único documento por endereço ou haverá uma página com catálogo de documentos?
- Os PDFs ficarão hospedados junto ao frontend ou em um serviço externo autorizado?
- Qual conta institucional poderá administrar os documentos e a hospedagem?
- Será possível utilizar um subdomínio oficial da Prefeitura?
- O painel administrativo fará parte da primeira versão ou ficará para uma etapa posterior?

## 12. Direção técnica recomendada

Para começar, utilizar **Vue 3 + Vite + JavaScript + vue-pdf-embed + CSS**, com publicação na **Vercel**.

Essa combinação permite construir o leitor sem adicionar tecnologias desnecessárias à primeira versão. O foco inicial deve ser reproduzir a identidade visual do portal, carregar um PDF corretamente e oferecer uma boa experiência de leitura.
