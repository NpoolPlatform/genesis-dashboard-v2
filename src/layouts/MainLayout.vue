<template>
  <q-layout view='hHh Lpr fFf'>
    <MainHeader />

    <q-page-container>
      <router-view />
    </q-page-container>

    <Footer />
  </q-layout>
</template>

<script setup lang='ts'>
import { defineAsyncComponent, onMounted } from 'vue'
import { useNotificationStore, notify } from 'npool-cli-v2'

const MainHeader = defineAsyncComponent(() => import('src/components/header/MainHeader.vue'))
const Footer = defineAsyncComponent(() => import('src/components/footer/Footer.vue'))

const notification = useNotificationStore()

onMounted(() => {
  notification.$subscribe((_, state) => {
    state.Notifications.forEach((notif, index) => {
      if (notif.Popup) {
        state.Notifications.splice(index, 1)
        notify(notif)
      }
    })
  })
})

</script>

<stype lang='sass' scoped>
</stype>
