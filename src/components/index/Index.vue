<template>
  <Apps />
  <Roles />
  <Users />
  <Auths />
</template>

<script setup lang='ts'>
import { onMounted, computed, defineAsyncComponent } from 'vue'
import { app, admin, user, role } from 'src/npoolstore'
import { useRouter } from 'vue-router'
import { AppID } from 'src/const/const'

const Apps = defineAsyncComponent(() => import('src/components/index/App.vue'))
const Roles = defineAsyncComponent(() => import('src/components/index/Role.vue'))
const Users = defineAsyncComponent(() => import('src/components/index/User.vue'))
const Auths = defineAsyncComponent(() => import('src/components/index/Auth.vue'))

const _admin = admin.useAdminStore()
const apps = computed(() => _admin.Apps)
const roles = computed(() => _admin.Roles)
const users = computed(() => _admin.Users)

const _user = user.useLocalUserStore()
const logiend = computed(() => _user.logined)

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
  _admin.getAdminApps({
    Message: {}
  }, (error: boolean, apps?: Array<app.App>) => {
    if (error) {
      void router.push({ path: '/signin' })
      return
    }
    if (apps?.length && roles.value.length > 0 && users.value.length > 0 && !logiend.value) {
      void router.push({ path: '/signin' })
    }
  })

  _admin.getGenesisRoles({
    Message: {}
  }, (error: boolean, roles?: Array<role.Role>) => {
    if (error) {
      void router.push({ path: '/signin' })
      return
    }
    if (apps.value.length > 0 && roles?.length && users.value.length > 0 && !logiend.value) {
      void router.push({ path: '/signin' })
    }
  })

  _admin.getGenesisUsers({
    Message: {}
  }, (error: boolean, users?: Array<user.User>) => {
    if (error) {
      void router.push({ path: '/signin' })
      return
    }

    if (!genesisUserCreated.value && !users?.length) {
      return
    }

    if (apps.value.length > 0 && roles.value.length > 0 && users?.length && !logiend.value) {
      void router.push({ path: '/signin' })
    }
  })
})

</script>

<style lang='sass' scoped>
</style>
