<template>
  <div>
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
            <v-row>
              <v-col class="col-12 col-md-6 py-0">
                <v-switch v-model="shuffleMode" label="Shuffle mode"></v-switch>
              </v-col>
              <v-col v-if="quizRequiresMicrophone" class="col-12 col-md-6 py-0">
                <v-switch
                  v-model="isMicrophoneAllowed"
                  label="Allow microphone"
                ></v-switch>
              </v-col>
            </v-row>
            <v-btn
              v-if="quizRequiresMicrophone"
              class="mt-10"
              color="primary"
              :disabled="shouldDisableButton"
              @click="startQuiz"
            >
              Start quiz
            </v-btn>
            <v-btn
              v-else
              class="mt-10"
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
            <StopwatchComponent
              :is-quiz-running="isQuizRunning"
              @timer-changed="onTimerChanged"
            />
            <h2>
              {{ currentIndex + 1 }}/{{ numberOfQuestions }}
              {{ quizNames[quizId - 1] }}
            </h2>
            <h2 class="pt-5">
              {{ quizQuestions[currentIndex].question }}
            </h2>
            <v-row v-if="quizRequiresMicrophone">
              <v-col class="col-10">
                <v-text-field
                  v-model="currentAnswer"
                  label="Your answer"
                  placeholder="Speech your answer"
                  readonly
                ></v-text-field>
              </v-col>
              <v-col class="col-2 mt-5">
                <v-icon v-if="!isRecognitionStarted" @click="startRecognition">
                  mdi-microphone
                </v-icon>
                <v-icon v-else @click="endRecognition">
                  mdi-stop-circle
                </v-icon>
              </v-col>
            </v-row>
            <v-form v-else @submit.prevent="nextQuestion">
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
                  <tr v-for="verb in quizQuestions" :key="verb.question">
                    <td>{{ verb.question }}</td>
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
    <RequestMicrophoneAccessDialog
      :show-dialog="showMicrophoneAccessDialog"
      @close-dialog="showMicrophoneAccessDialog = false"
    />
  </div>
</template>

<script>
import StopwatchComponent from './StopwatchComponent'
import RequestMicrophoneAccessDialog from './RequestMicrophoneAccessDialog.vue'

