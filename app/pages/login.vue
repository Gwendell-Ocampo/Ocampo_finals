<template>
  <div class="mx-auto d-flex align-center justify-center" style="height: 100vh;">
      <!-- VCARD -->
    <v-card width="500" class="py-8 px-6" rounded="xl" elevation="8">

      <v-card-text class="text-center">
        <!-- Icon -->
        <v-icon size="100" 
        color="primary" class="mb-4">
          mdi-account-circle</v-icon>
        <h3>Welcome Back, Please Login</h3>
        
        <!-- Form -->
        <v-form @submit.prevent="handleLogin">
          <v-text-field
            rounded
            v-model="username"
            label="Username"
            variant="outlined"
            prepend-inner-icon="mdi-account"
            required
          ></v-text-field>

          <v-text-field
            rounded
            v-model="password"
            label="Password"
            type="password"
            variant="outlined"
            prepend-inner-icon="mdi-lock"
            required
          ></v-text-field>

          <v-btn
            type="submit"
            color="primary"
            rounded
            block
            size="large"
            class="mt-4">Login</v-btn>
            
            <v-divider class="my-8">OR</v-divider>
            
          <v-btn
            v-if="!googleUser"
            type="button"
            color="green"
            rounded
            block
            size="large"
            class="mt-4"
            :loading="isSigningIn"
            :disabled="isSigningIn"
            @click="handleGoogleLogin"
            >Sign in With Google</v-btn>

          <v-btn
            v-if="googleUser"
            type="button"
            color="error"
            rounded
            block
            size="large"
            class="mt-4"
            @click="handleGoogleLogout"
            >Log out</v-btn>

          <v-alert v-if="googleError" type="error" class="mt-4">
            {{ googleError }}
          </v-alert>

          <v-alert v-if="googleUser" type="success" class="mt-4">
            Signed in as {{ googleUser.name }} ({{ googleUser.email }})
          </v-alert>
            
          

        </v-form>
      </v-card-text>
    </v-card>
  </div>
</template>

<script setup lang="ts">
// @ts-nocheck

const config = useRuntimeConfig()
const username = ref('')
const password = ref('')
const googleError = ref('')
const isSigningIn = ref(false)
const googleUser = ref<{ name: string; email: string } | null>(null)
const googleAccessToken = ref('')

useHead({
  script: [{ src: 'https://accounts.google.com/gsi/client', async: true, defer: true }],
})

const handleLogin = async () => {
  googleError.value = ''

  if (!username.value || !password.value) {
    googleError.value = 'Please enter your username and password.'
    return
  }

  localStorage.setItem('google_user', JSON.stringify({ name: username.value, email: username.value }))
  window.location.replace('/data-grid')
}

const waitForGoogle = () => new Promise((resolve, reject) => {
  const startedAt = Date.now()
  const check = () => {
    if (window.google?.accounts?.oauth2) return resolve(window.google)
    if (Date.now() - startedAt > 10000) return reject(new Error('Google sign-in could not be loaded.'))
    window.setTimeout(check, 100)
  }
  check()
})

const handleGoogleLogin = async () => {
  googleError.value = ''

  if (!config.public.googleClientId) {
    googleError.value = 'Google sign-in is not configured yet.'
    return
  }

  try {
    isSigningIn.value = true
    await waitForGoogle()
    window.google.accounts.oauth2.initTokenClient({
      client_id: config.public.googleClientId,
      scope: 'openid email profile',
      callback: async (response) => {
        if (response.error || !response.access_token) {
          googleError.value = 'Google sign-in could not be completed.'
          isSigningIn.value = false
          return
        }

        googleAccessToken.value = response.access_token

        let profile: { name: string; email: string }
        try {
          profile = await $fetch<{ name: string; email: string }>(
            'https://www.googleapis.com/oauth2/v3/userinfo',
            { headers: { Authorization: `Bearer ${response.access_token}` } },
          )
        } catch {
          googleError.value = 'Google sign-in could not load your profile.'
          return
        } finally {
          isSigningIn.value = false
        }

        googleUser.value = profile
        localStorage.setItem('google_user', JSON.stringify(profile))
        window.location.replace('/data-grid')
      },
      error_callback: () => {
        googleError.value = 'Google sign-in was cancelled or the popup could not be opened.'
        isSigningIn.value = false
      },
    }).requestAccessToken({ prompt: 'select_account' })
  } catch (error) {
    googleError.value = error.message || 'Google sign-in could not be completed.'
    isSigningIn.value = false
  }
}

const handleGoogleLogout = async () => {
  if (googleAccessToken.value) {
    try {
      await $fetch('https://oauth2.googleapis.com/revoke', {
        method: 'POST',
        body: new URLSearchParams({ token: googleAccessToken.value }),
      })
    } catch {
      googleError.value = 'Failed to revoke Google access token.'
    }
  }

  googleAccessToken.value = ''
  localStorage.removeItem('google_user')
  googleUser.value = null
  googleError.value = ''
}
</script>

<style scoped>
</style>