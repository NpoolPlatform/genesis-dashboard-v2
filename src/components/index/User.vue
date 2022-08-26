<template>
  <q-table
    dense
    flat
    :columns='columns'
    :title='$t("MSG_USERS")'
    :rows='users'
    row-key='ID'
  >
    <template #top-right>
      <q-select
        v-if='myApps.length > 0'
        dense
        :options='myApps'
        v-model='selectedApp'
        :label='$t("MSG_APP")'
      />
      <q-input
        v-if='myApps.length > 0'
        dense
        class='small'
        v-model='password'
        :label='$t("MSG_PASSWORD")'
        disable
      />
      <q-btn
        v-if='myApps.length > 0'
        flat
        dense
        icon='published_with_changes'
        @click='onRefreshPassword'
      />
      <q-input
        v-if='myApps.length > 0'
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
import { computed, ref, watch } from 'vue'
import {
  App,
  encryptPassword,
  formatTime,
  NotifyType,
  useGenesisAdminStore,
  User
} from 'npool-cli-v4'
import { useI18n } from 'vue-i18n'

// eslint-disable-next-line @typescript-eslint/unbound-method
const { t } = useI18n({ useScope: 'global' })

const genesisadmin = useGenesisAdminStore()
const apps = computed(() => genesisadmin.Apps)
const users = computed(() => genesisadmin.Users)

const columns = computed(() => [
  {
    name: 'AppID',
    label: t('MSG_APP_ID'),
    field: (row: User) => row.AppID
  },
  {
    name: 'UserID',
    label: t('MSG_USER_ID'),
    field: (row: User) => row.ID
  },
  {
    name: 'EmailAddress',
    label: t('MSG_EMAIL_ADDRESS'),
    field: (row: User) => row.EmailAddress
  },
  {
    name: 'PhoneNO',
    label: t('MSG_PHONE_NO'),
    field: (row: User) => row.PhoneNO
  },
  {
    name: 'Roles',
    label: t('MSG_ROLES'),
    field: (row: User) => row.Roles.join(',')
  },
  {
    name: 'CreatedAt',
    label: t('MSG_CREATEDAT'),
    field: (row: User) => formatTime(row.CreatedAt)
  }
])

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

const selectedAppID = ref('')
const selectedApp = ref(undefined as unknown as MyApp)
watch(selectedApp, () => {
  selectedAppID.value = selectedApp.value?.value?.ID
})

const onCreateGenesisUser = () => {
  if (password.value.length === 0 || emailAddress.value.length === 0) {
    return
  }

  if (!selectedAppID.value.length) {
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
  }, () => {
    // TODO
  })
}

</script>

<style lang='sass' scoped>
</style>
