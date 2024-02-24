<template>
  <div class="container">
    <Header />

    <!-- Div que exibe a caixa de perguntas, 
      ela fica escondida depois você responder um certo número de perguntas. -->
    <div id="caixa-pergunta" v-if="estado">
      <h2 class="pergunta">{{ pergunta }}</h2>
      <!-- Os botões são criados por um for loop que passa por todas os itens da  lista de alternativas 
        e são diferenciados com base em seu index. Além disso, em cada botão está acoplada a diretiva 
        v-on:click (descobri que isso pode ser encurtado para @click mas deixei o v-on por estilo) 
        que chama uma função para contar e verificar questões. -->
      <div id="alternativas">
        <button
          v-for="item in alternativas"
          :key="item"
          v-on:click="contaQuestao(item)"
        >
          {{ item }}
        </button>
      </div>
    </div>

    <!-- Essas divs tem as telas de vitória e derrota.
      Acho que tem um jeito melhor pra isso, mas quero
      entregar esse site hoje então depois melhoro. -->
    <div id="caixa-pergunta" v-if="!estado">
      <div id="final" v-if="ganhou">
        <img src="/sonicganha.png" height="250px" />
        <h1>Você ganhou!</h1>
      </div>
      <div id="final" v-if="!ganhou">
        <img src="/sonicperde.png" height="250px" />
        <h1>Você perdeu...</h1>
      </div>
      <button v-on:click="resetarSite()">Jogar Novamente</button>
    </div>
  </div>
</template>

<script>
import Header from '../components/Header.vue';

export default {
  components: {
    Header,
  },

  data() {
    return {
      pergunta: '',
      correta: '',
      corretas: 0,
      erradas: 0,
      antesAlternativas: [],
      alternativas: [],
      respondidas: 0,
      estado: true,
      ganhou: false,
    };
  },
  methods: {
    // Essa função basicamente verifica as questões respondidas, além de contar a quantidade de questões já
    // feitas e a quantidade de acertos e erros. Console logs podem ser usados para testes.
    contaQuestao(item) {
      if (item == this.correta) {
        console.log('Acertou!'); // Mostra que está certo.
        this.corretas += 1;
      } else {
        console.log('Errou!'); // Mostra que está errado.
        this.erradas += 1;
      }
      this.respondidas += 1;

      if (this.respondidas > 9) {
        if (this.corretas >= this.erradas) {
          this.ganhou = true;
        }
        this.estado = false;
      } else {
        this.selecionaQuestao();
      }

      console.log('Acertos:', this.corretas); // Mostra quantidade de acertos.
      console.log('Erros:', this.erradas); // Mostra quantidade de erros.
      console.log('Respondidas:', this.respondidas); // Mostra questões feitas.
    },

    resetarSite() {
      this.corretas = 0;
      this.erradas = 0;
      this.respondidas = 0;
      this.estado = true;
      this.ganhou = false;
    },

    // Função que pega as questões da API. Sim ela pega uma questão por vez e sim,
    // provavelmente tem jeitos mais otimizados de fazer isso, mas como eu pessoalmente
    // gostei do visual de carregar uma por vez e de não ter que programar um
    // jeito de desativar os botões de uma questão específica cada vez que for
    // respondida e não ter que lidar com alguém que quisesse responder elas numa ordem
    // aleatória. Enfim, funcionou e é o que importa :D
    async selecionaQuestao() {
      try {
        const response = await this.$axios.get(
          'https://the-trivia-api.com/v2/questions/'
        );
        console.log('Resposta certa:', response.data[0].correctAnswer); // Pode ser retirado

        // Função shuffle.
        function shuffle(array) {
          for (let i = array.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [array[i], array[j]] = [array[j], array[i]];
          }
          return array;
        }

        this.antesAlternativas = []; // Limpa a lista para não acumular itens com as próximas questões.

        // Como as alternativas erradas da API estão dentro de uma lista, esse loop
        // é usado pra adicionar todas elas para depois, fora do loop,
        // adicionar a alternativa certa.
        for (let i = 0; i < 3; i++) {
          this.antesAlternativas.push(response.data[0].incorrectAnswers[i]);
        }
        this.antesAlternativas.push(response.data[0].correctAnswer);

        // Anteriormente estava usando o método comentado abaixo que meu amigo Marcos
        // mostrou, porém, ao pesquisar sobre por não ter entendido muito bem seu
        // funcionamento, desobri que criar e usar a fução shuffle seria melhor.
        //alternativas.sort(() => Math.random() - 0.5);
        // Fonte:
        //https://www.freecodecamp.org/news/how-to-shuffle-an-array-of-items-using-javascript-or-typescript/

        //console.log(this.antesAlternativas);
        this.alternativas = []; // Limpa lista para não acumular itens.
        this.alternativas = shuffle(this.antesAlternativas);
        //console.log(this.alternativas)
        this.correta = response.data[0].correctAnswer;
        this.pergunta = response.data[0].question.text;
      } catch (error) {
        console.error(error);
        this.posts = [];
      }
    },
  },
  async mounted() {
    await this.selecionaQuestao();
  },
};
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  text-align: center;
  padding-top: 140px;
  align-items: center;
  background-color: #7fc7d9;
}

.pergunta {
  color: #1b1a55;
  margin-bottom: 20%;
}

#caixa-pergunta {
  background-color: #f3f5f7;
  padding-top: 5%;
  padding-bottom: 5%;
  padding-left: 5%;
  padding-right: 5%;
  min-height: 60vh;
  max-width: 60%;
  border-left: 5px solid #98abee;
  border-bottom: 5px solid #98abee;
  box-shadow: -5px 5px #233056;
  display: flex;
  flex-direction: column;
}

#alternativas {
  display: flex;
  flex-direction: column;
}

#final {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  padding-bottom: 50px;
}

/* Eu não fiz muita coisa nos botões, mas eles estão estranhamente bonitos?
  Se chegar muito feio eu juro que eu estava visualizando diferente*/
button {
  margin: 10px;
  padding: 10px;
  cursor: pointer;
  transition: all 0.15s;
  font-weight: bold;
}
</style>
