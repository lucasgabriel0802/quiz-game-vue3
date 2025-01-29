<template>
  <div>
    <ScoreBoard :winCount="this.winCount" :loseCount="this.loseCount" />

    <template v-if="this.answers">
      <h1 v-html="this.question"></h1>

      <template v-for="(answer, index) in this.answers" :key="index">
        <input :disabled="this.answerSubmitted" type="radio" name="options" :value="answer"
          v-model="this.chosen_answer">
        <label v-html="answer"></label>
        <br>
      </template>
      <br>
      <button @click="submitAnswer()" class="send" type="button">Send</button>


      <section class="result" v-if="this.answerSubmitted">
        <template v-if="this.chosen_answer == this.correctAnswer">
          <h4>&#9989; Parabéns, a resposta "{{ this.correctAnswer }}" está correta.</h4>
        </template>
        <template v-else>
          <h4>&#10060; Que pena, a resposta está errada. A resposta correta é "{{ this.correctAnswer }}".</h4>
        </template>
        <button @click="this.getNewQuestion()" class="send" type="button">Próxima pergunta</button>
      </section>
    </template>
  </div>
</template>

<script>
import ScoreBoard from './components/ScoreBoard.vue';

export default {
  name: 'App',
  components: {
    ScoreBoard
  },
  data() {
    return {
      question: undefined,
      incorrectAnswers: [],
      correctAnswer: undefined,
      chosen_answer: undefined,
      answerSubmitted: false,
      winCount: 0,
      loseCount: 0
    }
  },

  computed: {
    answers() {
      var answers = [...this.incorrectAnswers];
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
      return answers;
    }
  },
  methods: {
    submitAnswer() {
      if (!this.chosen_answer) {
        alert('Pick one of the options');
      } else {
        this.answerSubmitted = true;
        if (this.chosen_answer == this.correctAnswer)
          this.winCount += 1;
        else
          this.loseCount += 1;
      }
    },
    getNewQuestion() {
      this.axios
        .get('https://opentdb.com/api.php?amount=1')
        .then((response) => {
          this.question = response.data.results[0].question;
          this.incorrectAnswers = response.data.results[0].incorrect_answers;
          this.correctAnswer = response.data.results[0].correct_answer;
          this.answerSubmitted = false;
          // console.log(response.data.results[0])
        }).catch(error => {
          if (error.response) {
            console.error('Erro de resposta:', error.response.status, error.response.data);
          } else if (error.request) {
            console.error('Erro de requisição:', error.request);
          } else {
            console.error('Erro desconhecido:', error.message);
          }
        });
    }
  },
  created() {
    this.getNewQuestion();
  },


}

</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 900px;
}

h1 {
  margin-top: 40px;
}

input[type='radio'] {
  margin: 12px 4px;
}

button.send {
  margin-top: 12px;
  height: 40px;
  min-width: 120px;
  padding: 0 16px;
  color: #fff;
  background-color: #1867c0;
  border: 1px solid #1867c0;
  cursor: pointer;
}
</style>
