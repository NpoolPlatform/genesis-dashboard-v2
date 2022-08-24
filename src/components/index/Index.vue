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
  <q-table
    dense
    flat
    :title='$t("MSG_ROLES")'
    :rows='roles'
    row-key='ID'
  >
    <template #top-right>
      <q-btn
        v-if='roles.length === 0'
        dense
        flat
        class='btn flat'
        :label='$t("MSG_CREATE_GENESIS_ROLES")'
        @click='onCreateGenesisRoles'
      />
    </template>
  </q-table>
  <q-table
    dense
    flat
    :title='$t("MSG_USERS")'
    :rows='users'
    row-key='ID'
  >
    <template #top-right>
      <q-select
        dense :options='myApps'
        v-model='selectedApp'
        :label='$t("MSG_APP")'
      />
      <q-input
        dense
        class='small'
        v-model='password'
        :label='$t("MSG_PASSWORD")'
        disable
      />
      <q-btn
        flat
        dense
        icon='published_with_changes'
        @click='onRefreshPassword'
      />
      <q-input
        dense
        class='small'
        v-model='emailAddress'
        :label='$t("MSG_EMAIL_ADDRESS")'
      />
      <q-btn
        v-if='users.length <= 1'
        dense
        flat
        class='btn flat'
        :label='$t("MSG_CREATE_GENESIS_USER")'
        @click='onCreateGenesisUser'
      />
    </template>
  </q-table>
</template>

<script setup lang='ts'>
import { onMounted, computed, ref } from 'vue'
import { App, encryptPassword, NotifyType, Role, useGenesisAdminStore, User } from 'npool-cli-v4'
import { useRouter } from 'vue-router'
import { AppID } from 'src/const/const'

const genesisadmin = useGenesisAdminStore()
const apps = computed(() => genesisadmin.Apps)
const roles = computed(() => genesisadmin.Roles)
const users = computed(() => genesisadmin.Users)

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
    if (apps.length > 0 && roles.value.length > 0 && users.value.length > 0) {
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
    if (apps.value.length > 0 && roles.length > 0 && users.value.length > 0) {
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

    if (apps.value.length > 0 && roles.value.length > 0 && users.length > 0) {
      void router.push({ path: '/signin' })
    }
  })
})

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
  }, (apps: Array<App>, error: boolean) => {
    if (error) {
      void router.push({ path: '/signin' })
      return
    }
    if (apps.length > 0 && roles.value.length > 0 && users.value.length > 0) {
      void router.push({ path: '/signin' })
    }
  })
}

const onCreateGenesisRoles = () => {
  genesisadmin.createGenesisRoles({
    Message: {
      Error: {
        Title: 'MSG_CREATE_GENESIS_ROLES',
        Description: 'MSG_CREATE_GENESIS_ROLES_FAIL',
        Popup: true,
        Type: NotifyType.Error
      }
    }
  }, (roles: Array<Role>, error: boolean) => {
    if (error) {
      void router.push({ path: '/signin' })
      return
    }
    if (apps.value.length > 0 && roles.length > 0 && users.value.length > 0) {
      void router.push({ path: '/signin' })
    }
  })
}

const password = ref('')
const emailAddress = ref('')

const updatePassword = (): string => {
  let password = ''
  const chars = 'a-z,A-Z,0-9,#'
  const charactersArray = chars.split(',')
  let CharacterSet = ''
  if (charactersArray.indexOf('a-z') >= 0) {
    CharacterSet += 'abcdefghijklmnopqrstuvwxyz'
  }
  if (charactersArray.indexOf('A-Z') >= 0) {
    CharacterSet += 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
  }
  if (charactersArray.indexOf('0-9') >= 0) {
    CharacterSet += '0123456789'
  }
  if (charactersArray.indexOf('#') >= 0) {
    CharacterSet += '![]{}()%&$#@'
  }
  for (let i = 0; i < 16; i++) {
    password += CharacterSet.charAt(Math.floor(Math.random() * CharacterSet.length))
  }
  return password
}

const onRefreshPassword = () => {
  password.value = updatePassword()
}

interface MyApp {
  label: string
  value: App
}

const myApps = computed(() => Array.from(apps.value.filter((el) => {
  return users.value.findIndex((uel) => uel.AppID === el.ID) < 0
})).map((app) => {
  return {
    label: app.Name,
    value: app
  } as MyApp
}))

const selectedAppID = ref(AppID)
const selectedApp = computed({
  get: () => {
    const app = apps.value.find((el) => el.ID === selectedAppID.value)
    if (!app) {
      return undefined as unknown as MyApp
    }
    return {
      label: app?.Name,
      value: app
    } as MyApp
  },
  set: (val: MyApp) => {
    selectedAppID.value = val.value.ID
  }
})

const onCreateGenesisUser = () => {
  if (password.value.length === 0 || emailAddress.value.length === 0) {
    return
  }

  genesisadmin.createGenesisUser({
    TargetAppID: selectedAppID.value,
    EmailAddress: emailAddress.value,
    PasswordHash: encryptPassword(password.value),
    Message: {
      Error: {
        Title: 'MSG_CREATE_GENESIS_USER',
        Description: 'MSG_CREATE_GENESIS_USER_FAIL',
        Popup: true,
        Type: NotifyType.Error
      }
    }
  }, (user: User, error: boolean) => {
    if (error) {
      void router.push({ path: '/signin' })
      return
    }

    if (genesisUserCreated.value) {
      void router.push({ path: '/signin' })
    }
  })
}

</script>

<style lang='sass' scoped>
</style>
