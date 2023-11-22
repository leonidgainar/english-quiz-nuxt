<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="10" md="8" lg="6" xl="5">
      <QuizStepperComponent
        :quiz-name="quizName"
        :quiz-requires-microphone="quizRequiresMicrophone"
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
      switch (this.id) {
        case '1':
          return 'Past Simple'
        case '2':
          return 'Past Participle'
        case '3':
          return 'Translate RO -> EN'
        case '4':
          return 'Translate EN -> RO'
        case '5':
          return 'Speech Infinitive'
        case '6':
          return 'Speech Past Simple'
        case '7':
          return 'Speech Past Participle'
        default:
          return 'Unknown quiz'
      }
    },

    quizRequiresMicrophone() {
      switch (this.id) {
        case '5':
        case '6':
        case '7':
          return true
        default:
          return false
      }
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
            question: pastSimple,
            correctAnswer: pastSimple,
          }
        })
      }
      if (this.id === '7') {
        verbs = irregularVerbs.map(({ infinitive, pastParticiple }) => {
          return {
            infinitive,
            question: pastParticiple.split,
            correctAnswer: pastParticiple.split,
          }
        })
      }
      return verbs
    },
  },
}
</script>
