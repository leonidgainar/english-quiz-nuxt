<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="10" md="8" lg="6" xl="5">
      <QuizStepperComponent
        :quiz-name="quizName"
        :quiz-all-questions="quizAllQuestions"
      />
    </v-col>
  </v-row>
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
        quizName = 'Past Participle'
      }
      if (this.id === '3') {
        quizName = 'Translate RO -> EN'
      }
      if (this.id === '4') {
        quizName = 'Translate EN -> RO'
      }
      if (this.id === '5') {
        quizName = 'Speech Infinitive'
      }
      if (this.id === '6') {
        quizName = 'Speech Past Simple'
      }
      if (this.id === '7') {
        quizName = 'Speech Past Participle'
      }
      return quizName
    },

    quizAllQuestions() {
      let verbs = null
      if (this.id === '1') {
        verbs = irregularVerbs.map(({ infinitive, pastSimple }) => {
          return {
            infinitive,
            question: infinitive,
            correctAnswer: pastSimple,
          }
        })
      }
      if (this.id === '2') {
        verbs = irregularVerbs.map(({ infinitive, pastParticiple }) => {
          return {
            infinitive,
            question: infinitive,
            correctAnswer: pastParticiple,
          }
        })
      }
      if (this.id === '3') {
        verbs = irregularVerbs.map(({ infinitive, translation }) => {
          return {
            infinitive,
            question: translation,
            correctAnswer: 'to ' + infinitive,
          }
        })
      }
      if (this.id === '4') {
        verbs = irregularVerbs.map(({ infinitive, translation }) => {
          return {
            infinitive,
            question: 'to ' + infinitive,
            correctAnswer: translation,
          }
        })
      }
      if (this.id === '5') {
        verbs = irregularVerbs.map(({ infinitive }) => {
          return {
            infinitive,
            question: infinitive,
            correctAnswer: infinitive,
          }
        })
      }
      if (this.id === '6') {
        verbs = irregularVerbs.map(({ infinitive, pastSimple }) => {
          return {
            infinitive,
            question: `${pastSimple.split('/')}`,
            correctAnswer: `${pastSimple.split('/')}`,
          }
        })
      }
      if (this.id === '7') {
        verbs = irregularVerbs.map(({ infinitive, pastParticiple }) => {
          return {
            infinitive,
            question: `${pastParticiple.split('/')}`,
            correctAnswer: `${pastParticiple.split('/')}`,
          }
        })
      }
      return verbs
    },
  },
}
</script>
