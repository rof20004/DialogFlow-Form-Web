<template>
  <div id="q-app">
    <router-view />
  </div>
</template>
<script>
export default {
  name: 'App',

  data () {
    return {
      isOnline: true
    }
  },

  methods: {
    notifyOffline () {
      this.$q.notify({
        position: 'bottom',
        timeout: 10000,
        textColor: 'red',
        color: 'white',
        message: 'Você está offline no momento',
        icon: 'signal_wifi_off',
        actions: [{ label: 'Fechar', color: 'black' }]
      })
    },

    notifyOnline () {
      this.$q.notify({
        position: 'bottom',
        timeout: 10000,
        textColor: 'blue',
        color: 'white',
        message: 'Sua conexão com a internet foi restaurada',
        icon: 'signal_wifi_4_bar',
        actions: [{ label: 'Fechar', color: 'black' }]
      })
    },

    checkConnectivity () {
      setInterval(() => {
        if (!navigator.onLine && this.isOnline) {
          this.notifyOffline()
          this.isOnline = false
        } else if (navigator.onLine && !this.isOnline) {
          this.notifyOnline()
          this.isOnline = true
        }
      }, 1000)
    }
  },

  created () {
    this.$q.dark.set(true)
    this.checkConnectivity()
  }
}
</script>
