<template>
  <v-col>
    <v-row v-if="voiceOptions.length > 0" class="col-12">
      <v-select
        v-model="selectedVoiceIndex"
        :items="voiceOptions"
        label="Select speech voice"
      ></v-select>
    </v-row>
    <v-row class="col-12">
      <v-card>
        <v-card-title>
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="Search"
            single-line
            hide-details
          ></v-text-field>
          <v-data-table
            :headers="headers"
            :items="items"
            :search="search"
            :items-per-page="10"
            :footer-props="footerProps"
            class="elevation-1"
          >
            <template #[`item.actions`]="{ item }">
              <v-icon class="mr-2" @click="speechText(item)">
                mdi-volume-high
              </v-icon>
            </template>
          </v-data-table>
        </v-card-title>
      </v-card>
    </v-row>
  </v-col>
</template>

<script>
import irregularVerbs from '@/static/irregularVerbs.json'

export default {
  data() {
    return {
      search: '',
      voices: [],
      selectedVoiceIndex: null,
      footerProps: {
        'items-per-page-options': [10, 25, 50, 75, { text: 'All', value: -1 }],
      },
      headers: [
        {
          text: 'Infinitive',
          align: 'start',
          value: 'infinitive',
        },
        { text: 'Past simple', value: 'pastSimple' },
        { text: 'Past participle', value: 'pastParticiple' },
        { text: 'Translation (RO)', value: 'translation' },
        { text: 'Speech', value: 'actions', sortable: false },
      ],
      items: irregularVerbs,
    }
  },

  computed: {
    voiceOptions() {
      return this.voices.map((voice, index) => ({
        text: voice.name,
        value: index,
      }))
    },

    selectedVoice() {
      return this.selectedVoiceIndex !== null
        ? this.voices[this.selectedVoiceIndex]
        : ''
    },
  },

  mounted() {
    // Ensure that speechSynthesis is supported in the browser
    if ('speechSynthesis' in window) {
      // Set up the event listener
      window.speechSynthesis.addEventListener(
        'voiceschanged',
        this.handleVoicesChanged
      )

      // Initial population of voices
      this.populateVoices()
    }
  },

  beforeDestroy() {
    // Remove the event listener when the component is destroyed
    window.speechSynthesis.removeEventListener(
      'voiceschanged',
      this.handleVoicesChanged
    )
  },

  methods: {
    handleVoicesChanged() {
      // Handle the voiceschanged event
      this.populateVoices()
    },

    populateVoices() {
      // Fetch and update the available voices
      const allVoices = window.speechSynthesis.getVoices()
      this.voices = allVoices
        .filter((voice) => voice.lang === 'en-US' || voice.lang === 'en-GB')
        .map((voice) => ({
          name: `${voice.name} (${voice.lang})`,
          value: voice,
        }))
    },

    speechText(item) {
      const textToSpeech = `${item.infinitive}, ${item.pastSimple.split(
        '/'
      )}, ${item.pastParticiple}`
      // Create a new SpeechSynthesisUtterance object
      const utterance = new SpeechSynthesisUtterance(textToSpeech)

      if ('speechSynthesis' in window) {
        if (!this.selectedVoice) {
          const voices = window.speechSynthesis.getVoices()
          // Filter all en-US or en-GB voices and use the first one as speech synthesis voice
          utterance.voice = voices.filter(
            (voice) => voice.lang === 'en-US' || voice.lang === 'en-GB'
          )[0]
        } else {
          utterance.voice = this.selectedVoice.value
        }
        // Speak the text
        window.speechSynthesis.speak(utterance)
      } else {
        alert('Your browser does not support speech synthesis.')
      }
    },
  },
}
</script>
