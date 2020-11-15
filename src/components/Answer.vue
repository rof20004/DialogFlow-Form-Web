<template>
  <div v-if="answer">
    <transition
        appear
        enter-active-class="animated fadeInDown"
        leave-active-class="animated fadeOutUp"
        mode="out-in"
      >
      <q-chat-message avatar="~assets/person.jpg" sent>
        <div class="row full-width text-right" :key="answer.text">
          <div class="col-12">
            <p class="text-subtitle1">{{ answer.text }}</p>
          </div>
        </div>
      </q-chat-message>
    </transition>
  </div>
</template>

<script>
export default {
  name: 'Answer',

  props: {
    answer: {
      type: Object,
      required: true
    }
  }
}
</script>
