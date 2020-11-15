<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar class="bg-white">
        <q-toolbar-title class="text-black">
          <img src="~assets/logo.png" alt="Logomarca do Hello Órama" height="60">
        </q-toolbar-title>

        <q-btn
          color="green"
          class="desktop-only"
          label="Abra sua conta"
        />

        <q-btn
          flat
          dense
          round
          color="black"
          icon="menu"
          aria-label="Menu"
          class="mobile-only"
        />
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
export default {
  name: 'LandingPageLayout'
}
</script>
