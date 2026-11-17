<template>
  <div class="verify-container">
    <h2>Verifica tu correo</h2>
    <p>Enviamos un código a {{ email }}</p>
    <input
      v-model="code"
      type="text"
      inputmode="numeric"
      autocomplete="one-time-code"
      placeholder="Código de 6 dígitos"
      maxlength="6"
      class="code-input"
    />
    <button @click="verify" class="btn-verify" :disabled="isVerifying">
      {{ isVerifying ? 'Verificando...' : 'Verificar' }}
    </button>
    <button @click="resend" class="btn-resend" :disabled="isResending">
      {{ isResending ? 'Reenviando...' : 'Reenviar código' }}
    </button>
    <p v-if="message" class="message">{{ message }}</p>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { insforge } from '../lib/insforgeClient'

const route = useRoute()
const router = useRouter()
const email = route.query.email
const code = ref('')
const message = ref('')
const isVerifying = ref(false)
const isResending = ref(false)

const verify = async () => {
  if (isVerifying.value) return

  isVerifying.value = true
  message.value = ''

  try {
    const { error } = await insforge.auth.verifyEmail({
      email,
      otp: code.value.trim(),
    })

    if (error) {
      message.value = `No se pudo verificar el código. Comprueba que sea correcto y que no haya expirado. ${error.message}`
      return
    }

    await router.push('/home')
  } catch (error) {
    message.value = `Error al verificar el correo: ${error.message}`
  } finally {
    isVerifying.value = false
  }
}

const resend = async () => {
  if (isResending.value) return

  isResending.value = true
  message.value = ''

  try {
    const { error } = await insforge.auth.resendVerificationEmail({ email })
    message.value = error
      ? `No se pudo reenviar el código: ${error.message}`
      : 'Nuevo código enviado. Usa el código más reciente.'
    if (!error) code.value = ''
  } catch (error) {
    message.value = `Error al reenviar el código: ${error.message}`
  } finally {
    isResending.value = false
  }
}
</script>

<style scoped>
.verify-container {
  max-width: 400px;
  margin: 60px auto;
  text-align: center;
  padding: 20px;
}
.code-input {
  width: 100%;
  padding: 10px;
  font-size: 18px;
  text-align: center;
  letter-spacing: 4px;
  margin: 16px 0;
  border: 1px solid #ccc;
  border-radius: 6px;
}
.btn-verify {
  width: 100%;
  padding: 10px;
  background: #4f46e5;
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  margin-bottom: 8px;
}
.btn-resend {
  width: 100%;
  padding: 10px;
  background: transparent;
  color: #4f46e5;
  border: 1px solid #4f46e5;
  border-radius: 6px;
  cursor: pointer;
}
.message {
  margin-top: 12px;
  color: #333;
}
</style>