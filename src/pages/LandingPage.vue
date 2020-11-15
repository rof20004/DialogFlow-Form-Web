<template>
  <q-page class="flex flex-center">
    <q-img src="~assets/fundo.png" style="height: calc(100vh - 70px);"/>

    <div class="row full-width absolute-center q-pa-md text-center">
      <div class="col-md-2 offset-md-5 col-12">
        <p class="text-h4 bg-white text-black">Investir é para todo mundo</p>
      </div>

      <div class="col-12">
        <p class="text-subtitle1">Comece hoje mesmo sem experiência</p>
      </div>

      <div class="col-12">
        <q-btn no-caps label="Fale com a nossa Agente Virtual" color="primary" to="/form"></q-btn>
      </div>
    </div>
  </q-page>
</template>

<script>
export default {
  name: 'LandingPage'
}
</script>
