<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="10" md="8" lg="6" xl="5">
      <VerbsTableComponent />
    </v-col>
  </v-row>
</template>

<script>
import VerbsTableComponent from '../components/VerbsTableComponent'
export default {
  name: 'IndexPage',

  components: {
    VerbsTableComponent,
  },
}
</script>
