<script setup>
import logo from '../assets/logo_granito.png'

const ano = new Date().getFullYear()

const linksUteis = [
  { label: 'Perguntas Frequentes', href: '#', icon: 'question' },
  { label: 'Acesso a informação', href: '#', icon: 'info' },
  { label: 'Ouvidoria', href: '#', icon: 'chat' },
  { label: 'Mapa do Site', href: '#', icon: 'sitemap' },
]

const redes = [
  { label: 'Facebook', href: '#', icon: 'facebook' },
  { label: 'Instagram', href: '#', icon: 'instagram' },
  { label: 'YouTube', href: '#', icon: 'youtube' },
]

function topo() {
  window.scrollTo({ top: 0, behavior: 'smooth' })
}
</script>

<template>
  <footer id="rodape" class="footer">
    <div class="footer__main">
      <div class="footer__top">
        <div class="footer__org">
          <img :src="logo" alt="Governo Municipal de Granito" class="footer__logo" />
          <div>
            <p class="footer__poder">PODER EXECUTIVO</p>
            <p class="footer__nome">PREFEITURA MUNICIPAL DE GRANITO</p>
            <p class="footer__cnpj">CNPJ: 11.040.888/0001-02</p>
          </div>
        </div>

        <div class="footer__social">
          <h3>Redes Sociais</h3>
          <div class="footer__social-list">
            <a v-for="r in redes" :key="r.label" :href="r.href" :aria-label="r.label" class="footer__social-btn">
              <svg viewBox="0 0 24 24" aria-hidden="true">
                <path v-if="r.icon === 'facebook'" fill="currentColor" d="M14 8V6.500c0-.700.200-1.200 1.300-1.200H17V2.200C16.700 2.200 15.700 2 14.600 2 12.200 2 10.500 3.500 10.500 6.100V8H8v3.500h2.500V22H14V11.500h2.800L17.200 8H14Z" />
                <g v-else-if="r.icon === 'instagram'" fill="none" stroke="currentColor" stroke-width="2">
                  <rect x="3" y="3" width="18" height="18" rx="5" />
                  <circle cx="12" cy="12" r="4" />
                  <circle cx="17.500" cy="6.500" r="1" fill="currentColor" stroke="none" />
                </g>
                <path v-else fill="currentColor" d="M22 8.200a3 3 0 0 0-2.100-2.100C18.100 5.600 12 5.600 12 5.600s-6.100 0-7.900.5A3 3 0 0 0 2 8.200C1.500 10 1.500 12 1.500 12s0 2 .5 3.800a3 3 0 0 0 2.100 2.100c1.800.5 7.900.5 7.900.5s6.100 0 7.900-.5a3 3 0 0 0 2.100-2.100c.5-1.800.5-3.800.5-3.800s0-2-.5-3.800ZM10 15V9l5.200 3L10 15Z" />
              </svg>
            </a>
          </div>
        </div>
      </div>

      <div class="footer__cols">
        <section>
          <h3>Sobre o Portal</h3>
          <p class="footer__text">
            Portal da Transparência - Compromisso com a transparência e o acesso à informação pública.
          </p>
        </section>

        <section>
          <h3>Links Úteis</h3>
          <ul class="footer__list">
            <li v-for="l in linksUteis" :key="l.label">
              <a :href="l.href">
                <svg class="footer__icon" viewBox="0 0 24 24" aria-hidden="true">
                  <path v-if="l.icon === 'question'" fill="currentColor" d="M12 2a10 10 0 1 0 0 20 10 10 0 0 0 0-20Zm1.100 16h-2.200v-2.200h2.200V18Zm1.800-6.500c-.500.700-1.100 1-1.500 1.400-.3.300-.4.600-.4 1.100h-2c0-.900.200-1.500.700-2 .500-.500 1.300-.800 1.500-1.200.200-.300.200-.600.100-.900-.200-.400-.600-.600-1.100-.600-.700 0-1.200.400-1.300 1.200H8.800c.100-1.700 1.400-2.900 3.200-2.900 1.100 0 2 .400 2.600 1.100.600.800.600 2.100.300 2.900Z" />
                  <path v-else-if="l.icon === 'info'" fill="currentColor" d="M12 2a10 10 0 1 0 0 20 10 10 0 0 0 0-20Zm1.200 15h-2.400v-6h2.400v6ZM12 9.400a1.400 1.400 0 1 1 0-2.800 1.400 1.400 0 0 1 0 2.800Z" />
                  <g v-else-if="l.icon === 'chat'" fill="currentColor">
                    <path d="M9 3C4.600 3 1 5.900 1 9.500c0 1.800.9 3.400 2.400 4.600L2.700 17l3.400-1.700c.9.300 1.900.4 2.900.4 4.400 0 8-2.900 8-6.500S13.400 3 9 3Z" />
                    <path d="M18.500 10.600c.3.700.5 1.400.5 2.100 0 2.200-1.500 4.100-3.800 5.100.9.200 1.800.4 2.800.4.800 0 1.600-.1 2.300-.4L23 19.500l-.6-2.400C23.400 16.300 24 15.200 24 14c0-1.500-.9-2.800-2.200-3.700-.9.100-2.100.2-3.300.3Z" />
                  </g>
                  <g v-else fill="currentColor">
                    <rect x="9" y="2" width="6" height="5" rx="1" />
                    <rect x="1" y="16" width="6" height="5" rx="1" />
                    <rect x="9" y="16" width="6" height="5" rx="1" />
                    <rect x="17" y="16" width="6" height="5" rx="1" />
                    <path d="M11.300 7h1.400v4.500H20V16h-1.500v-3H12.700v3h-1.400v-3H5.500v3H4v-4.500h7.300V7Z" />
                  </g>
                </svg>
                {{ l.label }}
              </a>
            </li>
          </ul>
        </section>

        <section>
          <h3>Endereço</h3>
          <ul class="footer__list">
            <li>
              <svg class="footer__icon" viewBox="0 0 24 24" aria-hidden="true"><path fill="currentColor" d="M12 2a7 7 0 0 0-7 7c0 5.200 7 13 7 13s7-7.800 7-13a7 7 0 0 0-7-7Zm0 9.500A2.500 2.500 0 1 1 12 6.500a2.500 2.500 0 0 1 0 5Z" /></svg>
              Av. José Saraiva Xavier, 95 - Centro
            </li>
            <li>
              <svg class="footer__icon" viewBox="0 0 24 24" aria-hidden="true"><path fill="currentColor" d="M3 21V9h6V3h12v18H3Zm3-2h2v-2H6v2Zm0-4h2v-2H6v2Zm5 4h2v-2h-2v2Zm0-4h2v-2h-2v2Zm0-4h2V9h-2v2Zm0-4h2V5h-2v2Zm5 12h2v-2h-2v2Zm0-4h2v-2h-2v2Zm0-4h2V9h-2v2Zm0-4h2V5h-2v2Z" /></svg>
              Granito-PE
            </li>
            <li>
              <svg class="footer__icon" viewBox="0 0 24 24" aria-hidden="true"><path fill="currentColor" d="M3 6h14v10H3V6Zm2 2v6h10V8H5Zm16 0v10H7v2h16V8h-2ZM2 2h12v2H2V2Z" /></svg>
              56160-000
            </li>
          </ul>
        </section>

        <section>
          <h3>Contato</h3>
          <ul class="footer__list">
            <li>
              <svg class="footer__icon" viewBox="0 0 24 24" aria-hidden="true"><path fill="currentColor" d="M6.600 10.800a15 15 0 0 0 6.600 6.600l2.200-2.200a1 1 0 0 1 1-.250c1.100.360 2.300.550 3.600.550a1 1 0 0 1 1 1V20a1 1 0 0 1-1 1A17 17 0 0 1 3 4a1 1 0 0 1 1-1h3.500a1 1 0 0 1 1 1c0 1.300.200 2.500.550 3.600a1 1 0 0 1-.250 1l-2.200 2.200Z" /></svg>
              <a href="tel:+558738801156">(87) 3880-1156</a>
            </li>
            <li>
              <svg class="footer__icon" viewBox="0 0 24 24" aria-hidden="true"><path fill="currentColor" d="M2 5h20v2l-10 6L2 7V5Zm0 4.300 10 6 10-6V19H2V9.300Z" /></svg>
              <a href="mailto:pmg@granito.pe.gov.br">pmg@granito.pe.gov.br</a>
            </li>
            <li>
              <svg class="footer__icon" viewBox="0 0 24 24" aria-hidden="true"><path fill="currentColor" d="M12 2a10 10 0 1 0 0 20 10 10 0 0 0 0-20Zm4.200 13.500L11 12.400V6h1.800v5.400l4.300 2.600-.9 1.500Z" /></svg>
              <span>de Segunda à Sexta, a partir das 08:00hs às 14:00hs (exceto nos feriados).</span>
            </li>
          </ul>
        </section>
      </div>
    </div>

    <div class="footer__bottom">
      <p>© {{ ano }} Sistema Portal da Transparência. Todos os direitos reservados.</p>
      <p class="footer__dev">Desenvolvido por <strong>Oliveira Góis Tecnologia e Gestão LTDA</strong></p>
    </div>

    <button type="button" class="footer__top-btn" aria-label="Voltar ao topo" @click="topo">
      <svg viewBox="0 0 24 24" aria-hidden="true">
        <path fill="none" stroke="currentColor" stroke-width="2.500" stroke-linecap="round" stroke-linejoin="round" d="M12 20V4m-7 7 7-7 7 7" />
      </svg>
    </button>
  </footer>
