<script setup>
import logo from '../assets/logo_granito.png'
import {
  ArrowRight,
  Menu,
  ChevronDown,
  CircleHelp,
  Network,
  Accessibility,
  Contrast,
  Mail,
  Lock,
} from 'lucide-vue-next'

// Barra superior: reprodução visual do portal oficial (decorativa).
// Só os atalhos "Ir para" funcionam, levando às partes da própria página.
const atalhos = [
  { label: 'Ir para o conteúdo', href: '#conteudo', icon: ArrowRight },
  { label: 'Ir para o menu', href: '#menu', icon: Menu },
  { label: 'Ir para o rodapé', href: '#rodape', icon: ChevronDown },
]

const utilidades = [
  { label: 'FAQ', icon: CircleHelp },
  { label: 'Mapa do Site', icon: Network },
  { label: 'Acessibilidade', icon: Accessibility },
]

defineProps({
  usuario: { type: String, default: 'Secretária de Educação' },
})

const links = [
  { label: 'Portal da Transparência', href: '#', icon: 'chart' },
  { label: 'e-Sic', href: '#', icon: 'info' },
  { label: 'Ouvidoria', href: '#', icon: 'chat' },
]

function saudacao() {
  const h = new Date().getHours()
  if (h < 12) return 'Olá, bom dia,'
  if (h < 18) return 'Olá, boa tarde,'
  return 'Olá, boa noite,'
}
</script>

<template>
  <header class="header">
    <div class="topo">
      <div class="topo__inner">
        <nav class="topo__atalhos" aria-label="Atalhos">
          <a v-for="a in atalhos" :key="a.label" :href="a.href" class="topo__item">
            <component :is="a.icon" class="topo__icone" aria-hidden="true" />
            {{ a.label }}
          </a>
        </nav>

        <div class="topo__direita">
          <span v-for="u in utilidades" :key="u.label" class="topo__item">
            <component :is="u.icon" class="topo__icone" aria-hidden="true" />
            {{ u.label }}
          </span>

          <span class="topo__sep" aria-hidden="true"></span>

          <span class="topo__fonte" aria-hidden="true">
            <span>A-</span>
            <span class="topo__fonte-normal">A</span>
            <span>A+</span>
          </span>

          <span class="topo__sep" aria-hidden="true"></span>

          <span class="topo__item">
            <Contrast class="topo__icone" aria-hidden="true" />
            Alto Contraste
          </span>
          <span class="topo__item">
            <Mail class="topo__icone" aria-hidden="true" />
            Webmail
          </span>
          <span class="topo__item topo__item--cadeado" title="Área restrita">
            <Lock class="topo__icone" aria-hidden="true" />
          </span>
        </div>
      </div>
    </div>

    <div class="header__inner">
      <a class="header__brand" href="#" aria-label="Governo Municipal de Granito">
        <img :src="logo" alt="Governo Municipal de Granito — Juntos construímos o futuro." />
      </a>

      <div class="header__right">
        <!-- <p class="header__user">
          <span class="header__greeting"><span aria-hidden="true">👋</span> {{ saudacao() }}</span>
          <strong>{{ usuario }}!</strong>
        </p> -->

        <nav id="menu" class="header__nav" aria-label="Links institucionais">
          <a v-for="l in links" :key="l.label" :href="l.href" class="header__link">
            <svg class="header__icon" viewBox="0 0 24 24" aria-hidden="true">
              <!-- gráfico -->
              <g v-if="l.icon === 'chart'" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <path d="M3 3v18h18" />
                <path d="m7 15 4-5 3 3 6-7" />
              </g>
              <!-- info -->
              <g v-else-if="l.icon === 'info'">
                <path fill="currentColor" d="M12 2a10 10 0 1 0 0 20 10 10 0 0 0 0-20Zm1.2 15h-2.4v-6h2.4v6ZM12 9.4a1.4 1.4 0 1 1 0-2.8 1.4 1.4 0 0 1 0 2.8Z" />
              </g>
              <!-- chat -->
              <g v-else fill="currentColor">
                <path d="M9 3C4.6 3 1 5.9 1 9.5c0 1.8.9 3.4 2.4 4.6L2.7 17l3.4-1.7c.9.3 1.9.4 2.9.4 4.4 0 8-2.9 8-6.5S13.400 3 9 3Z" />
                <path d="M18.500 10.600c.3.700.5 1.400.5 2.100 0 2.200-1.500 4.100-3.800 5.100.9.2 1.800.4 2.800.4.800 0 1.600-.1 2.300-.4L23 19.500l-.6-2.400C23.400 16.300 24 15.200 24 14c0-1.500-.9-2.800-2.200-3.700-.9.100-2.100.2-3.300.3Z" />
              </g>
            </svg>
            {{ l.label }}
          </a>
        </nav>
      </div>
    </div>
  </header>
