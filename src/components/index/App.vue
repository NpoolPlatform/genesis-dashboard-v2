<template>
  <q-table
    dense
    flat
    :title='$t("MSG_APPS")'
    :rows='apps'
    row-key='ID'
  >
    <template #top-right>
      <q-btn
        v-if='apps.length === 0'
        dense
        flat
        class='btn flat'
        :label='$t("MSG_CREATE_ADMIN_APPS")'
        @click='onCreateAdminApps'
      />
    </template>
  </q-table>
</template>

<script setup lang='ts'>
import { computed } from 'vue'
import {
  NotifyType,
  useGenesisAdminStore
} from 'npool-cli-v4'

const genesisadmin = useGenesisAdminStore()
const apps = computed(() => genesisadmin.Apps)

const onCreateAdminApps = () => {
  genesisadmin.createAdminApps({
    Message: {
      Error: {
        Title: 'MSG_CREATE_ADMIN_APPS',
        Description: 'MSG_CREATE_ADMIN_APPS_FAIL',
        Popup: true,
        Type: NotifyType.Error
      }
    }
  }, () => {
    // TODO
  })
}

</script>

<style lang='sass' scoped>
</style>
