<template>
  <div>
    <div v-if="!quizIsStarted">
      <h1>Welcome to {{ quizName }} Quiz!</h1>
      <v-select
        v-model.trim="numberOfVerbs"
        :items="[5, 10, 20, 30, 40, 50, 'All']"
        label="Select number of questions"
      ></v-select>
      <v-btn color="primary" :disabled="!numberOfVerbs" @click="startQuiz">
        Start quiz
      </v-btn>
    </div>

    <div v-if="quizIsStarted && !quizIsFinished">
      <h2>
        {{ currentIndex + 1 }}/{{ numberOfVerbs }}
        Past Simple form of the verb:
      </h2>
      <h2 class="pt-5">
        {{ selectedVerbs[currentIndex].infinitive }}
      </h2>
      <v-form @submit.prevent="nextQuestion">
        <v-text-field
          v-model="currentAnswer"
          label="Your answer"
          placeholder="Type your answer"
        ></v-text-field>
        <v-btn color="primary" :disabled="!currentAnswer" type="submit">
          Next question
        </v-btn>
      </v-form>
    </div>

    <div v-if="quizIsFinished">
      <h2>Your score: {{ score }}/{{ numberOfVerbs }}</h2>
      <v-simple-table class="my-2">
        <template #default>
          <thead>
            <tr>
              <th class="text-left">Verb</th>
              <th class="text-left">Correct answer</th>
              <th class="text-left">Your answer</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="verb in selectedVerbs" :key="verb.infinitive">
              <td>{{ verb.infinitive }}</td>
              <td>{{ verb.correctAnswer }}</td>
              <td :class="verb.isCorrect ? 'green--text' : 'red--text'">
                {{ verb.yourAnswer }}
              </td>
            </tr>
          </tbody>
        </template>
      </v-simple-table>
      <v-btn class="mt-2" color="primary" @click="startNewQuiz">
        Start new quiz
      </v-btn>
    </div>
  </div>
</template>

<script>
import irregularVerbs from '@/static/irregularVerbs.json'

export default {
  data() {
    return {
      quizIsStarted: false,
      quizIsFinished: false,
      numberOfVerbs: null,
      selectedVerbs: [],
      currentIndex: 0,
      currentAnswer: null,
      score: 0,
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

    verbs() {
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
  methods: {
    startQuiz() {
      this.quizIsStarted = true
      this.numberOfVerbs =
        this.numberOfVerbs === 'All'
          ? irregularVerbs.length
          : this.numberOfVerbs
      this.selectedVerbs = this.verbs
        .sort(() => Math.random() - Math.random())
        .slice(0, this.numberOfVerbs)
    },

    nextQuestion() {
      this.currentAnswer = this.currentAnswer.toLowerCase()
      this.selectedVerbs[this.currentIndex].yourAnswer = this.currentAnswer
      this.calculateScore()
      if (this.currentIndex < this.selectedVerbs.length - 1) {
        this.currentAnswer = null
        this.currentIndex++
      } else {
        this.quizIsFinished = true
      }
    },

    calculateScore() {
      const correctAnswers =
        this.selectedVerbs[this.currentIndex].correctAnswer.split('/')
      if (
        correctAnswers[0] === this.currentAnswer ||
        correctAnswers[1] === this.currentAnswer ||
        this.selectedVerbs[this.currentIndex].correctAnswer ===
          this.currentAnswer
      ) {
        this.selectedVerbs[this.currentIndex].isCorrect = true
        this.score++
      } else {
        this.selectedVerbs[this.currentIndex].isCorrect = false
      }
    },

    startNewQuiz() {
      this.currentIndex = 0
      this.selectedVerbs = []
      this.numberOfVerbs = null
      this.quizIsFinished = false
      this.quizIsStarted = false
      this.currentAnswer = null
      this.score = 0
    },
  },
}
</script>
