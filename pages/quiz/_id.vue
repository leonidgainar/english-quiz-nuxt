<template>
  <div>
    <QuizStepperComponent
      :quiz-name="quizName"
      :quiz-all-questions="quizAllQuestions"
    />
  </div>
</template>

<script>
import QuizStepperComponent from '../../components/QuizStepperComponent'
import irregularVerbs from '@/static/irregularVerbs.json'

export default {
  components: {
    QuizStepperComponent,
  },

  data() {
    return {
      restartQuiz: false,
    }
  },

  computed: {
    id() {
      return this.$route.params.id
    },

    quizName() {
      let quizName = null
      if (this.id === '1') {
        quizName = 'Past Simple'
      }
      if (this.id === '2') {
        quizName = 'Past Participate'
      }
      return quizName
    },

    quizAllQuestions() {
      let verbs = null
      if (this.id === '1') {
        verbs = irregularVerbs.map(({ infinitive, pastSimple }) => {
          return {
            infinitive,
            correctAnswer: pastSimple,
          }
        })
      }
      if (this.id === '2') {
        verbs = irregularVerbs.map(({ infinitive, pastParticipate }) => {
          return {
            infinitive,
            correctAnswer: pastParticipate,
          }
        })
      }
      return verbs
    },
  },
}
</script>
