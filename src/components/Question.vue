<template>
  <transition
      appear
      enter-active-class="animated fadeInDown"
      leave-active-class="animated fadeOutUp"
      mode="out-in"
    >
    <q-chat-message avatar="~assets/avatar.png">
      <div class="row full-width text-left" :key="question.text">
        <div class="col-12">
          <p class="text-subtitle1" v-for="(line, index) in text.split('\n')" :key="index">
            {{ line }}
          </p>
        </div>
      </div>

      <q-spinner-dots v-if="question.loading" size="2rem" />
    </q-chat-message>
  </transition>
</template>

<script>
export default {
  name: 'Question',

  props: {
    question: {
      type: Object,
      required: true
    }
  },

  data () {
    return {
      text: '',
      cont: 0,
      value: ''
    }
  },

  watch: {
    question () {
      this.text = ''
      this.cont = 0
      this.printChar()
      this.$emit('scroll')
    },

    showInput (newVal) {
      if (newVal) {
        this.$store.dispatch('question/setShowInput', newVal)
      } else {
        this.$store.dispatch('question/setShowInput', false)
      }

      if (newVal && this.question.text.indexOf('instantes') !== -1) {
        this.$store.dispatch('question/setShowInput', false)
        setTimeout(() => { window.location.href = 'https://mega-hack-hello-orama.web.app/' }, 2000)
      }
    }
  },

  computed: {
    showInput () {
      return this.text.length === this.cont
    }
  },

  methods: {
    printChar () {
      for (const char of this.question.text) {
        this.cont += 1
        setTimeout(() => { this.text += char; this.$emit('scroll') }, 60 * this.cont)
      }
    }
  },

  mounted () {
    this.printChar()
  }
}
</script>
