<template>
  <q-page class="q-pa-xl">
    <div v-if="init" class="row absolute-center">
      <div class="col-12">
        <q-btn no-caps label="Iniciar Atendimento" color="primary" @click="next('welcome')" :loading="loading"></q-btn>
      </div>
    </div>

    <div v-else class="row full-width justify-center">
      <div class="col-12 col-md-6">
        <Chat :conversations="conversations" />
      </div>
    </div>

    <q-footer v-show="showInput" class="bg-black text-primary">
      <div class="row full-width text-center">
        <div class="col-12">
          <q-input ref="input" v-model="text" filled square label="Responda" @keyup.enter="next('conversation')"/>
        </div>
      </div>
    </q-footer>
  </q-page>
</template>

<script>
import Chat from 'components/Chat'

export default {
  name: 'Index',

  components: {
    Chat
  },

  data () {
    return {
      conversations: [],
      loading: false,
      init: true,
      context: null,
      text: ''
    }
  },

  watch: {
    showInput (newVal) {
      if (newVal) {
        this.$nextTick(() => this.$refs.input.$el.focus())
      }
    }
  },

  computed: {
    showInput () {
      return this.$store.getters['question/showInput']
    }
  },

  methods: {
    next (method) {
      switch (method) {
        case 'welcome':
          this.welcome()
          break
        case 'conversation': {
          this.setConversationAnswer()
          this.setLoadingConversationQuestion({ text: '', loading: true })
          this.conversation(this.text)
          break
        }
        default:
          break
      }

      this.text = ''
      this.$store.dispatch('question/setShowInput', false)
    },

    async welcome () {
      // http://helloorama-form-api.us-east-1.elasticbeanstalk.com/api/register
      this.loading = true
      const { data } = await this.$axios.get('http://localhost:3000/api/register/welcome')
      this.playOutput(data)
    },

    async conversation (userInput) {
      this.loading = true

      const payload = {
        text: this.text
      }

      if (this.context) {
        payload.context = this.context
      }

      const { data } = await this.$axios.post('http://localhost:3000/api/register/conversation', payload)
      this.context = [data.context[0]] || null
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

              if (this.conversations.length === 0) {
                this.setLoadingConversationQuestion({ text: response.text })
              } else {
                this.setConversationQuestion({ text: response.text, loading: false })
              }

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
    },

    setLoadingConversationQuestion (question) {
      this.conversations.push({ question })
    },

    setConversationQuestion (question) {
      const index = this.conversations.length - 1
      const conversation = this.conversations[index]
      conversation.question = question
      this.$set(this.conversations, index, conversation)
    },

    setConversationAnswer () {
      const index = this.conversations.length - 1
      const conversation = this.conversations[index]
      conversation.answer = { text: this.text }
      this.$set(this.conversations, index, conversation)
    }
  }
}
</script>
