<script setup>
import { computed, nextTick, onBeforeUnmount, onMounted, ref, watch } from 'vue'
import VuePdfEmbed, { usePdfDocument } from 'vue-pdf-embed'
import 'vue-pdf-embed/dist/styles/textLayer.css'
import 'vue-pdf-embed/dist/styles/annotationLayer.css'
import { LoaderCircle, TriangleAlert, Download } from 'lucide-vue-next'
import PdfToolbar from './PdfToolbar.vue'

const props = defineProps({
  url: { type: String, required: true },
  nomeArquivo: { type: String, default: 'documento.pdf' },
})

const emit = defineEmits(['carregado'])

const ID = 'pdf-leitor' // as páginas recebem os ids pdf-leitor-1, pdf-leitor-2, ...
const NIVEIS_ZOOM = [0.25, 0.5, 0.75, 1, 1.25, 1.5, 2, 2.5, 3]
const MAX_PIXELS_CANVAS = 16_000_000 // limite seguro para navegadores móveis (iOS)

/* ---------- Carregamento do documento ---------- */

const erro = ref(null)
const progresso = ref(0)
const pronto = ref(false) // true depois da primeira renderização

const { doc, print } = usePdfDocument({
  source: computed(() => props.url),
  onError: (e) => (erro.value = e),
  onProgress: ({ loaded, total }) => {
    if (total) progresso.value = Math.min(100, Math.round((loaded / total) * 100))
  },
})

const totalPaginas = ref(0)
const larguraNatural = ref(0) // largura da página a 100% (px CSS)
const alturaNatural = ref(0)

// Converte a data do PDF ("D:20261103111127-03'00'") para "2026-11-03"
function dataDoPdf(valor) {
  const m = /^D:(\d{4})(\d{2})?(\d{2})?/.exec(valor || '')
  return m ? `${m[1]}-${m[2] || '01'}-${m[3] || '01'}` : null
}

watch(doc, async (d) => {
  if (!d) return
  const pagina = await d.getPage(1)
  const [x0, y0, x1, y1] = pagina.view
  const deitada = pagina.rotate % 180 !== 0
  const pontosParaPx = 96 / 72
  larguraNatural.value = (deitada ? y1 - y0 : x1 - x0) * pontosParaPx
  alturaNatural.value = (deitada ? x1 - x0 : y1 - y0) * pontosParaPx
  totalPaginas.value = d.numPages

  let atualizadoEm = null
  try {
    const { info } = await d.getMetadata()
    atualizadoEm = dataDoPdf(info?.ModDate || info?.CreationDate)
  } catch {
    // metadados são opcionais
  }
  emit('carregado', { paginas: d.numPages, atualizadoEm })
})

/* ---------- Largura disponível e zoom ---------- */

const areaEl = ref(null)
const larguraDisponivel = ref(0)
let observador = null
let timerResize = null

function medirArea() {
  const el = areaEl.value
  if (!el) return
  const estilo = getComputedStyle(el)
  const padding = parseFloat(estilo.paddingLeft) + parseFloat(estilo.paddingRight)
  larguraDisponivel.value = Math.max(200, Math.floor(el.clientWidth - padding))
}

const modoLargura = ref(true) // "Ajustar à largura"
const escala = ref(1) // usado quando modoLargura = false

const larguraRender = computed(() => {
  if (!larguraDisponivel.value || !larguraNatural.value) return 0
  return Math.round(modoLargura.value ? larguraDisponivel.value : larguraNatural.value * escala.value)
})

const percentual = computed(() =>
  larguraNatural.value ? Math.round((larguraRender.value / larguraNatural.value) * 100) : 100,
)

// Reduz a resolução do canvas em zooms muito altos para não estourar a memória
const escalaRender = computed(() => {
  if (!larguraRender.value || !larguraNatural.value) return 1
  const dpr = window.devicePixelRatio || 1
  const altura = (larguraRender.value * alturaNatural.value) / larguraNatural.value
  const pixels = larguraRender.value * altura * dpr * dpr
  return pixels > MAX_PIXELS_CANVAS ? Math.sqrt(MAX_PIXELS_CANVAS / pixels) : 1
})

const podeAumentar = computed(() => percentual.value / 100 < NIVEIS_ZOOM.at(-1) - 0.01)
const podeDiminuir = computed(() => percentual.value / 100 > NIVEIS_ZOOM[0] + 0.01)

function zoomMais() {
  const atual = percentual.value / 100
  const proximo = NIVEIS_ZOOM.find((n) => n > atual + 0.01)
  if (proximo) {
    modoLargura.value = false
    escala.value = proximo
  }
}

function zoomMenos() {
  const atual = percentual.value / 100
  const anterior = [...NIVEIS_ZOOM].reverse().find((n) => n < atual - 0.01)
  if (anterior) {
    modoLargura.value = false
    escala.value = anterior
  }
}

function ajustarLargura() {
  modoLargura.value = true
}