</template>

<style scoped>
.footer {
  background: #081d45;
  color: #fff;
  font-family: 'Raleway', sans-serif;
  position: relative;
}

.footer h3 {
  margin: 0 0 1.5rem;
  font-size: 1.4rem;
  font-weight: 500;
}

.footer p {
  margin: 0;
}

.footer a {
  color: inherit;
  text-decoration: none;
}

.footer a:hover,
.footer a:focus-visible {
  text-decoration: underline;
}

.footer__main {
  max-width: 1600px;
  margin: 0 auto;
  padding: 3rem 2rem 2.5rem;
  box-sizing: border-box;
}

.footer__top {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 2rem;
  margin-bottom: 2.5rem;
}

.footer__org {
  display: flex;
  align-items: center;
  gap: 1.1rem;
  padding-top: 1.1rem;
}

.footer__logo {
  width: 242px;
  height: auto;
}

.footer__poder {
  font-size: 1rem;
  letter-spacing: 0.04em;
  margin-bottom: 0.3rem !important;
}

.footer__nome {
  font-size: 1.6rem;
  font-weight: 500;
  letter-spacing: 0.02em;
}

.footer__cnpj {
  font-size: 0.9rem;
  opacity: 0.85;
  margin-top: 0.3rem !important;
}

.footer__social {
  width: 396px;
  max-width: 100%;
}