</template>

<style>
body {
  margin: 0;
  font-family: 'Raleway', sans-serif;
}
</style>

<style scoped>
.header {
  background: #134194;
  color: #fff;
  font-family: 'Raleway', sans-serif;
}

/* ---------- Barra superior (decorativa) ---------- */

.topo__inner {
  max-width: 1500px;
  margin: 0 auto;
  padding: 0 2rem;
  box-sizing: border-box;
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 1rem;
  min-height: 40px;
  position: relative;
}

/* linha fina abaixo da barra, só na largura do conteúdo */
.topo__inner::after {
  content: '';
  position: absolute;
  left: 2rem;
  right: 2rem;
  bottom: 0;
  border-bottom: 1px solid rgba(255, 255, 255, 0.2);
}

.topo__atalhos,
.topo__direita {
  display: flex;
  align-items: center;
  gap: 1.6rem;
}

.topo__item {
  display: inline-flex;
  align-items: center;
  gap: 0.3rem;
  color: #fff;
  text-decoration: none;
  font-size: 1rem;
  white-space: nowrap;
  opacity: 0.95;
}

a.topo__item:hover,
a.topo__item:focus-visible {
  text-decoration: underline;
}

.topo__icone {
  width: 1em;
  height: 1em;
  flex-shrink: 0;
}

.topo__item--cadeado {
  padding-right: 0.5rem;
}

.topo__sep {
  align-self: stretch;
  width: 1px;
  background: rgba(255, 255, 255, 0.2);
}

.topo__fonte {
  display: inline-flex;
  align-items: center;
  gap: 1.2rem;
  font-size: 1rem;
}

.topo__fonte-normal {
  font-size: 1.1rem;
}

@media (max-width: 1400px) {
  .topo__atalhos {
    display: none;
  }

  .topo__inner {
    justify-content: flex-end;
  }
}

@media (max-width: 800px) {
  .topo {
    display: none;
  }
}

/* ---------- Faixa principal ---------- */

.header__inner {
  max-width: 1500px;
  margin: 0 auto;
  padding: 1.5rem 2rem;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1.5rem;
  min-height: 172px;
  box-sizing: border-box;
}

.header__brand img {
  display: block;
  width: 348px;
  max-width: 100%;
  height: auto;
}

.header__right {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  gap: 2.5rem;
}

.header__user {
  margin: 0;
  text-align: right;
  font-size: 0.95rem;
  line-height: 1.4;
}

.header__greeting {
  display: block;
  font-weight: 400;
  opacity: 0.9;
}

.header__user strong {
  font-weight: 700;
}

.header__nav {
  display: flex;
  gap: 2rem;
}

.header__link {
  display: inline-flex;
  align-items: center;
  gap: 0.4rem;
  color: #fff;
  text-decoration: none;
  font-size: 1.15rem;
  font-weight: 500;
}

.header__link:hover,
.header__link:focus-visible {
  text-decoration: underline;
}

.header__icon {
  width: 1.2em;
  height: 1.2em;
  flex-shrink: 0;
}

@media (max-width: 800px) {
  .header__inner {
    flex-direction: column;
    align-items: center;
    padding: 1rem;
  }

  .header__brand img {
    width: 280px;
  }

  .header__right {
    align-items: center;
    gap: 1rem;
  }

  .header__user {
    text-align: center;
  }

  .header__nav {
    flex-wrap: wrap;
    justify-content: center;
    gap: 0.75rem 1.25rem;
  }

  .header__link {
    font-size: 1rem;
  }
}
</style>