export default {
  components: {
    StopwatchComponent,
    RequestMicrophoneAccessDialog,
  },

  props: {
    quizName: {
      type: String,
      required: true,
    },
    quizRequiresMicrophone: {
      type: Boolean,
      default: false,
    },
    quizAllQuestions: {
      type: Array,
      required: true,
    },
  },

  data() {
    return {
      quizNames: [
        'Past Simple form of the verb:',
        'Past Participle form of the verb:',
        'Translate in EN the Infinitive of the verb:',
        'Translate in RO the Infinitive of the verb:',
        'Speech Infinitive form:',
        'Speech Past Simple form(s):',
        'Speech Past Participle form(s):',
      ],
      showMicrophoneAccessDialog: false,
      recognition: null,
      isMicrophoneAllowed: false,
      isRecognitionStarted: false,
      mediaStream: null,
      shuffleMode: true,
      quizQuestions: [],
      numberOfQuestions: null,
      currentIndex: 0,
      currentAnswer: null,
      score: 0,
      step: 1,
      isQuizRunning: true,
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
    shouldDisableButton() {
      return (
        this.quizRequiresMicrophone &&
        (!this.numberOfQuestions || !this.isMicrophoneAllowed)
      )
    },
  },

  watch: {
    numberOfQuestions() {
      this.initQuizQuestions()
    },

    shuffleMode() {
      this.initQuizQuestions()
    },

    isMicrophoneAllowed() {
      if (this.quizRequiresMicrophone && this.isMicrophoneAllowed) {
        this.requestMicrophoneAccess()
      }
    },
  },

  mounted() {
    if (this.quizRequiresMicrophone) {
      // Create a new instance of SpeechRecognition
      if (
        'webkitSpeechRecognition' in window ||
        'SpeechRecognition' in window
      ) {
        const SpeechRecognition =
          window.SpeechRecognition || window.webkitSpeechRecognition

        this.recognition = new SpeechRecognition()
        this.recognition.lang = 'en-US'
        this.recognition.continuous = true

        // Add event listeners
        this.recognition.addEventListener('start', this.handleRecognitionStart)
        this.recognition.addEventListener('end', this.handleRecognitionEnd)
        this.recognition.addEventListener(
          'result',
          this.handleRecognitionResult
        )
      } else {
        alert('Speech recognition not supported 😢')
      }
    }
  },

  beforeDestroy() {
    // Remove event listeners
    this.cleanupRecognitionEventListeners()
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
      return inputString
        ? inputString.trim().replace(pattern, (match) => replacements[match])
        : ''
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
      } else {
        this.quizQuestions = this.quizQuestions
          .sort((a, b) => a.infinitive.localeCompare(b.infinitive))
          .slice(0, this.numberOfQuestions)
      }
    },

    startQuiz() {
      this.step = 2
      this.startRecognitionIfRequiredAndEnabled()
    },

    nextQuestion() {
      this.startRecognitionIfRequiredAndEnabled()

      if (!this.currentAnswer) {
        return // Exit early if currentAnswer is not set
      }

      this.currentAnswer = this.currentAnswer.toLowerCase()

      const currentQuestion = this.quizQuestions[this.currentIndex]
      currentQuestion.yourAnswer = this.currentAnswer
      this.calculateScore()

      if (this.currentIndex < this.quizQuestions.length - 1) {
        this.currentAnswer = null
        this.currentIndex++
      } else {
        this.step = 3
        this.endRecognitionIfRequiredAndEnabled()
      }
    },

    calculateScore() {
      const currentQuestion = this.quizQuestions[this.currentIndex]
      const correctAnswers = currentQuestion.correctAnswer.split('/')
      const normalizedCurrentAnswer = this.replaceSpecialCharacters(
        this.currentAnswer
      )

      const isAnswerCorrect = correctAnswers.some(
        (answer) =>
          this.replaceSpecialCharacters(answer) === normalizedCurrentAnswer
      )

      currentQuestion.isCorrect = isAnswerCorrect

      if (isAnswerCorrect) {
        this.score++
      }
    },

    startNewQuiz() {
      this.step = 1
      this.currentIndex = 0
      this.score = 0
      this.quizQuestions = []
      this.shuffleMode = true
      this.isMicrophoneAllowed = false
      this.numberOfQuestions = null
      this.currentAnswer = null
      this.startRecognitionIfRequiredAndEnabled()
    },

    onTimerChanged(timer) {
      this.timer = timer
    },

    requestMicrophoneAccess() {
      navigator.permissions
        .query({ name: 'microphone' })
        .then((permissionStatus) => {
          if (permissionStatus.state === 'granted') {
            // Microphone access already granted
            this.isMicrophoneAllowed = true
          } else if (permissionStatus.state === 'denied') {
            // Microphone access denied; instruct the user to enable it manually
            this.showMicrophoneAccessDialog = true
            this.isMicrophoneAllowed = false
          } else {
            // Microphone access not determined; request permission
            navigator.mediaDevices
              .getUserMedia({ audio: true })
              .then(() => {
                // Microphone access granted
                this.isMicrophoneAllowed = true
              })
              .catch(() => {
                // Microphone access denied
                this.showMicrophoneAccessDialog = true
                this.isMicrophoneAllowed = false
              })
          }
        })
    },

    cleanupRecognitionEventListeners() {
      if (this.recognition) {
        this.recognition.removeEventListener(
          'start',
          this.handleRecognitionStart
        )
        this.recognition.removeEventListener('end', this.handleRecognitionEnd)
        this.recognition.removeEventListener(
          'result',
          this.handleRecognitionResult
        )
        this.handleRecognitionEnd()
      }
    },

    handleRecognitionStart() {
      /* eslint-disable-next-line */
      console.log(
        'Your browser supports speech recognition. Please enable it in your browser settings.'
      )
      this.isQuizRunning = true
    },

    handleRecognitionEnd() {
      if (this.recognition) {
        this.isQuizRunning = false
        this.recognition.stop()
      }
    },

    handleRecognitionResult(event) {
      const result = event.results[0][0].transcript
      if (result && this.step === 2) {
        this.currentAnswer = result
        this.endRecognition()
      }
    },

    startRecognitionIfRequiredAndEnabled() {
      if (this.quizRequiresMicrophone && this.isMicrophoneAllowed) {
        this.startRecognition()
      }
    },

    endRecognitionIfRequiredAndEnabled() {
      if (this.quizRequiresMicrophone && this.isMicrophoneAllowed) {
        this.endRecognition()
      }
    },

    startRecognition() {
      if (this.recognition && !this.isRecognitionStarted) {
        this.isRecognitionStarted = true
        this.recognition.start()
      }
    },

    endRecognition() {
      if (this.recognition && this.isRecognitionStarted) {
        this.isRecognitionStarted = false
        this.recognition.stop()
        if (this.currentAnswer && this.step === 2) {
          setTimeout(() => {
            this.nextQuestion()
          }, 500)
        }
      }
    },
  },
}
</script>
