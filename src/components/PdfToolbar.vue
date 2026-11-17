<script setup>
import { ref, watch } from 'vue'
import {
  ChevronLeft,
  ChevronRight,
  ZoomIn,
  ZoomOut,
  MoveHorizontal,
  Printer,
  Download,
  Maximize,
  Minimize,
} from 'lucide-vue-next'

const props = defineProps({
  paginaAtual: { type: Number, default: 1 },
  totalPaginas: { type: Number, default: 0 },
  percentual: { type: Number, default: 100 },
  modoLargura: { type: Boolean, default: true },
  podeDiminuir: { type: Boolean, default: true },
  podeAumentar: { type: Boolean, default: true },
  urlDownload: { type: String, required: true },
  nomeArquivo: { type: String, default: 'documento.pdf' },
  desabilitada: { type: Boolean, default: false },
  telaCheia: { type: Boolean, default: false },
})

const emit = defineEmits([
  'anterior',
  'proxima',
  'ir-para',
  'zoom-menos',
  'zoom-mais',
  'ajustar-largura',
  'imprimir',
  'tela-cheia',
])

// Campo de página: cópia local para o usuário poder digitar livremente
const paginaDigitada = ref(String(props.paginaAtual))
watch(
  () => props.paginaAtual,
  (n) => (paginaDigitada.value = String(n)),
)

function confirmarPagina() {
  const n = parseInt(paginaDigitada.value, 10)
  if (Number.isNaN(n)) {
    paginaDigitada.value = String(props.paginaAtual)
    return
  }
  const destino = Math.min(Math.max(n, 1), props.totalPaginas || 1)
  paginaDigitada.value = String(destino)
  emit('ir-para', destino)
}
</script>

<template>
  <div class="toolbar" role="toolbar" aria-label="Ferramentas do documento">
    <!-- Navegação entre páginas -->
    <div class="toolbar__grupo toolbar__grupo--paginas">
      <button
        type="button"
        class="toolbar__btn"
        :disabled="desabilitada || paginaAtual <= 1"
        aria-label="Página anterior"
        title="Página anterior"
        @click="emit('anterior')"
      >
        <ChevronLeft aria-hidden="true" />
      </button>

      <label class="toolbar__pagina">
        <span class="sr-only">Página atual</span>
        <input
          v-model="paginaDigitada"
          type="text"
          inputmode="numeric"
          class="toolbar__input-pagina"
          :disabled="desabilitada"
          @keydown.enter.prevent="confirmarPagina"
          @blur="confirmarPagina"
        />
        <span aria-hidden="true">de {{ totalPaginas || '–' }}</span>
        <span class="sr-only">de {{ totalPaginas }} páginas</span>
      </label>

      <button
        type="button"
        class="toolbar__btn"
        :disabled="desabilitada || paginaAtual >= totalPaginas"
        aria-label="Próxima página"
        title="Próxima página"
        @click="emit('proxima')"
      >
        <ChevronRight aria-hidden="true" />
      </button>
    </div>

    <!-- Zoom -->
    <div class="toolbar__grupo toolbar__grupo--zoom">
      <button
        type="button"
        class="toolbar__btn"
        :disabled="desabilitada || !podeDiminuir"
        aria-label="Diminuir zoom"
        title="Diminuir zoom"
        @click="emit('zoom-menos')"
      >
        <ZoomOut aria-hidden="true" />
      </button>
      <output class="toolbar__zoom" aria-live="polite" :aria-label="`Zoom em ${percentual}%`">
        {{ percentual }}%
      </output>
      <button
        type="button"
        class="toolbar__btn"
        :disabled="desabilitada || !podeAumentar"
        aria-label="Aumentar zoom"
        title="Aumentar zoom"
        @click="emit('zoom-mais')"
      >
        <ZoomIn aria-hidden="true" />
      </button>
      <button
        type="button"
        class="toolbar__btn toolbar__btn--texto"
        :class="{ 'toolbar__btn--ativo': modoLargura }"
        :aria-pressed="modoLargura"
        :disabled="desabilitada"
        title="Ajustar à largura"
        @click="emit('ajustar-largura')"
      >
        <MoveHorizontal aria-hidden="true" />
        <span class="toolbar__rotulo">Ajustar à largura</span>
      </button>
    </div>

    <!-- Imprimir, baixar e tela cheia -->
    <div class="toolbar__grupo toolbar__grupo--acoes">
      <button
        type="button"
        class="toolbar__btn"
        :disabled="desabilitada"
        aria-label="Imprimir"
        title="Imprimir"
        @click="emit('imprimir')"
      >
        <Printer aria-hidden="true" />
      </button>
      <a
        :href="urlDownload"
        :download="nomeArquivo"
        class="toolbar__btn"
        aria-label="Baixar PDF"
        title="Baixar PDF"
      >
        <Download aria-hidden="true" />
      </a>
      <button
        type="button"
        class="toolbar__btn"
        :aria-pressed="telaCheia"
        :aria-label="telaCheia ? 'Sair da tela cheia' : 'Tela cheia'"
        :title="telaCheia ? 'Sair da tela cheia (Esc)' : 'Tela cheia'"
        @click="emit('tela-cheia')"
      >
        <Minimize v-if="telaCheia" aria-hidden="true" />
        <Maximize v-else aria-hidden="true" />
      </button>
    </div>
  </div>
</template>

<style scoped>
.toolbar {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: space-between;
  gap: 0.5rem 1rem;
  padding: 0.5rem 0.75rem;
  background: #134194;
  color: #fff;
  font-size: 0.95rem;
}

.toolbar__grupo {
  display: flex;
  align-items: center;
  gap: 0.25rem;
}

.toolbar__btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 0.4rem;
  min-width: 40px;
  height: 40px;
  padding: 0 0.5rem;
  border: 0;
  border-radius: 6px;
  background: transparent;
  color: inherit;
  font: inherit;
  text-decoration: none;
  cursor: pointer;
  box-sizing: border-box;
}

.toolbar__btn svg {
  width: 20px;
  height: 20px;
  flex-shrink: 0;
}

.toolbar__btn:hover:not(:disabled) {
  background: rgba(255, 255, 255, 0.15);
}

.toolbar__btn:disabled {
  opacity: 0.4;
  cursor: not-allowed;
}

.toolbar__btn--ativo {
  background: rgba(255, 255, 255, 0.2);
}

.toolbar__btn:focus-visible,
.toolbar input:focus-visible {
  outline: 3px solid #f2b705;
  outline-offset: 1px;
}

.toolbar__pagina {
  display: inline-flex;
  align-items: center;
  gap: 0.4rem;
  white-space: nowrap;
}

.toolbar__input-pagina {
  width: 3rem;
  height: 32px;
  border: 0;
  border-radius: 4px;
  text-align: center;
  font: inherit;
  color: #081d45;
}

.toolbar__zoom {
  min-width: 3.5rem;
  text-align: center;
  font-variant-numeric: tabular-nums;
}

.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}

/* Celulares */
@media (max-width: 640px) {
  .toolbar {
    padding: 0.5rem;
    justify-content: center;
  }

  .toolbar__rotulo {
    display: none;
  }

  .toolbar__grupo--zoom .toolbar__zoom {
    min-width: 3rem;
  }
}
</style>
