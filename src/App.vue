<template>
  <div id="app">
    <!-- img alt="Vue logo" src="./assets/logo.png" -->
    <Header :index="index"/>
    <b-container class="bv-example-row">
      <b-row>
        <b-col sm="6" offset="3">
          <QuestionBox
            v-if="questions.length"
            :currentQuestion="questions[index]"
            :next="next"
            v-on:scoreUpdated="updateCounters"
            v-on:replayGame="playAgain"
            :index="index"
          /> 
        </b-col>
      </b-row>


      <b-row>
        <b-col sm="6" offset="3">
        <Footer :index="index" :correctCount="counters.correctCount" :incorrectCount="counters.incorrectCount" />
        </b-col>
      </b-row>  

      <b-row>
        <b-col sm="6" offset="3">
            <b-button @click="playAgain" variant="warning" >Reset Quiz</b-button>
        </b-col>
      </b-row>  

    </b-container>
    
  </div>
</template>

<script>


import Header from './components/Header.vue'
import QuestionBox from './components/QuestionBox.vue'
import Footer from './components/Footer.vue'



export default {
  name: 'app',
  components: {
    Header,
    QuestionBox, 
    Footer
  },
  data(){
    return {
      questions: [], 
      index: 0,
      resetGame:1,
      counters: {
          correctCount:0,
          incorrectCount:0
        }
      }
    
  },
 
  methods: {
    updateCounters (updatedCount) {
      console.log('in updated Counters...');
      this.counters.correctCount = updatedCount.correctAnswers;
      this.counters.incorrectCount = updatedCount.incorrectAnswers;
    },

    next() {
      
      if (this.index === 10){
        //Reset index back to zero and reload next set of 10 questions.
        this.resetGame = 0;
      }
      else{
        this.index++;
      }
      
    },

    playAgain() {
        this.index = 0;
        this.questions = [];
        //this.playAgain = 1;
        this.resetGame = 1;
          fetch('https://opentdb.com/api.php?amount=10&category=21&difficulty=easy&type=multiple',{
          method:'get'
          })
          .then((response) => {
              return response.json()
          }) 
          .then((jsonData) => {
            this.questions = jsonData.results;
            this.counters.correctCount = 0;
            this.counters.incorrectCount = 0;
          })
          //Disable resetGame button
          
    }
  },
  mounted: function() {
    this.playAgain();

  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
