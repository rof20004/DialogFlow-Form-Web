<template>
  <div v-if="Object.keys(question).length > 0">
    <transition
        appear
        enter-active-class="animated fadeInDown"
        leave-active-class="animated fadeOutUp"
        mode="out-in"
      >
      <div class="row full-width text-center" :key="question.text">
        <div class="col-12">
          <p class="text-h4">{{ text }}</p>
        </div>
      </div>
    </transition>

    <q-footer v-show="showInput" class="bg-white text-primary">
      <WelcomeInput v-if="method === 'welcome'" :showInput="showInput" @next="$emit('next', $event)" />
    </q-footer>
  </div>
</template>

<script>
import WelcomeInput from 'components/WelcomeInput'

export default {
  name: 'Question',

  props: {
    question: {
      type: Object,
      required: true
    },

    method: {
      type: String,
      required: true
    }
  },

  components: {
    WelcomeInput
  },

  data () {
    return {
      text: '',
      cont: 0
    }
  },

  computed: {
    showInput () {
      if (Object.keys(this.question).length === 0) {
        return false
      }

      return this.text.length === this.cont
    }
  },

  watch: {
    question () {
      this.text = ''
      this.cont = 0
      this.printChar()
    },

    showInput (newVal) {
      if (newVal && this.question.text.indexOf('instantes') !== -1) {
        setTimeout(() => { window.location.href = 'https://www.google.com.br' }, 2000)
      }
    }
  },

  methods: {
    printChar () {
      if (Object.keys(this.question).length === 0) {
        return
      }

      for (const char of this.question.text) {
        this.cont += 1
        setTimeout(() => { this.text += char }, 55 * this.cont)
      }
    }
  },

  mounted () {
    this.printChar()
  }
}
</script>
