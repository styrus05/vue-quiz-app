<template>
    <div class="question-box-container">
        <b-jumbotron>
            
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
            <b-button @click="nextButton" variant="success" >Skip</b-button>
            <b-button @click="nextButton" variant="warning" v-bind:disabled="disableNext == 1" >Next</b-button>
            <b-button @click="submittedAnswer" variant="primary" v-bind:disabled="disableSubmit == 1">Submit</b-button>
            
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

    </div> 

</template>


<script>
export default {
    props: {
        currentQuestion: Object,
        next: Function, 
    },
    data(){
        return {
            nowSelectedIndex: null,
            showDismissibleAlert:false,
            message:null,
            disableSubmit:0,
            disableNext:0,
            counters: {
                correctAnswers:0,
                incorrectAnswers:0
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
        selectedAnswer(index){
            this.nowSelectedIndex = index;
        },

        nextButton(){
            this.nowSelectedIndex =null;
            this.disableSubmit = 0;
            this.disableNext = 1;
            this.showDismissibleAlert=false;
            this.firstAttempt=true;
            this.next();
        },

        submittedAnswer(){
            //console.log(this.currentQuestion.correct_answer);
            //console.log(this.answers[this.nowSelectedIndex]);
            
            var self = this;
            
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