<template>
  <q-page class="flex flex-center">
    <Question :question="questions[index]" @next="next"/>
  </q-page>
</template>

<script>
import Question from 'components/Question'

export default {
  name: 'Index',

  components: {
    Question
  },

  data () {
    return {
      questions: [
        { label: 'Seu nome', text: 'Olá, seja bem vindo ao Hello Órama, antes de começar preciso saber seu nome, poderia me informar?' },
        { label: 'Renda', text: 'Legal, e qual sua renda mínima?' },
        { label: null, text: 'Obrigado, você será redirecionado para o portal dentro de instantes' }
      ],
      question: {},
      index: 0
    }
  },

  methods: {
    next () {
      this.index += 1
    }
  },

  mounted () {
    this.question = this.questions[this.index]
  }
}
</script>
