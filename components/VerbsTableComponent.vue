<template>
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
</template>

<script>
import irregularVerbs from '@/static/irregularVerbs.json'

export default {
  data() {
    return {
      search: '',
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
        { text: 'Speech', value: 'actions' },
      ],
      items: irregularVerbs,
    }
  },

  methods: {
    speechText(item) {
      const textToSpeech = `${item.infinitive}, ${item.pastSimple.split(
        '/'
      )}, ${item.pastParticiple}`
      // Create a new SpeechSynthesisUtterance object
      const utterance = new SpeechSynthesisUtterance(textToSpeech)

      // Filter all en-US or en-GB voices and use the first one as speech synthesis voice
      const voices = window.speechSynthesis.getVoices()
      utterance.voice = voices.filter(
        (voice) => voice.lang === 'en-US' || voice.lang === 'en-GB'
      )[0]
      // Speak the text
      window.speechSynthesis.speak(utterance)
    },
  },
}
</script>
