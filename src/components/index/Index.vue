<template>
  <div />
</template>

<script setup lang='ts'>
import { onMounted } from 'vue'
import { App, useGenesisAdminStore } from 'npool-cli-v4'
import { useRouter } from 'vue-router'

const genesisadmin = useGenesisAdminStore()

const router = useRouter()

onMounted(() => {
  genesisadmin.getAdminApps({
    Message: {}
  }, (apps: Array<App>, error: boolean) => {
    if (error) {
      void router.push({ path: '/signin' })
      return
    }
    if (apps.length > 0) {
      void router.push({ path: '/signin' })
    }
  })
})

</script>

<style lang='sass' scoped>
</style>
