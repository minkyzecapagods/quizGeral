<template>
  <div>
    <div class="container">
      <Header />

      <!-- Itera sobre cada questão da lista -->
      <div v-for="(item, index) in lista" :key="index" class="separar">
        <div class="caixa-pergunta" :class="{'desabilitado': desabilitar.includes(index)}">
          <h2 class="pergunta">{{ item.pergunta }}</h2>
          <div class="alternativas">
            <!-- Itera diretamente sobre as alternativas do item -->
            <button
              v-for="(botao, bIndex) in item.alternativas"
              :key="bIndex"
              @click="contaQuestao(botao, index)"
              :disabled="desabilitar.includes(index)"
            >
              {{ botao }}
            </button>
          </div>
        </div>
      </div>

      <!-- Tela de resultado: vitória ou derrota -->
      <div class="caixa-pergunta" v-if="!estado">
        <div class="final">
          <img :src="source" alt="Resultado" height="250px" />
          <h1>{{ mensagem }}</h1>
        </div>
        <button @click="resetarSite">Jogar Novamente</button>
      </div>
    </div>
    <Footer />
  </div>
</template>

<script>
import Header from '../components/Header.vue';
import Footer from '../components/Footer.vue';

export default {
  components: {
    Header,
    Footer,
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
      // 'ganhou' não está sendo usado; se não for necessário, pode ser removido.
      desabilitar: [],
      // Defina o número máximo de questões como propriedade para facilitar alterações
      maxQuestoes: 10,
    };
  },
  methods: {
    /**
     * Verifica se a resposta selecionada está correta e atualiza os contadores.
     * @param {String} resposta - A alternativa selecionada pelo usuário.
     * @param {Number} index - O índice da questão atual.
     */
    contaQuestao(resposta, index) {
      console.log('Resposta selecionada:', resposta, 'na questão:', index);
      if (resposta === this.lista[index].alternativaCorreta) {
        console.log('Acertou!');
        this.corretas += 1;
      } else {
        console.log('Errou!');
        this.erradas += 1;
      }
      this.respondidas += 1;

      // Se todas as questões foram respondidas, define a mensagem de resultado
      if (this.respondidas >= this.maxQuestoes) {
        if (this.corretas >= this.erradas) {
          this.mensagem = `Você ganhou! Questões certas: ${this.corretas}`;
          this.source = '/sonicganha.png';
        } else {
          this.mensagem = `Você perdeu... Questões erradas: ${this.erradas}`;
          this.source = '/sonicperde.png';
        }
        this.estado = false;
      }

      // Desabilita a questão para evitar múltiplas respostas
      this.desabilitar.push(index);

      console.log('Acertos:', this.corretas);
      console.log('Erros:', this.erradas);
      console.log('Respondidas:', this.respondidas);
    },

    /**
     * Reinicia o jogo, limpando os dados e carregando novas questões.
     */
    resetarSite() {
      this.lista = [];
      this.desabilitar = [];
      this.corretas = 0;
      this.erradas = 0;
      this.respondidas = 0;
      this.estado = true;
      this.selecionaQuestao();
    },

    /**
     * Realiza uma requisição para obter as questões e formata os dados.
     */
    async selecionaQuestao() {
      try {
        const response = await this.$axios.get(
          'https://the-trivia-api.com/v2/questions/'
        );
        // Função para embaralhar as alternativas
        function shuffle(array) {
          for (let i = array.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [array[i], array[j]] = [array[j], array[i]];
          }
          return array;
        }

        response.data.forEach((element) => {
          // Constrói um array de alternativas já embaralhado
          const alternativas = shuffle([
            ...element.incorrectAnswers,
            element.correctAnswer,
          ]);
          // Adiciona a questão à lista
          this.lista.push({
            pergunta: element.question.text,
            alternativas,
            alternativaCorreta: element.correctAnswer,
          });
        });
        console.log('Primeira questão alternativas:', this.lista[0].alternativas);
      } catch (error) {
        console.error('Erro ao carregar questões:', error);
      }
    },
  },
  async mounted() {
    await this.selecionaQuestao();
  },
};
</script>

<style scoped>

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

/* Classes para separar questões e caixa de pergunta */
.separar {
  display: flex;
  justify-content: center;
  flex-direction: column;
  text-align: center;
  align-items: center;
  height: 100%;
  width: 100%;
}

.caixa-pergunta {
  background-color: #f3f5f7;
  padding: 5% 5%;
  margin-bottom: 50px;
  min-height: 60vh;
  width: 60%;
  border-left: 5px solid #98abee;
  border-bottom: 5px solid #98abee;
  box-shadow: -5px 5px #233056;
  display: flex;
  flex-direction: column;
}

/* Estilização para alternativas */
.alternativas {
  display: flex;
  flex-direction: column;
}

/* Estilização para a tela de resultado */
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

/* Estilização para indicar elemento desabilitado */
.desabilitado {
  pointer-events: none;
  opacity: 0.9;
}
</style>

