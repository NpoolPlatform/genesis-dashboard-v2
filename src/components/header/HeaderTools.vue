<template>
  <div class='row'>
    <HeaderToolBtn :icon='bellIcon' />
    <AvatarDropdown v-if='logined' />
  </div>
</template>

<script setup lang='ts'>
import { user, notif } from 'src/npoolstore'
import { defineAsyncComponent, computed } from 'vue'

import bellNoMsg from '../../assets/bell-no-msg.svg'
import bellMsg from '../../assets/bell-msg.svg'

const AvatarDropdown = defineAsyncComponent(() => import('src/components/avatar/AvatarDropdown.vue'))
const HeaderToolBtn = defineAsyncComponent(() => import('src/components/header/HeaderToolBtn.vue'))

const _user = user.useLocalUserStore()
const logined = computed(() => _user.logined)

const _notif = notif.useNotifStore()
const bellIcon = computed(() => _notif.unreads(undefined, undefined) ? bellMsg : bellNoMsg)

</script>

<style lang='sass' scoped>
</style>
