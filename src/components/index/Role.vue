<template>
  <q-table
    dense
    flat
    :title='$t("MSG_ROLES")'
    :rows='roles'
    row-key='ID'
  >
    <template #top-right>
      <q-btn
        v-if='roles.length <= 1'
        dense
        flat
        class='btn flat'
        :label='$t("MSG_CREATE_GENESIS_ROLES")'
        @click='onCreateGenesisRoles'
      />
    </template>
  </q-table>
</template>

<script setup lang='ts'>
import { computed } from 'vue'
import { notify, admin } from 'src/npoolstore'

const _admin = admin.useAdminStore()
const roles = computed(() => _admin.Roles)

const onCreateGenesisRoles = () => {
  _admin.createGenesisRoles({
    Message: {
      Error: {
        Title: 'MSG_CREATE_GENESIS_ROLES',
        Description: 'MSG_CREATE_GENESIS_ROLES_FAIL',
        Popup: true,
        Type: notify.NotifyType.Error
      }
    }
  }, () => {
    // TODO
  })
}

</script>

<style lang='sass' scoped>
</style>
