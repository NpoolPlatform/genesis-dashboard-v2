<template>
  <div class='row'>
    <HeaderToolBtn :icon='bellIcon' />
    <AvatarDropdown v-if='logined' />
  </div>
</template>

<script setup lang='ts'>
import { useMailboxStore } from 'npool-cli-v2'
import { useLocalUserStore } from 'npool-cli-v4'
import { defineAsyncComponent, computed } from 'vue'

import bellNoMsg from '../../assets/bell-no-msg.svg'
import bellMsg from '../../assets/bell-msg.svg'

const AvatarDropdown = defineAsyncComponent(() => import('src/components/avatar/AvatarDropdown.vue'))
const HeaderToolBtn = defineAsyncComponent(() => import('src/components/header/HeaderToolBtn.vue'))

const user = useLocalUserStore()
const logined = computed(() => user.logined)

const mailbox = useMailboxStore()
const bellIcon = computed(() => mailbox.Notifications.length > 0 ? bellMsg : bellNoMsg)

</script>

<style lang='sass' scoped>
</style>
