<template>
  <q-table
    dense
    flat
    :title='$t("MSG_AUTHS")'
    :rows='auths'
    row-key='ID'
  >
    <template #top-right>
      <q-btn
        v-if='auths?.length'
        dense
        flat
        class='btn flat'
        :label='$t("MSG_AUTHORIZE_GENESIS")'
        @click='onAuthorizeGenesis'
      />
    </template>
  </q-table>
</template>

<script setup lang='ts'>
import { computed } from 'vue'
import {
  useGenesisAdminStore
} from 'npool-cli-v4'
import { AppID } from 'src/const/const'

const genesisadmin = useGenesisAdminStore()
const auths = computed(() => genesisadmin.Auths?.get(AppID))

const onAuthorizeGenesis = () => {
  genesisadmin.authorizeGenesis({
    Message: {}
  }, () => {
    // TODO
  })
}

</script>

<style lang='sass' scoped>
</style>
