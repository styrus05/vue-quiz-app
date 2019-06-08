<template>
    <div>
        <b-progress class="mt-2" :max="max" show-value>
      <b-progress-bar :value="correctPercentage"  variant="success" >
            Correct: <strong> {{ correctPercentage }} </strong>
      </b-progress-bar>
      <b-progress-bar :value="incorrectPercentage" variant="danger">
            Wrong: <strong> {{ incorrectPercentage }} </strong>
      </b-progress-bar>
      <b-progress-bar :value="yetToAnswer" variant="warning">        
            To Answer: <strong> {{ yetToAnswer }} </strong>
</b-progress-bar>
    </b-progress>
    <p> {{ counters }} </p>
    </div> 
</template>

<script>
  export default {
    data() {
      return {
        value: 10,
        max: 10
      }
    },
    methods: {
      
    },
    computed: {
        correctPercentage: function () {
            return this.value * (this.correctCount / 10);
        },
        incorrectPercentage: function () {
            return this.value * (this.incorrectCount / 10);
        },
        yetToAnswer: function () {
            return this.value - (this.correctCount + this.incorrectCount);
        }
    },
    props: {
        index:Number,
        correctCount:Number,
        incorrectCount:Number
    }

  }
</script>

<style scoped>

.progress {
height: 30px;
}
</style>