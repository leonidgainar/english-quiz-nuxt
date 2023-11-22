<template>
  <v-row justify="center">
    <v-dialog v-model="dialog" persistent max-width="290">
      <v-card>
        <v-card-title class="text-h5">
          Allow microphone
        </v-card-title>
        <v-card-text>Microphone access is denied. Please enable it in your browser settings.</v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="green darken-1" text @click="closeDialog">
            OK
          </v-btn>
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
  }
}
</script>
