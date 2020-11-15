<template>
  <q-page class="q-pa-xl">
    <div class="row full-width justify-center">
      <div class="col-12 col-md-6">
        <Chat :conversations="conversations" />
      </div>
    </div>

    <q-footer v-show="showInput" class="bg-black text-primary">
      <div class="row full-width text-center">
        <div class="col-12">
          <q-input ref="input" v-model="text" filled square label="Responda" @keyup.enter="next('conversation')">
            <template v-slot:after>
              <q-btn round flat icon="mic" color="white" @click="voice" />
            </template>
          </q-input>
        </div>
      </div>
    </q-footer>
  </q-page>
</template>

<script>
/* eslint-disable new-cap */
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
      text: '',
      recognition: null,
      recognizing: false,
      ignore_onend: false,
      final_transcript: '',
      interim_transcript: ''
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
          this.createConversationQuestion({ text: '', loading: true })
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
      this.loading = true
      const { data } = await this.$axios.get('https://helloorama-form-api.herokuapp.com/api/register/welcome')
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

      const { data } = await this.$axios.post('https://helloorama-form-api.herokuapp.com/api/register/conversation', payload)
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
                this.createConversationQuestion({ text: response.text })
              } else {
                this.updateConversationQuestion({ text: response.text, loading: false })
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

    createConversationQuestion (question) {
      this.conversations.push({ question })
    },

    updateConversationQuestion (question) {
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
    },

    voice () {
      this.text = ''
      this.final_transcript = ''

      this.recognition = new window.webkitSpeechRecognition()

      this.recognition.onstart = () => {
        this.recognizing = true
        console.log('Reconhecendo')
      }

      this.recognition.onerror = event => {
        if (event.error === 'no-speech') {
          this.ignore_onend = true
        }
        if (event.error === 'audio-capture') {
          this.ignore_onend = true
        }
        if (event.error === 'not-allowed') {
          this.ignore_onend = true
        }
      }

      this.recognition.onend = () => {
        this.recognizing = false
      }

      this.recognition.onresult = event => {
        for (var i = event.resultIndex; i < event.results.length; ++i) {
          if (event.results[i].isFinal) {
            this.final_transcript += event.results[i][0].transcript
          }
        }

        this.text = this.final_transcript
        this.next('conversation')
      }

      this.recognition.lang = 'pt-BR'
      this.recognition.start()
    }
  },

  mounted () {
    this.next('welcome')
  }
}
</script>
