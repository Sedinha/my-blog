---
title: 'Como implementamos uma animação de Hero sofisticada no Astro'
description: 'Relato técnico detalhado sobre a criação do componente HeroTransition.astro, desafios, arquitetura e lições aprendidas.'
pubDate: '2024-06-19'
heroImage: '../../assets/blog-placeholder-1.jpg'
tags: ['astro', 'animação', 'intersection-observer', 'frontend', 'case-study']
---

Neste artigo, vamos compartilhar em detalhes como foi o processo de implementação do componente `HeroTransition.astro` em nosso blog Astro. O objetivo era criar uma transição visualmente impressionante, onde um SVG grande do personagem na seção do herói se transforma suavemente em um logo simples na barra de navegação à medida que o usuário rola a página.

Além de abordar a arquitetura e as técnicas utilizadas, também relatamos os principais desafios enfrentados — como a quebra do header em páginas internas — e como solucionamos cada um deles.

---

## Estrutura do Projeto

O componente foi planejado para ser autônomo, performático e facilmente integrável a qualquer página do blog. A estrutura principal ficou assim:

- **Header fixo**: Com o logo e navegação, posicionado no topo.
- **Hero sticky**: Uma seção de herói ocupando 100vh, com SVG grande, título e CTAs.
- **Seção gatilho**: Um bloco de conteúdo que serve de trigger para a animação de transição.

---

## Tecnologias e APIs Utilizadas

- **Intersection Observer API**: Para detectar o progresso da rolagem de forma performática.
- **SVG inline**: Para permitir manipulação direta de escala, posição e opacidade via CSS/JS.
- **CSS `position: sticky`**: Para manter o hero fixo enquanto o conteúdo seguinte rola por cima.
- **requestAnimationFrame**: Para garantir animações suaves e sem travamentos.

---

## Passo a Passo da Implementação

### 1. Estrutura HTML e CSS

Definimos o header fixo, a seção hero sticky e o conteúdo gatilho. O SVG do logo foi posicionado no header, enquanto o SVG do herói ficou centralizado na seção hero.

### 2. Lógica da Animação

O script do componente:

- Observa a seção de conteúdo usando Intersection Observer.
- Usa o `intersectionRatio` como progresso da animação (0 a 1).
- Interpola escala, posição e opacidade do SVG do herói e do logo.
- Utiliza `requestAnimationFrame` para máxima fluidez.

### 3. Refatoração e Modularização

No início, todo o CSS estava no próprio arquivo do componente, o que causou problemas de escopo — especialmente quando navegávamos para páginas internas como `/about`, onde o header ficava quebrado.

**Como resolvemos:**

- Movemos os estilos essenciais do header para o próprio `Header.astro`.
- Mantivemos apenas os estilos específicos da animação no arquivo `hero-transition.css`.
- No script do HeroTransition, garantimos que o logo do header só ficasse com `opacity: 0` quando a animação estivesse ativa, evitando sumiço do logo em outras páginas.

### 4. Responsividade e Performance

- O cálculo de posição e escala é dinâmico, garantindo que a animação funcione em qualquer tamanho de tela.
- O uso de múltiplos thresholds no Intersection Observer permite uma transição suave e responsiva ao scroll.

---

## Dificuldades e Soluções

### Header quebrado em páginas internas

Após mover o logo para o header global, percebemos que o CSS da animação estava afetando o header em todas as páginas, deixando o logo invisível fora da home. A solução foi separar os estilos e garantir que o script do HeroTransition só alterasse o estado do logo quando necessário.

### Sincronização entre componentes

Como o header e o HeroTransition são componentes separados, foi necessário garantir que o script do HeroTransition acessasse corretamente os elementos do header via IDs globais, sem causar erros caso algum elemento não estivesse presente.

### Manutenção da fluidez

Todas as manipulações de DOM foram envolvidas em `requestAnimationFrame` para evitar "jank" e garantir 60fps.

---

## Código Resumido da Lógica de Animação

```js
// ...existing code...
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    const progress = Math.max(0, Math.min(1, entry.intersectionRatio));
    window.requestAnimationFrame(() => {
      updateAnimation(progress);
    });
  });
}, { threshold: thresholds });

function updateAnimation(progress) {
  // Interpola escala, posição e opacidade dos SVGs
  // ...existing code...
}
// ...existing code...
```

---

## Resultado

O componente `HeroTransition.astro` agora implementa, de forma autônoma e performática, uma transição visual onde um SVG grande do herói se transforma em um logo pequeno na barra de navegação fixa, utilizando Intersection Observer API, manipulação de SVG, transformações CSS e boas práticas de animação para garantir uma experiência fluida e moderna.

---

## Commit Sugerido

Conforme o padrão [Conventional Commits 1.0](https://www.conventionalcommits.org/):

```text
feat(hero): animação de transição do hero para logo no header com Intersection Observer

- Implementa o componente HeroTransition.astro com animação sofisticada baseada em rolagem
- Refatora estilos do header para evitar conflitos em páginas internas
- Garante performance e responsividade usando Intersection Observer API e requestAnimationFrame
- Documenta todo o processo e desafios enfrentados
```

---

## Referências

- [Intersection Observer API - MDN](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)
- [Conventional Commits 1.0](https://www.conventionalcommits.org/)
- [Astro Documentation](https://docs.astro.build/)