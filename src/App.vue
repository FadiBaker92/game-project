<template>
  <div>

    <ScoreList :winCount="this.winCount" :loseCount="this.loseCount" />
    
    <template v-if="this.question">

     <h1 v-html="this.question"></h1>
    
      <template v-for="answer in this.answers" :key="answer">
  
        <input 
                
                type="radio"
                name ="options"
                :value ="answer"
                v-model="this.chosenAnswer"
                :disabled="this.answerSubmitted"
                >        
                <label v-html="answer"></label> <br>
      </template>

      <button v-if="!this.answerSubmitted" @click="this.submitAnswer()" class="send" type="button">Send</button>
      
      <section v-if="this.answerSubmitted" class="result">
        <h4 v-if="this.chosenAnswer == this.correctAnswer"
        v-html="'&#9989; Congrats '+ this.correctAnswer +' is the correct answer.'">
         </h4>
        <h4 v-else 
        v-html="'&#10060; I`m sorry you picked the wrong answer. The correct answer is ' + this.correctAnswer + '.'">
        </h4>
        <button @click="this.getNewQuestion()" class="send" type="button">Next question</button>
      </section>

    </template>

    
  </div>
</template>

<script>

import ScoreList from '@/components/ScoreList.vue'

export default {
  name: 'App',
  components : {
    ScoreList
  },

  data(){
    return {
      question : undefined,
      incorrectAnswers : undefined,
      correctAnswer : undefined,
      chosenAnswer : undefined,
      answerSubmitted : false,
      winCount : 0 ,
      loseCount : 0
    }
  },

  computed : {
    answers() {
      var answers = JSON.parse(JSON.stringify(this.incorrectAnswers)) ;
      answers.splice ( Math.round (Math.random() * answers.length) , 0 , this.correctAnswer);
      return answers;
    }
  },

  methods : {
    submitAnswer() {
      if (!this.chosenAnswer) {
        alert ("pick one of the options");
      } else {
        this.answerSubmitted = true; 
        if (this.chosenAnswer == this.correctAnswer) {
          this.winCount ++
        } else {
          this.loseCount ++
        }
      }
    },

    getNewQuestion() {

      this.answerSubmitted=false;
      this.chosenAnswer = undefined;
      this.question = undefined;

      this.axios
    .get("https://opentdb.com/api.php?amount=1&category=18&difficulty=easy&type=multiple")
    .then((response) => {

        this.question = response.data.results[0].question;
        this.incorrectAnswers = response.data.results[0].incorrect_answers;
        this.correctAnswer = response.data.results[0].correct_answer;
        
      })
    }
  },

  created () { 
    this.getNewQuestion();
  }
}

</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px;
 
  input[type=radio] {
    margin : 12px 4px ;
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
}
</style>
