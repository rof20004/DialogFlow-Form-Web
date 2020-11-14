<template>
  <div class="row full-width text-center">
    <div class="col-12">
      <q-input ref="input" filled square label="Seu nome" @keyup.enter="$emit('next', 'economizar')"/>
    </div>
  </div>
</template>

<script>
export default {
  name: 'WelcomeInput',

  props: {
    text: {
      type: String,
      required: true
    },

    showInput: {
      type: Boolean,
      required: true
    }
  },

  watch: {
    showInput (newVal) {
      if (newVal) {
        this.$nextTick(() => this.$refs.input.$el.focus())
      }
    }
  }
}
</script>
