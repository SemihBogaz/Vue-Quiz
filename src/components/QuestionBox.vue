<template>
  <div>
    <b-jumbotron>
      <template #lead>
        <br />
        {{ currentQuestion.question }}
      </template>

      <hr class="my-4" />
      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          @click="selectAnswer(index)"
          :class="answerClass(index)"
        >
          {{ answer }}
        </b-list-group-item>
      </b-list-group>

      <div class="btn">
        <b-button variant="primary" @click="submitAnswer" :disabled="selectedIndex===null || isSubmitted">Submit</b-button>
        <b-button variant="success" @click="next">Next</b-button>
      </div>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash'

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function,
  },
  data() {
    return {
      selectedIndex: null,
      shuffledAnswers: [],
      score: 0,
      isSubmitted: false,
      correctIndex: null,

    };
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    },
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex=null
        this.isSubmitted=false
        this.shuffleAnswers()
        console.log(this.currentQuestion.correct_answer);
      }
    }
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    shuffleAnswers() {
      let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
      this.shuffledAnswers = _.shuffle(answers)
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
    submitAnswer() {
      let isCorrect = false

      if (this.currentQuestion.correct_answer == this.shuffledAnswers[this.selectedIndex]) {
        isCorrect = true
      }
      this.increment(isCorrect)
      this.isSubmitted = true
    },
    answerClass(index){

      let answerClass = ''

      if (!this.isSubmitted && this.selectedIndex === index) {
        answerClass = 'selected'
      }
      else if (this.isSubmitted && this.correctIndex === index) {
        answerClass = 'correct'
      }
      else if (this.isSubmitted && this.selectedIndex === index && this.correctIndex !== index) {
        answerClass = 'incorrect'
      }

      return answerClass
    }    
  }
};
</script>

<style scoped>
.jumbotron {
  background-color: lightgray;
}
.list-group {
  margin: 15px 30px 15px 30px;
  cursor: pointer;
}

.list-group-item:hover {
  background-color: #eee;
}

.btn {
  margin: 0 5px 5px 5px;
}

.lead {
  margin-left: 2px;
  margin-right: 2px;
}

.selected {
  background-color: lightblue;
}

.correct {
  background-color: lightgreen;
}
.incorrect {
  background-color: orangered;
}
</style>
