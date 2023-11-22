<template>
  <v-row justify="center">
    <v-dialog v-model="dialog" persistent max-width="500">
      <v-card>
        <v-card-title class="text-h5"> Allow microphone </v-card-title>
        <v-card-text>
          Microphone access is denied. Please enable it in your browser
          settings.
        </v-card-text>
        <v-card-text> 1) Instructions for desktop (Chrome): </v-card-text>
        <v-card-text> Step 1: </v-card-text>
        <v-img
          class="mb-5 mx-5"
          :src="require('@/assets/instruction-1.png')"
          alt="instruction step 1"
        ></v-img>
        <v-card-text> Step 2: </v-card-text>
        <v-img
          class="mb-5 mx-5"
          :src="require('@/assets/instruction-2.png')"
          alt="instruction step 2"
        ></v-img>
        <v-card-text>
          Step 3: Toggle "Allow microphone" switch and the prompt will reappear
        </v-card-text>
        <v-card-text>
          Step 4: Click "Allow" to enable microphone access
        </v-card-text>
        <v-card-text> 2) Instructions for mobile: </v-card-text>
        <v-card-text>Step 1: Refresh the page</v-card-text>
        <v-card-text>
          Step 2: Toggle "Allow microphone" switch and the prompt will reappear
        </v-card-text>
        <v-card-text>
          Step 3: Click "Allow" to enable microphone access
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="green darken-1" dark @click="closeDialog"> OK </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>

<script lang="ts">
export default {
  props: {
    showDialog: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      dialog: false,
    }
  },
  watch: {
    showDialog() {
      this.dialog = this.showDialog
    },
  },
  methods: {
    closeDialog() {
      this.dialog = false
      this.$emit('close-dialog')
    },
  },
}
</script>
