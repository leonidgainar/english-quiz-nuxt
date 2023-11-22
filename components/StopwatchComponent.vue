<template>
  <h3 class="text-right">
    Quiz time: {{ hours }} : {{ minutes }} : {{ seconds }}
  </h3>
</template>

<script>
export default {
  props: {
    isQuizRunning: {
      type: Boolean,
      default: true,
    },
  },

  data() {
    return {
      hour: 0,
      min: 0,
      sec: 0,
      ms: 0,
    }
  },

  computed: {
    hours() {
      return this.hour < 10 ? '0' + this.hour : this.hour
    },

    minutes() {
      return this.min < 10 ? '0' + this.min : this.min
    },

    seconds() {
      return this.sec < 10 ? '0' + this.sec : this.sec
    },
  },

  watch: {
    seconds() {
      this.$emit('timer-changed', {
        hours: this.hours,
        minutes: this.minutes,
        seconds: this.seconds,
      })
    },
  },

  created() {
    setInterval(this.incrementTimer, 10)
  },

  methods: {
    incrementTimer() {
      if (!this.isQuizRunning) {
        return
      }
      this.ms += 10
      if (this.ms === 1000) {
        this.ms = 0
        this.sec++
        if (this.sec === 60) {
          this.sec = 0
          this.min++
          if (this.min === 60) {
            this.min = 0
            this.hour++
          }
        }
      }
    },
  },
}
</script>
