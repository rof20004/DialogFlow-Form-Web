<template>
  <q-page class="flex flex-center">
    <q-btn v-if="init" no-caps label="Iniciar Atendimento" color="primary" @click="next('welcome')" :loading="loading"></q-btn>
    <Question :question="question" :method="method" @next="next($event)"/>
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
      question: {},
      loading: false,
      method: '',
      init: true
    }
  },

  methods: {
    next (method) {
      switch (method) {
        case 'welcome':
          this.question = {}
          this.method = method
          this.welcome()
          break
        case 'economizar':
          this.question = {}
          this.method = method
          alert('OK')
          break
        default:
          break
      }
    },

    async welcome () {
      this.loading = true
      const { data } = await this.$axios.get('http://http://helloorama-form-api.us-east-1.elasticbeanstalk.com//api/register')
      this.playOutput(data)
    },

    playOutput (response) {
      const buf = new Uint8Array(response.buffer.data).buffer
      const audioContext = new AudioContext()
      let outputSource
      try {
        if (buf.byteLength > 0) {
          audioContext.decodeAudioData(buf,
            (buffer) => {
              audioContext.resume()
              outputSource = audioContext.createBufferSource()
              outputSource.connect(audioContext.destination)
              outputSource.buffer = buffer
              outputSource.start(0)
              this.question = { text: response.text }
              this.init = false
              this.loading = false
            },
            function () {
              console.log(arguments)
            })
        }
      } catch (e) {
        console.log(e)
      }
    }
  }
}
</script>
