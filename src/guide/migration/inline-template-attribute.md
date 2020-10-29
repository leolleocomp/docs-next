---
badges:
  - breaking
---

# Atributo de Template Inline <MigrationBadges :badges="$frontmatter.badges" />

## Visão Geral

O suporte à [feature inline-template](https://vuejs.org/v2/guide/components-edge-cases.html#Inline-Templates) foi removido.

## Sintaxe 2.x

Na versão 2.x, o Vue disponibilizou o atributo `inline-template` em componentes filho para usar seu conteúdo interno como o seu template, ao invés de tratá-lo como conteúdo distribuído.

```html
<my-component inline-template>
  <div>
    <p>Estes são compilados como o próprio template do componente</p>
    <p>Ao invés do conteúdo de transclusão do pai.</p>
  </div>
</my-component>
```

## Sintaxe 3.x

Essa feature não será mais suportada.

## Estratégia de Migração

A maior parte dos casos de uso para o `inline-template` assume um setup sem-ferramenta-de-build (no-build-tool-setup), onde todos os templates são escritos diretamente dentro da página HTML.

### Opção #1: Use a tag `<script>`

A forma mais intuitiva de contornar esses casos é utilizando a tag `<script>` com um tipo alternativo:

```js
<script type="text/html" id="my-comp-template">
  <div>{{ hello }}</div>
</script>
```

E no componente, referenciar o _template_ usando um seletor:

```js
const MyComp = {
  template: '#my-comp-template'
  // ...
}
```

Isso não requer nenhum setup de _build_, funciona em todos os navegadores, não está sujeito a nenhuma armadilha de _parse_ de HTML _in-DOM_ (e.g. você pode usar nomes de propriedades em _camelCase_), e fornece _syntax highlight_ apropriado na maioria das IDEs. Em _frameworks server-side_ tradicionais, esses _templates_ podem ser separados em _templates_ parciais de servidor (inclusive no template HTML principal) para melhor manutenibilidade.

### Opção #2: Slot Padrão

Um componente que previamente utilizava o `inline-template` também pode ser refatorado usando o _slot_ padrão - que torna o _data scoping_ mais explícito ao mesmo tempo em que preserva a conveniência de escrever conteúdo filho _inline_:

```html
<!-- Sintaxe 2.x -->
<my-comp inline-template :msg="parentMsg">
  {{ msg }} {{ childState }}
</my-comp>

<!-- Versão com Slot Padrão -->
<my-comp v-slot="{ childState }">
  {{ parentMsg }} {{ childState }}
</my-comp>
```

O filho, ao invés de não fornecer nenhum _template_, deve agora renderizar o _slot_ padrão\*:

```html
<!--
  no template filho, renderize o slot padrão ao mesmo tempo em que passar
  o estado privado, necessário, do filho.
-->
<template>
  <slot :childState="childState" />
</template>
```

> - Nota: No 3.x, slots podem ser renderizadas como o root com o suporte nativo aos [fragments](/guide/migration/fragments)!
