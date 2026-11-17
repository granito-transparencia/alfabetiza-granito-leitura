<script setup>
defineProps({
  // Lista de níveis: [{ label, href }]. O último item é a página atual.
  itens: { type: Array, required: true },
})
</script>

<template>
  <nav class="breadcrumb" aria-label="Caminho de navegação">
    <ol class="breadcrumb__lista">
      <li v-for="(item, i) in itens" :key="item.label" class="breadcrumb__item">
        <a
          v-if="i < itens.length - 1 && item.href"
          :href="item.href"
          class="breadcrumb__link"
          :class="{ 'breadcrumb__link--icone': item.icone }"
          :title="item.icone ? item.label : undefined"
        >
          <!-- Ícone de casinha no lugar do texto; o texto fica só para leitores de tela -->
          <template v-if="item.icone === 'casa'">
            <svg class="breadcrumb__casa" viewBox="0 0 24 24" aria-hidden="true">
              <path fill="currentColor" d="M12 2.5 1 12h3v9.5h6v-6h4v6h6V12h3L12 2.5Z" />
            </svg>
            <span class="sr-only">{{ item.label }}</span>
          </template>
          <template v-else>{{ item.label }}</template>
        </a>
        <span v-else aria-current="page" class="breadcrumb__atual">{{ item.label }}</span>

        <svg v-if="i < itens.length - 1" class="breadcrumb__sep" viewBox="0 0 24 24" aria-hidden="true">
          <path fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" d="m9 18 6-6-6-6" />
        </svg>
      </li>
    </ol>
  </nav>
</template>

<style scoped>
.breadcrumb__lista {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 0.25rem;
  font-size: 0.95rem;
}

.breadcrumb__item {
  display: inline-flex;
  align-items: center;
  gap: 0.25rem;
  min-width: 0;
}

.breadcrumb__link {
  color: #134194;
  text-decoration: none;
  font-weight: 500;
}

.breadcrumb__link:hover,
.breadcrumb__link:focus-visible {
  text-decoration: underline;
}

.breadcrumb__link--icone {
  display: inline-flex;
  align-items: center;
}

.breadcrumb__casa {
  width: 1.35em;
  height: 1.35em;
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

.breadcrumb__atual {
  color: #4a5568;
  overflow-wrap: anywhere;
}

.breadcrumb__sep {
  width: 1em;
  height: 1em;
  color: #8a94a6;
  flex-shrink: 0;
}
</style>
