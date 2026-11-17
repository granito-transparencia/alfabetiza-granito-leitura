<script setup>
import { computed, defineAsyncComponent, reactive, ref } from 'vue'
import { Download, Printer } from 'lucide-vue-next'
import Breadcrumb from '../components/Breadcrumb.vue'

// O leitor (PDF.js) é pesado; carregá-lo à parte faz o cabeçalho e o resto da página aparecerem antes
const PdfViewer = defineAsyncComponent(() => import('../components/PdfViewer.vue'))

// Dados do documento. Na primeira versão ficam fixos aqui;
// depois podem vir de uma lista/catálogo ou de um painel administrativo.
const documento = reactive({
  titulo: 'Plano Municipal de Comunicação da Política de Alfabetização do Território',
  arquivo: '/documentos/plano-municipal-comunicacao-alfabetizacao.pdf',
  formato: 'PDF',
  paginas: null, // lido do próprio PDF quando ele carrega
  atualizadoEm: null, // 'AAAA-MM-DD'. Se ficar null, usa a data de modificação gravada no PDF
})

const nomeArquivo = computed(() => documento.arquivo.split('/').pop())

const dataFormatada = computed(() => {
  if (!documento.atualizadoEm) return null
  return new Date(documento.atualizadoEm + 'T00:00:00').toLocaleDateString('pt-BR')
})

const caminho = [
  { label: 'Início', href: 'https://granito.pe.gov.br/', icone: 'casa' },
  { label: 'Alfabetiza Granito', href: 'https://granito.pe.gov.br/paginas/alfabetiza-granito' },
  { label: documento.titulo },
]

const leitor = ref(null)

function aoCarregar({ paginas, atualizadoEm }) {
  documento.paginas = paginas
  if (!documento.atualizadoEm) documento.atualizadoEm = atualizadoEm
}

function imprimir() {
  leitor.value?.imprimir()
}
</script>

<template>
  <main id="conteudo" class="documento" tabindex="-1">
    <div class="documento__inner">
      <Breadcrumb :itens="caminho" />

      <header class="documento__cabecalho">
        <div class="documento__info">
          <h1 class="documento__titulo">{{ documento.titulo }}</h1>

          <ul class="documento__meta">
            <li>
              <span class="documento__selo">{{ documento.formato }}</span>
            </li>
            <li v-if="documento.paginas">
              {{ documento.paginas }} {{ documento.paginas === 1 ? 'página' : 'páginas' }}
            </li>
            <li v-if="dataFormatada">Atualizado em {{ dataFormatada }}</li>
          </ul>
        </div>

        <!-- <div class="documento__acoes">
          <a :href="documento.arquivo" :download="nomeArquivo" class="botao botao--primario">
            <Download aria-hidden="true" />
            Baixar PDF
          </a>
          <button type="button" class="botao botao--secundario" @click="imprimir">
            <Printer aria-hidden="true" />
            Imprimir
          </button>
        </div> -->
      </header>

      <section class="documento__leitor" aria-label="Visualizador do documento">
        <PdfViewer
          ref="leitor"
          :url="documento.arquivo"
          :nome-arquivo="nomeArquivo"
          @carregado="aoCarregar"
        />
      </section>
    </div>
  </main>
</template>

<style scoped>
.documento:focus {
  outline: none; /* recebe foco só pelo atalho "Ir para o conteúdo" */
}

.documento {
  background: #f3f5f9;
  color: #1f2937;
  font-family: 'Raleway', sans-serif;
}

.documento__inner {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.documento__cabecalho {
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
  gap: 1.5rem;
  padding-bottom: 1.5rem;
  border-bottom: 1px solid #dde3ec;
}

.documento__titulo {
  margin: 0 0 0.75rem;
  font-size: 1.75rem;
  font-weight: 700;
  line-height: 1.3;
  color: #081d45;
}

.documento__meta {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 0.5rem 1rem;
  font-size: 0.95rem;
  color: #4a5568;
}

.documento__selo {
  display: inline-block;
  padding: 0.15rem 0.6rem;
  border-radius: 4px;
  background: #134194;
  color: #fff;
  font-size: 0.8rem;
  font-weight: 700;
  letter-spacing: 0.05em;
}

.documento__acoes {
  display: flex;
  gap: 0.75rem;
  flex-shrink: 0;
}

.botao {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  padding: 0.7rem 1.2rem;
  border-radius: 6px;
  font: inherit;
  font-size: 1rem;
  font-weight: 500;
  text-decoration: none;
  cursor: pointer;
  border: 2px solid #134194;
}

.botao svg {
  width: 1.15em;
  height: 1.15em;
}

.botao--primario {
  background: #134194;
  color: #fff;
}

.botao--primario:hover {
  background: #1a52b8;
  border-color: #1a52b8;
}

.botao--secundario {
  background: #fff;
  color: #134194;
}

.botao--secundario:hover {
  background: #eaf0fb;
}

.botao:focus-visible {
  outline: 3px solid #f2b705;
  outline-offset: 2px;
}

@media (max-width: 800px) {
  .documento__inner {
    padding: 1.25rem 1rem;
  }

  .documento__cabecalho {
    flex-direction: column;
    align-items: stretch;
  }

  .documento__titulo {
    font-size: 1.35rem;
  }

  .documento__acoes .botao {
    flex: 1;
  }
}

/* Celulares: o leitor ocupa a largura toda da tela */
@media (max-width: 640px) {
  .documento__leitor {
    margin: 0 -1rem;
  }
}
</style>
