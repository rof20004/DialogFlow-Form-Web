<template>
  <q-scroll-area ref="chatScroll" style="height: calc(100vh - 160px); width: 100%;" :visible="false">
    <div class="row" v-for="(conversation, index) in conversations" :key="index">
      <div class="col-12">
        <Question :question="conversation.question" @scroll="scrollToBottom" />
      </div>

      <div v-if="conversation.answer" class="col-12">
        <Answer :answer="conversation.answer"/>
      </div>
    </div>
  </q-scroll-area>
</template>

<script>
import Question from 'components/Question'
import Answer from 'components/Answer'

export default {
  name: 'Chat',

  components: {
    Question,
    Answer
  },

  props: {
    conversations: {
      type: Array,
      required: true,
      default: () => []
    }
  },

  watch: {
    conversations () {
      this.scrollToBottom()
    }
  },

  methods: {
    scrollToBottom () {
      const scrollArea = this.$refs.chatScroll
      const scrollTarget = scrollArea.getScrollTarget()
      const duration = 300 // ms - use 0 to instant scroll
      scrollArea.setScrollPosition(scrollTarget.scrollHeight, duration)
    }
  }
}
</script>
