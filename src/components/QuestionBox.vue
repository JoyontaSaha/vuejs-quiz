<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template v-slot:lead>{{ currentQuestion.question }}</template>

      <hr class="my-4" />

      <b-list-group>
        <b-list-group-item 
          v-for="(answer, index) in answers" :key="index"
          @click="selectedAnswer(index)"
          :class="answerClass(index)"
          >
          {{ answer }}
          </b-list-group-item>
      </b-list-group>

      <b-button variant="primary" 
        @click="submitAnswer"
        :disabled="this.selectedIndex === null || answered == true"
      >
        Submit
      </b-button>
      <b-button @click="next" variant="success" href="#">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash'

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },

  data() {
    return {
      selectedIndex:  null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false
    }
  },

  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    }
  },
  watch: {
    //shuffle rest of the answers except 1st answer
    // currentQuestion() {
    //   this.selectedIndex = null;
    //   this.shuffleAnswers();
    // }

    //shuffle 1st answer + rest of the answers 
   currentQuestion: {
     immediate: true,
     handler() {
         this.selectedIndex = null
         this.answered = false
         this.shuffleAnswers()
     }
   }
  },
  methods: {
    selectedAnswer(index) {
      this.selectedIndex = index;
      // console.log(this.selectedIndex);
    },
    submitAnswer() {
      // console.log('submitAnswer function called') 
      let isCorrect = false
      // console.log('correctIndex: '+ this.correctIndex)
      if(this.selectedIndex === this.correctIndex) {
        isCorrect = true
      }
      this.answered = true
      this.increment(isCorrect)   
    },
    answerClass(index) {
      let answerClass = ''

      if (!this.answered && this.selectedIndex === index) {
        answerClass = 'selected'
      } else if (this.answered && this.correctIndex === index) {
        answerClass = 'correct'
      } else if (this.answered && this.selectedIndex === index && this.correctIndex !== index){
        answerClass = 'incorrect'
      }
      
      return answerClass
    },
    shuffleAnswers() {
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            this.shuffledAnswers = _.shuffle(answers)
            this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
            // console.log(answers);
            // console.log(this.shuffledAnswers);
    }
  },
  //shuffle 1st answer : method 1 
//  mounted() {
//     this.shuffleAnswers();
//   }
  // mounted() {
  //   this.shuffleAnswers();
  //   console.log(this.currentQuestion);
  // }
};
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
}
.list-group-item:hover {
  background: #EEE;
  cursor: pointer;
}
.selected {
  background-color: lightblue;
}
.correct {
  background-color: lightgreen;
}
.incorrect {
  background-color: red;
}
.btn {
  margin: 0 5px;
}
</style>