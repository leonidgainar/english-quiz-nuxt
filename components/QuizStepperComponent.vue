<template>
  <v-stepper v-model="step">
    <v-stepper-header>
      <v-stepper-step :complete="step > 1" step="1"> </v-stepper-step>

      <v-divider></v-divider>

      <v-stepper-step :complete="step > 2" step="2"> </v-stepper-step>

      <v-divider></v-divider>

      <v-stepper-step step="3"> </v-stepper-step>
    </v-stepper-header>

    <v-stepper-items>
      <v-stepper-content step="1">
        <div v-if="step === 1">
          <h1>Welcome to {{ quizName }} Quiz!</h1>
          <v-select
            v-model.trim="numberOfQuestions"
            :items="[5, 10, 20, 30, 40, 50, 60, 70, 80, 90, 'All']"
            label="Select number of questions"
          ></v-select>
          <v-switch v-model="shuffleMode" label="Shuffle mode"></v-switch>
          <v-btn
            color="primary"
            :disabled="!numberOfQuestions"
            @click="startQuiz"
          >
            Start quiz
          </v-btn>
          <v-alert class="mt-10" type="info">
            Switch off shuffle mode if you want the verbs in the quiz to be in
            alphabetical order
          </v-alert>
        </div>
      </v-stepper-content>

      <v-stepper-content step="2">
        <div v-if="step === 2">
          <StopwatchComponent @timer-changed="onTimerChanged" />
          <h2>
            {{ currentIndex + 1 }}/{{ numberOfQuestions }} {{ quizName }} form
            of the verb:
          </h2>
          <h2 class="pt-5">
            {{
              quizQuestions[currentIndex].infinitive ||
              quizQuestions[currentIndex].translation
            }}
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
      </v-stepper-content>

      <v-stepper-content step="3">
        <div v-if="step === 3">
          <h2>Your score: {{ score }}/{{ numberOfQuestions }}</h2>
          <h3 class="text-right">
            Quiz time:
            {{ timer.hours }} : {{ timer.minutes }} : {{ timer.seconds }}
          </h3>
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
                <tr v-for="verb in quizQuestions" :key="verb.infinitive">
                  <td>{{ verb.infinitive || verb.translation }}</td>
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
      </v-stepper-content>
    </v-stepper-items>
  </v-stepper>
</template>

<script>
import StopwatchComponent from './StopwatchComponent'

export default {
  components: {
    StopwatchComponent,
  },

  props: {
    quizName: {
      type: String,
      required: true,
    },
    quizAllQuestions: {
      type: Array,
      required: true,
    },
  },

  data() {
    return {
      shuffleMode: true,
      quizQuestions: [],
      numberOfQuestions: null,
      currentIndex: 0,
      currentAnswer: null,
      score: 0,
      step: 1,
      timer: {
        hours: 0,
        minutes: 0,
        seconds: 0,
      },
    }
  },

  computed: {
    quizId() {
      return this.$route.params.id
    },
  },

  watch: {
    numberOfQuestions() {
      this.initQuizQuestions()
    },

    shuffleMode() {
      this.initQuizQuestions()
    },
  },

  methods: {
    replaceSpecialCharacters(inputString) {
      const replacements = {
        ț: 't',
        ă: 'a',
        ș: 's',
        î: 'i',
        â: 'a',
      }

      const pattern = new RegExp(Object.keys(replacements).join('|'), 'g')
      return inputString ? inputString.trim().replace(pattern, (match) => replacements[match]) : ''
    },

    initQuizQuestions() {
      this.quizQuestions = this.quizAllQuestions
      this.numberOfQuestions =
        this.numberOfQuestions === 'All'
          ? this.quizAllQuestions.length
          : this.numberOfQuestions
      if (this.shuffleMode) {
        this.quizQuestions = this.quizQuestions
          .sort(() => Math.random() - Math.random())
          .slice(0, this.numberOfQuestions)
      } else if (this.quizId === '3') {
        this.quizQuestions = this.quizQuestions
          .sort((a, b) => a.translation.localeCompare(b.translation))
          .slice(0, this.numberOfQuestions)
      } else {
        this.quizQuestions = this.quizQuestions
          .sort((a, b) => a.infinitive.localeCompare(b.infinitive))
          .slice(0, this.numberOfQuestions)
      }
    },

    startQuiz() {
      this.step = 2
    },

    nextQuestion() {
      this.currentAnswer = this.currentAnswer.toLowerCase()
      this.quizQuestions[this.currentIndex].yourAnswer = this.currentAnswer
      this.calculateScore()
      if (this.currentIndex < this.quizQuestions.length - 1) {
        this.currentAnswer = null
        this.currentIndex++
      } else {
        this.step = 3
      }
    },

    calculateScore() {
      const correctAnswers =
        this.quizQuestions[this.currentIndex].correctAnswer.split('/')

      if (
        this.replaceSpecialCharacters(correctAnswers[0]) === this.replaceSpecialCharacters(this.currentAnswer) ||
        this.replaceSpecialCharacters(correctAnswers[1]) === this.replaceSpecialCharacters(this.currentAnswer) ||
        this.quizQuestions[this.currentIndex].correctAnswer ===
          this.currentAnswer
      ) {
        this.quizQuestions[this.currentIndex].isCorrect = true
        this.score++
      } else {
        this.quizQuestions[this.currentIndex].isCorrect = false
      }
    },

    startNewQuiz() {
      this.step = 1
      this.currentIndex = 0
      this.quizQuestions = []
      this.shuffleMode = true
      this.numberOfQuestions = null
      this.currentAnswer = null
      this.score = 0
    },

    onTimerChanged(timer) {
      this.timer = timer
    },
  },
}
</script>
