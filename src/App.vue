<template>
  
  <div>

    <Scoreboard  :winCount="this.winCount" :loseCount="this.loseCount" />

    <template v-if="this.question">
      
      <h1 v-html="this.question"></h1>
      
      <template v-for="(answer, index) in this.answers" v-bind:key="index">
        <input 
          :disabled="this.answerSubmitted"
          type="radio" 
          name="options" 
          :value="answer"
          v-model="this.chose_answer"
        >
        <label v-html="answer"></label><br>
      </template> 

      <button v-if="!this.answerSubmitted" @click="this.submitAnswer()" class="send" type="button">Send</button>
      
      <section v-if="this.answerSubmitted" class="result">
        <h4 
          v-if="this.chose_answer == this.correctAnswer"
          v-html="'&#9989; Parabéns, a resposta correta é ' + this.correctAnswer"
        >
        </h4>
        <h4 v-else
          v-html="'&#10060; Que pena, a respostra correta é ' + this.correctAnswer"
        >
        </h4>
        <button @click="this.getNewQuestion()" class="send" type="button">Next question</button>
      </section>

    </template>
  </div>
</template>

<script>

import Scoreboard from './components/ScoreBoard.vue'

export default {

  name: 'App',
  components: {
    Scoreboard
  },

  data() {
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chose_answer: undefined,
      answerSubmitted: false,
      winCount: 0,
      loseCount: 0,
    }
  },

  computed: {
    answers() {
      var answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
      return answers;
    }
  },
  methods: {
    submitAnswer: function() {
      if (!this.chose_answer) {
        alert('Escolha uma das opçoes!');
      } else {
        this.answerSubmitted = true;
        if (this.chose_answer == this.correctAnswer) {
          this.winCount++;
        } else {
          this.loseCount++;
        }
      }
    },
  
    getNewQuestion: function() {
  
      this.answerSubmitted = false;
      this.chose_answer = undefined;
      this.question = undefined;
  
      this.axios
      .get('https://opentdb.com/api.php?amount=1&category=18')
      .then((response) => {
        this.question = response.data.results[0].question;
        this.incorrectAnswers = response.data.results[0].incorrect_answers;
        this.correctAnswer = response.data.results[0].correct_answer;

        console.log(response.data.results[0])
      })  
    
    }
  
  },
  created() {
    this.getNewQuestion();
  }

}

// https://opentdb.com/api.php?amount=1&category=18&difficulty=easy
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;

  input[type=radio] {
    margin: 12px 4px;
  }

  button.send{
    margin-top: 12px;
    height: 40px;
    min-width: 120px;
    padding: 0 16px;
    color: #fff;
    background-color: #1867c0;
    border: 1px solid #1867c0;
    border-radius: 12px;
    cursor: pointer;
    font-size: 18px;
  }
}
</style>
