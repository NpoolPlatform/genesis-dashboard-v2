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
import { admin } from 'src/npoolstore'

const _admin = admin.useAdminStore()
const auths = computed(() => _admin.Auths)

const onAuthorizeGenesis = () => {
  _admin.authorizeGenesis({
    Message: {}
  }, () => {
    // TODO
  })
}

</script>

<style lang='sass' scoped>
</style>
