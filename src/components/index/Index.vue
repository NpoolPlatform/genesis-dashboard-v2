<template>
  <Apps />
  <Roles />
  <Users />
  <Auths />
</template>

<script setup lang='ts'>
import { onMounted, computed, defineAsyncComponent } from 'vue'
import {
  App,
  Auth,
  NotifyType,
  Role,
  useGenesisAdminStore,
  useLocalUserStore,
  User
} from 'npool-cli-v4'
import { useRouter } from 'vue-router'
import { AppID } from 'src/const/const'

const Apps = defineAsyncComponent(() => import('src/components/index/App.vue'))
const Roles = defineAsyncComponent(() => import('src/components/index/Role.vue'))
const Users = defineAsyncComponent(() => import('src/components/index/User.vue'))
const Auths = defineAsyncComponent(() => import('src/components/index/Auth.vue'))

const genesisadmin = useGenesisAdminStore()
const apps = computed(() => genesisadmin.Apps)
const roles = computed(() => genesisadmin.Roles)
const users = computed(() => genesisadmin.Users)
const auths = computed(() => genesisadmin.Auths.get(AppID))

const user = useLocalUserStore()
const logiend = computed(() => user.logined)

const router = useRouter()

const genesisUserCreated = computed(() => {
  let created = false
  users.value.forEach((u) => {
    if (u.AppID === AppID) {
      created = true
    }
  })
  return created
})

onMounted(() => {
  genesisadmin.getAdminApps({
    Message: {}
  }, (apps: Array<App>, error: boolean) => {
    if (error) {
      void router.push({ path: '/signin' })
      return
    }
    if (apps.length > 0 && roles.value.length > 0 && users.value.length > 0 && auths.value?.length && !logiend.value) {
      void router.push({ path: '/signin' })
    }
  })

  genesisadmin.getGenesisRoles({
    Message: {}
  }, (roles: Array<Role>, error: boolean) => {
    if (error) {
      void router.push({ path: '/signin' })
      return
    }
    if (apps.value.length > 0 && roles.length > 0 && users.value.length > 0 && auths.value?.length && !logiend.value) {
      void router.push({ path: '/signin' })
    }
  })

  genesisadmin.getGenesisUsers({
    Message: {}
  }, (users: Array<User>, error: boolean) => {
    if (error) {
      void router.push({ path: '/signin' })
      return
    }

    if (!genesisUserCreated.value) {
      return
    }

    if (apps.value.length > 0 && roles.value.length > 0 && users.length > 0 && auths.value?.length && !logiend.value) {
      void router.push({ path: '/signin' })
    }
  })

  genesisadmin.getGenesisAuths({
    TargetAppID: AppID,
    Message: {
      Error: {
        Title: 'MSG_CREATE_ADMIN_APPS',
        Description: 'MSG_CREATE_ADMIN_APPS_FAIL',
        Popup: true,
        Type: NotifyType.Error
      }
    }
  }, (auths: Array<Auth>, error: boolean) => {
    if (error) {
      void router.push({ path: '/signin' })
      return
    }

    if (apps.value.length > 0 && roles.value.length > 0 && users.value.length > 0 && auths.length > 0 && !logiend.value) {
      void router.push({ path: '/signin' })
    }
  })
})

</script>

<style lang='sass' scoped>
</style>
