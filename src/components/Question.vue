<template>
  <div>
    <transition
        appear
        enter-active-class="animated fadeInDown"
        leave-active-class="animated fadeOutUp"
        mode="out-in"
      >
      <div class="row full-width text-center" :key="question.label">
        <div class="col-12">
          <p class="text-h4">{{ text }}</p>
        </div>
      </div>
    </transition>

    <q-footer v-show="question.label && showInput" class="bg-white text-primary">
      <div class="row full-width text-center">
        <div class="col-12">
          <q-input ref="input" filled square :label="question.label" @keyup.enter="$emit('next')"/>
        </div>
      </div>
    </q-footer>
  </div>
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
      cont: 0
    }
  },

  computed: {
    showInput () {
      const ok = this.text.length === this.cont

      if (ok) {
        this.$nextTick(() => this.$refs.input.$el.focus())
      }

      return ok
    }
  },

  watch: {
    question () {
      this.text = ''
      this.cont = 0
      this.printChar()
    },

    showInput (newVal) {
      if (newVal && !this.question.label) {
        setTimeout(() => { window.location.href = 'https://www.google.com.br' }, 2000)
      }
    }
  },

  methods: {
    printChar () {
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