.footer__social h3 {
  margin-bottom: 1.5rem;
}

.footer__social-list {
  display: flex;
  gap: 0.75rem;
}

.footer__social-btn {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.1);
  display: grid;
  place-items: center;
}

.footer__social-btn:hover {
  background: rgba(255, 255, 255, 0.2);
  text-decoration: none !important;
}

.footer__social-btn svg {
  width: 22px;
  height: 22px;
}

.footer__cols {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 2rem;
}

.footer__text {
  font-size: 1.15rem;
  line-height: 1.65;
  max-width: 24ch;
  max-width: 350px;
}

.footer__list {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
  flex-direction: column;
  gap: 0.8rem;
  font-size: 1.1rem;
  line-height: 1.55;
}

.footer__list li,
.footer__list a {
  display: flex;
  align-items: flex-start;
  gap: 0.6rem;
}

.footer__list li > a {
  display: inline;
}

.footer__icon {
  width: 1.1em;
  height: 1.1em;
  flex-shrink: 0;
  margin-top: 0.3em;
}

.footer__bottom {
  border-top: 1px solid rgba(255, 255, 255, 0.1);
  background: rgba(0, 0, 0, 0.12);
  text-align: center;
  padding: 2rem 1rem 2.2rem;
  font-size: 1.1rem;
}

.footer__dev {
  margin-top: 1rem !important;
  font-size: 1rem;
  opacity: 0.85;
}

.footer__dev strong {
  font-weight: 500;
  color: #fff;
}

.footer__top-btn {
  position: absolute;
  right: 2rem;
  bottom: 2.2rem;
  width: 58px;
  height: 58px;
  border: 0;
  border-radius: 50%;
  background: #134194;
  color: #fff;
  cursor: pointer;
  display: grid;
  place-items: center;
  box-shadow: 0 4px 14px rgba(0, 0, 0, 0.35);
}

.footer__top-btn:hover {
  background: #1a52b8;
}

.footer__top-btn svg {
  width: 26px;
  height: 26px;
}

@media (max-width: 1000px) {
  .footer__cols {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 640px) {
  .footer__main {
    padding: 2rem 1.25rem;
  }

  .footer__top {
    flex-direction: column;
  }

  .footer__org {
    flex-direction: column;
    align-items: flex-start;
    padding-top: 0;
  }

  .footer__nome {
    font-size: 1.25rem;
  }

  .footer__cols {
    grid-template-columns: 1fr;
  }

  .footer__top-btn {
    right: 1rem;
    bottom: 1rem;
    width: 48px;
    height: 48px;
  }

  .footer__bottom {
    padding-bottom: 5rem;
  }
}
</style>
