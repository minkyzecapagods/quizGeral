<template>
  <div class="default-layout">
    <!-- O componente <Nuxt /> injeta a página atual renderizada pelo Nuxt -->
    <Nuxt />
  </div>
</template>

<script>
/**
 * Futuras customizações (como cabeçalhos, rodapés ou barras laterais) podem ser adicionadas aqui.
 */
export default {
  // Opções do componente (se necessário) podem ser definidas aqui.
}
</script>

<style>
/*
  Estilos Globais
*/

/* Define a tipografia e algumas propriedades globais para o elemento html */
html {
  font-family:
    'Source Sans Pro',
    -apple-system,
    BlinkMacSystemFont,
    'Segoe UI',
    Roboto,
    'Helvetica Neue',
    Arial,
    sans-serif;
  word-spacing: 1px;
  /* Ajusta o tamanho do texto para melhorar a renderização em diferentes navegadores */
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  /* Melhora a suavização das fontes em sistemas diferentes */
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
}

/* Aplica o box-sizing para todos os elementos e seus pseudo-elementos,
   garantindo que padding e bordas sejam incluídos na largura total */
*,
*::before,
*::after {
  box-sizing: inherit;
  margin: 0;
}

/*
  Estilos para botões com tema verde.
  Estes botões têm borda e cor primária definidas, e alteram sua aparência ao passar o mouse.
*/
.button--green {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #3b8070;
  color: #3b8070;
  text-decoration: none;
  padding: 10px 30px;
  /* Transição suave para os efeitos hover */
  transition: background-color 0.3s, color 0.3s;
}

.button--green:hover {
  color: #fff;
  background-color: #3b8070;
}

/*
  Estilos para botões com tema cinza.
  Similar ao tema verde, mas com cores diferentes e com margem à esquerda para espaçamento.
*/
.button--grey {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #35495e;
  color: #35495e;
  text-decoration: none;
  padding: 10px 30px;
  margin-left: 15px;
  transition: background-color 0.3s, color 0.3s;
}

.button--grey:hover {
  color: #fff;
  background-color: #35495e;
}
</style>