// Mantém a posição de leitura quando o zoom ou a largura mudam
let proporcaoRolagem = null
watch(larguraRender, (novo, antigo) => {
  const el = areaEl.value
  if (antigo && pronto.value && el && el.scrollHeight) {
    proporcaoRolagem = el.scrollTop / el.scrollHeight
  }
})

/* ---------- Página atual e navegação ---------- */

const paginaAtual = ref(1)
let quadroRolagem = 0

function atualizarPaginaAtual() {
  quadroRolagem = 0
  const el = areaEl.value
  if (!el) return
  if (el.scrollTop + el.clientHeight >= el.scrollHeight - 2) {
    paginaAtual.value = totalPaginas.value || 1
    return
  }
  const linha = el.getBoundingClientRect().top + el.clientHeight * 0.3
  const paginas = el.querySelectorAll('.vue-pdf-embed__page')
  let atual = 1
  paginas.forEach((p, i) => {
    if (p.getBoundingClientRect().top <= linha) atual = i + 1
  })
  paginaAtual.value = atual
}

function aoRolar() {
  if (!quadroRolagem) quadroRolagem = requestAnimationFrame(atualizarPaginaAtual)
}

function irParaPagina(n, comportamento = 'smooth') {
  const el = areaEl.value
  const pagina = el?.querySelector(`#${ID}-${n}`)
  if (!pagina) return
  const deslocamento = pagina.getBoundingClientRect().top - el.getBoundingClientRect().top
  el.scrollTo({ top: el.scrollTop + deslocamento - 16, behavior: comportamento })
  paginaAtual.value = n
}

function aoRenderizar() {
  pronto.value = true
  if (proporcaoRolagem !== null) {
    const el = areaEl.value
    const proporcao = proporcaoRolagem
    proporcaoRolagem = null
    nextTick(() => {
      el.scrollTop = proporcao * el.scrollHeight
      atualizarPaginaAtual()
    })
  }
}

// Esc fecha a tela cheia no modo CSS (no modo do navegador, o próprio navegador cuida disso)
function aoTeclar(e) {
  if (e.key === 'Escape' && telaCheiaCss.value) {
    telaCheiaCss.value = false
    telaCheia.value = false
  }
}

/* ---------- Impressão ---------- */

const imprimindo = ref(false)

async function imprimir() {
  if (!doc.value || imprimindo.value) return
  imprimindo.value = true
  try {
    await print(200, props.nomeArquivo)
  } finally {
    imprimindo.value = false
  }
}

defineExpose({ imprimir })

/* ---------- Tela cheia ---------- */

const leitorEl = ref(null)
const telaCheia = ref(false)
// iPhone (Safari) não permite tela cheia em elementos: nesse caso o leitor
// passa a ocupar a janela toda via CSS (classe leitor--tela-cheia)
const suportaApiTelaCheia = typeof document !== 'undefined' && !!document.fullscreenEnabled
const telaCheiaCss = ref(false)

async function alternarTelaCheia() {
  if (suportaApiTelaCheia) {
    try {
      if (document.fullscreenElement) await document.exitFullscreen()
      else await leitorEl.value.requestFullscreen()
    } catch {
      telaCheiaCss.value = !telaCheiaCss.value
    }
  } else {
    telaCheiaCss.value = !telaCheiaCss.value
  }
  if (!suportaApiTelaCheia || !document.fullscreenElement) telaCheia.value = telaCheiaCss.value
}

function aoMudarTelaCheia() {
  telaCheia.value = document.fullscreenElement === leitorEl.value
}

// Trava a rolagem da página por trás enquanto o modo CSS está ativo
watch(telaCheiaCss, (ativo) => {
  document.documentElement.style.overflow = ativo ? 'hidden' : ''
})

/* ---------- Ciclo de vida ---------- */

onMounted(() => {
  medirArea()
  observador = new ResizeObserver(() => {
    clearTimeout(timerResize)
    timerResize = setTimeout(medirArea, 150)
  })
  observador.observe(areaEl.value)
  window.addEventListener('keydown', aoTeclar)
  document.addEventListener('fullscreenchange', aoMudarTelaCheia)
})

onBeforeUnmount(() => {
  observador?.disconnect()
  clearTimeout(timerResize)
  cancelAnimationFrame(quadroRolagem)
  window.removeEventListener('keydown', aoTeclar)
  document.removeEventListener('fullscreenchange', aoMudarTelaCheia)
  document.documentElement.style.overflow = ''
})
</script>

