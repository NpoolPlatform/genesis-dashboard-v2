<template>
  <div>
    <q-input
      v-model='account'
      :label='$t("MSG_ACCOUNT")'
    >
      <template #prepend>
        <q-icon name='account_circle' />
      </template>
    </q-input>
    <q-input
      v-model='password'
      :label='$t("MSG_PASSWORD")'
    >
      <template #prepend>
        <q-icon name='lock' />
      </template>
    </q-input>
    <div class='row top'>
      <q-btn
        no-caps
        :label='$t("MSG_SIGNIN")'
        @click='onLoginClick'
        class='btn start round'
      />
      <q-btn
        no-caps
        :label='$t("MSG_CONTACT_ADMIN")'
        class='btn alt round'
      />
    </div>
  </div>
</template>

<script setup lang='ts'>
import { ref } from 'vue'
import { user, notifverify, utils, notify, appuserbase, constant } from 'src/npoolstore'
import { useRouter } from 'vue-router'
import { useReCaptcha } from 'vue-recaptcha-v3'

const account = ref('')
const password = ref('')

const _user = user.useUserStore()
const verify = notifverify.useVerifyStore()
const recaptcha = useReCaptcha()

const router = useRouter()

const signin = (token: string) => {
  _user.login({
    Account: account.value,
    PasswordHash: utils.encryptPassword(password.value),
    AccountType: appuserbase.SignMethodType.Email,
    ManMachineSpec: token,
    EnvironmentSpec: 'NOT USED',
    Message: {
      Error: {
        Title: 'MSG_SINGIN',
        Message: 'MSG_SIGNIN_FAIL',
        Popup: true,
        Type: notify.NotifyType.Error
      }
    }
  }, () => {
    void router.push({ path: '/' })
  })
}

const getRecaptcha = () => {
  verify.getGoogleToken({
    Recaptcha: recaptcha,
    Req: constant.GoogleTokenType.Login,
    Message: {
      Error: {
        Title: 'MSG_GET_GOOGLE_TOKEN',
        Message: 'MSG_GET_GOOGLE_TOKEN_FAIL',
        Popup: true,
        Type: notify.NotifyType.Error
      }
    }
  }, (token: string) => {
    signin(token)
  })
}

const onLoginClick = () => {
  getRecaptcha()
}

</script>

<style lang='sass' scoped>
</style>
