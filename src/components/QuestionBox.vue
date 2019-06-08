<template>
    <div class="question-box-container">
        <b-jumbotron v-if="index!=9">
            
            <template slot="lead">
            {{currentQuestion.question}}
            </template>

            <hr class="my-4">

            <b-list-group >
                <b-list-group-item
                v-for="(answer, index) in answers" 
                :key="index"
                @click="selectedAnswer(index)"
                :class="[nowSelectedIndex === index ? 'selected' : 'gray']"
                >
                    {{answer}}

                </b-list-group-item>
            </b-list-group>
            
            <!--b-list-group v-if="index==9">
             <b-list-group-item> Your final scores are:</b-list-group-item>
             <b-list-group-item> Correct: {{counters.correctAnswers}}</b-list-group-item>
             <b-list-group-item> Wrong: {{counters.incorrectAnswers}}</b-list-group-item>
             <b-list-group-item> Did not answer: {{ counters.toAnswer }} </b-list-group-item>
            </b-list-group-->   
            
            <b-button @click="nextButton" variant="outline-success" v-bind:disabled="index==9">Skip</b-button>
            <!--b-button @click="backButton" variant="outline-warning" v-bind:disabled="disableBack == 1" >Back</b-button-->

            <!--b-button @click="nextButton" variant="warning" v-bind:disabled="disableNext == 1" >Next</b-button-->
            <b-button @click="submittedAnswer" variant="outline-primary" v-if="index!=9" v-bind:disabled="disableSubmit == 1">Submit</b-button>
            <b-button @click="replayGame" variant="danger" v-if="index==9">Reset Score and Play Again</b-button>
            
            <b-alert
                variant="danger"
                dismissible
                fade
                :show="showDismissibleAlert"
                @dismissed="showDismissibleAlert=false"
                >
                {{message}}
            </b-alert> 
            <Footer :correctAnswers="correctAnswers"/>

        </b-jumbotron>
        
        <b-jumbotron header="" lead="Your final score card is" v-if="index==9">
            <p>Correct: {{counters.correctAnswers}}</p>
            <p>Wrong: {{counters.incorrectAnswers}}</p>
            <p>Did not answer: {{ counters.toAnswer }}</p>

            <b-button @click="replayGame" variant="danger" v-if="index==9">Reset Score and Play Again</b-button>
        </b-jumbotron>

    </div> 

</template>


<script>
export default {
    props: {
        currentQuestion: Object,
        next: Function, 
        index: Number
    },
    data(){
        return {
            nowSelectedIndex: null,
            showDismissibleAlert:false,
            message:null,
            disableSubmit:1,
            disableNext:0,
            counters: {
                correctAnswers:0,
                incorrectAnswers:0,
                toAnswer:0
            },
            firstAttempt:true
            //answers: [] 
        }
    },
    computed: {
       answers() { 
            let answers = [];
            answers = [...this.currentQuestion.incorrect_answers];
            answers.push(this.currentQuestion.correct_answer);
            for (var i = answers.length - 1; i > 0; i--) {
                var j = Math.floor(Math.random() * (i + 1));
                var temp = answers[i];
                answers[i] = answers[j];
                answers[j] = temp;
            }
            return answers;
        }
    },

    watch: {
        resetSelectedIndex: function(newVal, oldVal) {
          console.log('Prop changed: ', newVal, ' | was: ', oldVal)
            //this.nowSelectedIndex = null;
        }
    },

    methods: {
        emitToParent () {
             this.$emit('scoreUpdated', this.counters);
             console.log('Emitted event: scoreUpdated');
        },

        replayGame () {
            this.$emit('replayGame');
        },

        selectedAnswer(index){
            this.nowSelectedIndex = index;
            this.disableSubmit = 0;

        },

        nextButton(){
            this.nowSelectedIndex =null;
            this.disableSubmit = 1;
            this.disableNext = 1;
            this.showDismissibleAlert=false;
            this.firstAttempt=true;
            this.counters.toAnswer = 10 - (this.counters.correctAnswers + this.counters.incorrectAnswers)
            this.next();
        },

        submittedAnswer(){
            //console.log(this.currentQuestion.correct_answer);
            //console.log(this.answers[this.nowSelectedIndex]);
            
            var self = this;
            this.disableSubmit = 1;
            if(this.currentQuestion.correct_answer === this.answers[this.nowSelectedIndex])
            {
                this.message="That was correct. Well done!";
                this.showDismissibleAlert=true;
                
                this.disableSubmit = 1;
                this.disableNext = 0; 

                if(this.firstAttempt){
                    this.counters.correctAnswers++ ;
                    this.emitToParent();}

                
                setTimeout(function(){
                //console.log('Going to trigger next button click');
                self.nextButton()},2000);   
            }
            else 
            {
                this.message="Incorrect answer selected! Correct answer is: " + this.currentQuestion.correct_answer;
                this.disableSubmit = 0;
                this.disableNext = 0; 
                if(this.firstAttempt){
                    this.counters.incorrectAnswers++;
                    this.emitToParent();}
                this.firstAttempt=false;
                this.showDismissibleAlert=true

                setTimeout(function(){
                //console.log('Going to trigger next button click');
                self.nextButton()},2000);        
            }
            }
    },
    mounted() {
        this.disableNext = 1;
        console.log(this.currentQuestion); 
    }
}
</script> 

<style scoped>
    .list-group {
        margin-bottom: 25px;
        cursor: pointer;
    }

    .btn {
        margin: 0 5px;
    }

    .notSelected {
        background-color: gray
    }
    .selected {
        background-color: lightblue
    }

    .correct {
        background-color: lightgreen
    }


    .incorrect {
        background-color: red
    }

    .list-group-item:hover {
        background: #eee;
        cursor: pointer;
    }

    .alert {
        margin-top: 20px;
    }
 
</style>