<template>
  <div ref="leitorEl" class="leitor" :class="{ 'leitor--tela-cheia': telaCheiaCss }">
    <PdfToolbar
      :pagina-atual="paginaAtual"
      :total-paginas="totalPaginas"
      :percentual="percentual"
      :modo-largura="modoLargura"
      :pode-aumentar="podeAumentar"
      :pode-diminuir="podeDiminuir"
      :url-download="url"
      :nome-arquivo="nomeArquivo"
      :desabilitada="!pronto"
      :tela-cheia="telaCheia"
      @tela-cheia="alternarTelaCheia"
      @anterior="irParaPagina(paginaAtual - 1)"
      @proxima="irParaPagina(paginaAtual + 1)"
      @ir-para="irParaPagina"
      @zoom-mais="zoomMais"
      @zoom-menos="zoomMenos"
      @ajustar-largura="ajustarLargura"
      @imprimir="imprimir"
    />

    <div
      ref="areaEl"
      class="leitor__area"
      tabindex="0"
      aria-label="Páginas do documento"
      @scroll.passive="aoRolar"
    >
      <div v-if="erro" class="leitor__estado leitor__estado--erro" role="alert">
        <TriangleAlert aria-hidden="true" />
        <p>Não foi possível exibir o documento neste navegador.</p>
        <a :href="url" :download="nomeArquivo" class="leitor__link">
          <Download aria-hidden="true" /> Baixar o PDF
        </a>
      </div>

      <template v-else>
        <div v-if="!pronto" class="leitor__estado" role="status">
          <LoaderCircle class="leitor__girando" aria-hidden="true" />
          <p>Carregando documento… {{ progresso ? `${progresso}%` : '' }}</p>
        </div>

        <VuePdfEmbed
          v-if="doc && larguraRender"
          :id="ID"
          :source="doc"
          :width="larguraRender"
          :scale="escalaRender"
          text-layer
          annotation-layer
          @rendered="aoRenderizar"
          @rendering-failed="(e) => (erro = e)"
          @internal-link-clicked="irParaPagina"
        />
      </template>
    </div>

    <div v-if="imprimindo" class="leitor__aviso" role="status">
      <LoaderCircle class="leitor__girando" aria-hidden="true" /> Preparando impressão…
    </div>
  </div>
</template>

<style scoped>
.leitor {
  position: relative;
  border: 1px solid #dde3ec;
  border-radius: 8px;
  overflow: hidden;
  background: #e5e9f0;
}

/* Tela cheia: pela API do navegador (:fullscreen) ou pelo modo CSS (iPhone) */
.leitor:fullscreen,
.leitor--tela-cheia {
  display: flex;
  flex-direction: column;
  width: 100%;
  height: 100%;
  border: 0;
  border-radius: 0;
}

.leitor--tela-cheia {
  position: fixed;
  inset: 0;
  z-index: 1000;
  height: 100dvh;
}

.leitor:fullscreen .leitor__area,
.leitor--tela-cheia .leitor__area {
  flex: 1;
  height: auto;
  min-height: 0;
}

.leitor__area {
  position: relative;
  height: 80vh;
  min-height: 480px;
  padding: 1rem;
  overflow: auto;
  scrollbar-gutter: stable;
  box-sizing: border-box;
}

.leitor__area:focus-visible {
  outline: 3px solid #f2b705;
  outline-offset: -3px;
}

.leitor__area :deep(.vue-pdf-embed__page) {
  width: fit-content;
  margin: 0 auto 1rem;
  background: #fff;
  box-shadow: 0 1px 4px rgba(8, 29, 69, 0.18);
}

.leitor__estado {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 0.75rem;
  padding: 1rem;
  text-align: center;
  color: #4a5568;
  background: #e5e9f0;
  z-index: 1;
}

.leitor__estado p {
  margin: 0;
}

.leitor__estado svg {
  width: 32px;
  height: 32px;
}

.leitor__estado--erro svg {
  color: #b45309;
}

.leitor__link {
  display: inline-flex;
  align-items: center;
  gap: 0.4rem;
  padding: 0.6rem 1rem;
  border-radius: 6px;
  background: #134194;
  color: #fff;
  text-decoration: none;
  font-weight: 500;
}

.leitor__link svg {
  width: 18px;
  height: 18px;
}

.leitor__aviso {
  position: absolute;
  left: 50%;
  bottom: 1rem;
  transform: translateX(-50%);
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.6rem 1rem;
  border-radius: 999px;
  background: #081d45;
  color: #fff;
  font-size: 0.9rem;
  box-shadow: 0 4px 14px rgba(0, 0, 0, 0.25);
  z-index: 2;
}

.leitor__aviso svg {
  width: 18px;
  height: 18px;
}

.leitor__girando {
  animation: girar 1s linear infinite;
}

@keyframes girar {
  to {
    transform: rotate(360deg);
  }
}

@media (prefers-reduced-motion: reduce) {
  .leitor__girando {
    animation: none;
  }
}

@media (max-width: 640px) {
  .leitor {
    border-radius: 0;
    border-left: 0;
    border-right: 0;
  }

  .leitor__area {
    height: 75vh;
    min-height: 400px;
    padding: 0.5rem;
  }

  .leitor__area :deep(.vue-pdf-embed__page) {
    margin-bottom: 0.5rem;
  }
}
</style>
