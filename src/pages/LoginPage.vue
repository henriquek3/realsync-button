<template>
  <q-layout>
    <q-page class="q-pa-md flex flex-center">
      <q-card
        class="q-pa-md"
        style="width: 400px"
      >
        <q-card-section>
          <div class="text-h6">Login</div>
        </q-card-section>

        <q-card-section>
          <q-input
            v-model="email"
            label="Email"
            type="email"
            outlined
            :error="emailError"
          />
          <q-input
            v-model="password"
            label="Senha"
            type="password"
            outlined
            :error="passwordError"
            class="q-mt-md"
          />
        </q-card-section>

        <q-card-actions align="right">
          <q-btn
            label="Login"
            color="primary"
            @click="login"
            :loading="loading"
          />
        </q-card-actions>

        <q-card-section
          v-if="errorMessage"
          class="text-negative"
        >
          {{ errorMessage }}
        </q-card-section>
      </q-card>
    </q-page>
  </q-layout>
</template>

<script setup>
import { ref } from 'vue';
import { api } from 'boot/axios';
import { useRouter } from 'vue-router';

const router = useRouter();


// Variáveis de estado
const email = ref('');
const password = ref('');
const loading = ref(false);
const errorMessage = ref(null);
const emailError = ref(false);
const passwordError = ref(false);

// Função de login
const login = async () => {
  if (!email.value || !password.value) {
    emailError.value = !email.value;
    passwordError.value = !password.value;
    errorMessage.value = 'Preencha os campos obrigatórios.';
    return;
  }

  loading.value = true;
  errorMessage.value = null;

  try {
    const response = await api.post('/api/login', {
      email: email.value,
      password: password.value,
    });

    // Armazena o token no localStorage
    localStorage.setItem('auth_token', response.data.token);

    // Redireciona para o dashboard
    router.push({ name: 'dashboard' });
  } catch (error) {
    errorMessage.value = error.response?.data?.error || 'Erro ao fazer login.';
  } finally {
    loading.value = false;
  }
};
</script>
