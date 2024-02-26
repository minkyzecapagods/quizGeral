<template>
  <div>
    <div class="container">
      <Header />
      
      <div v-for="(item, index) in lista" id="separar">
        <div id="caixa-pergunta" :class="index">
          <h2 class="pergunta">{{ item.pergunta}}</h2>
          <div id="alternativas">
            <button
            v-for="botao in lista[index].alternativas[0]"
            v-on:click="contaQuestao(botao, index)"
            v-bind:class="index"
            v-bind:disabled="desabilitar.includes(index)"
            >{{ botao }}</button>
          </div>
        </div>
      </div>
      <!-- Essas divs tem as telas de vitória e derrota.
        Acho que tem um jeito melhor pra isso, mas quero
        entregar esse site hoje então depois melhoro. -->
      <div id="caixa-pergunta" v-if="!estado">
        <div id="final">
          <img :src=source height="250px" />
          <h1>{{mensagem}}</h1>
        </div>
        <button v-on:click="resetarSite()">Jogar Novamente</button>
      </div>
    </div>
    <Footer/>
  </div>
</template>

<script>
import Header from '../components/Header.vue';
import Footer from '../components/Footer.vue'

export default {
  components: {
    Header,
    Footer
  },

  data() {
    return {
      mensagem: '',
      source: '',
      lista: [],
      corretas: 0,
      erradas: 0,
      respondidas: 0,
      estado: true,
      ganhou: false,
      desabilitar: []
    };
  },
  methods: {
    // Essa função basicamente verifica as questões respondidas, além de contar a quantidade de questões já
    // feitas e a quantidade de acertos e erros. Console logs podem ser usados para testes.
    contaQuestao(item, index) {
      console.log(item, index)
      if (item == this.lista[index].alternativaCorreta) {
        console.log('Acertou!'); // Mostra que está certo.
        this.corretas += 1;
      } else {
        console.log('Errou!'); // Mostra que está errado.
        this.erradas += 1;
      }
      this.respondidas += 1;

      if (this.respondidas > 9) {
        if (this.corretas >= this.erradas) {
          this.mensagem = 'Você ganhou! Questões certas: ' + this.corretas;
          this.source = '/sonicganha.png';
        }
        else {
          this.mensagem =  'Você perdeu... Questões erradas: ' + this.erradas;
          this.source = '/sonicperde.png';
        }
        
        this.estado = false;
      }

      this.desabilitar.push(index)

      console.log('Acertos:', this.corretas); // Mostra quantidade de acertos.
      console.log('Erros:', this.erradas); // Mostra quantidade de erros.
      console.log('Respondidas:', this.respondidas); // Mostra questões feitas.
    },

    resetarSite() {
      this.lista = [];
      this.desabilitar = [];
      this.corretas = 0;
      this.erradas = 0;
      this.respondidas = 0;
      this.estado = true;
      this.ganhou = false;
      this.selecionaQuestao();
    },

    async selecionaQuestao() {
      try {
        const response = await this.$axios.get(
          'https://the-trivia-api.com/v2/questions/'
        );
        // console.log(response.data);
        // Função shuffle.
        function shuffle(array) {
          for (let i = array.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [array[i], array[j]] = [array[j], array[i]];
          }
          return array;
        }
         let tentativa = [];

        response.data.forEach((element) => {
          let alternativas = [];
          let pergunta = element.question.text;
          tentativa = element.incorrectAnswers;
          tentativa.push(element.correctAnswer);
          alternativas.push(shuffle(tentativa));
          let correta = element.correctAnswer;
          this.lista.push({pergunta: pergunta, alternativas, alternativaCorreta: correta});
          tentativa = [];
        });
        console.log(this.lista[0].alternativas)
      } 
      catch (error) {
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
  flex-direction: column;
  text-align: center;
  padding-top: 140px;
  align-items: center;
  background-color: #7fc7d9;
}

.pergunta {
  color: #1b1a55;
  margin-bottom: 10%;
}

#separar {
  display: flex;
  justify-content: center;
  flex-direction: column;
  text-align: center;
  align-items: center;
  height: 100%;
  width: 100%;
}

#caixa-pergunta {
  background-color: #f3f5f7;
  padding-top: 5%;
  padding-bottom: 5%;
  padding-left: 5%;
  padding-right: 5%;
  margin-bottom: 50px;
  min-height: 60vh;
  width: 60%;
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

.final {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  margin-bottom: 50px;
  
}

button {
  margin: 10px;
  padding: 10px;
  cursor: pointer;
  transition: all 0.15s;
  font-weight: bold;
}
</style>
