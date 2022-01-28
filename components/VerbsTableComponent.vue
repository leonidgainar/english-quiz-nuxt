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
      ></v-data-table>
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
        { text: 'Translation', value: 'translation', sortable: false },
        { text: 'Past simple', value: 'pastSimple' },
        { text: 'Past participle', value: 'pastParticiple' },
      ],
      items: irregularVerbs,
    }
  },
}
</script>
