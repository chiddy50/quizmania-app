<template>
  <div class="quiz">
    <quiz-header></quiz-header>
    <question-box
      v-if="questions.length"
      :currentQuestion="questions[index]"
    ></question-box>   
  </div>
</template>

<script>
import QuestionBox from '@/components/QuestionBox.vue';
import QuizHeader from '@/components/QuizHeader.vue';
import questions from '@/questions.json';
import { bus } from '../main'


export default {
  name: 'quiz',
  components:{
    QuestionBox,
    QuizHeader
  },
  data(){
    return {
      index: 0,
      questions: []
    }
  },
  computed:{
    questionsLength(){
        return this.questions.length - 1;
    }
  },
  created (){
    bus.$on('next', (event) => {
      this.index ++
      if (this.index === this.questionsLength ? true : false ) {
        event.target.disabled = true
      }

    })

    
  },
  mounted(){
    this.getAllQuestions()
  },
  methods: {
    getAllQuestions(){
      if (localStorage.getItem('quiz') === null) {
          localStorage.setItem('quiz', JSON.stringify(questions));
          this.questions = questions;
      }else{
          this.questions = JSON.parse(localStorage.getItem('quiz'));
      }   
    },
  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">

</style>
