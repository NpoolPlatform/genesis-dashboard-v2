<template>
  <q-table
    dense
    flat
    :title='$t("MSG_APPS")'
    :rows='apps'
    :columns='(columns as never)'
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
  App,
  NotifyType,
  formatTime,
  useGenesisAdminStore
} from 'npool-cli-v4'
import { useI18n } from 'vue-i18n'

// eslint-disable-next-line @typescript-eslint/unbound-method
const { t } = useI18n({ useScope: 'global' })

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

const columns = computed(() => [
  {
    name: 'AppID',
    label: t('MSG_ID'),
    align: 'left',
    field: (row: App) => row.ID
  },
  {
    name: 'Name',
    label: t('MSG_NAME'),
    field: (row: App) => row.Name
  },
  {
    name: 'Logo',
    label: t('MSG_LOGO'),
    field: (row: App) => row.Logo
  },
  {
    name: 'Signup Method',
    label: t('MSG_SIGNUP_METHOD'),
    field: (row: App) => row.SignupMethods.join(',')
  },
  {
    name: 'Ext Signin Method',
    label: t('MSG_EXT_SIGNIN_METHOD'),
    field: (row: App) => row.ExtSigninMethods.join(',')
  },
  {
    name: 'Invitation Code Must',
    label: t('MSG_INVITATION_CODE_MUST'),
    field: (row: App) => row.InvitationCodeMust
  },
  {
    name: 'Kyc Enable',
    label: t('MSG_KYC_ENABLE'),
    field: (row: App) => row.KycEnable
  },
  {
    name: 'Signin Verify Enable',
    label: t('MSG_SIGNIN_VERIFY_ENABLE'),
    field: (row: App) => row.SigninVerifyEnable
  },
  {
    name: 'Recaptcha Method',
    label: t('MSG_RECAPTCHA_METHOD'),
    field: (row: App) => row.RecaptchaMethod
  },
  {
    name: 'CreatedAt',
    label: t('MSG_CREATEDAT'),
    field: (row: App) => formatTime(row.CreatedAt)
  }
])

</script>

<style lang='sass' scoped>
</style>
