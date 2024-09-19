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

const email = ref('');
const password = ref('');
const emailError = ref(false);
const passwordError = ref(false);
const errorMessage = ref(null);
const loading = ref(false);
const router = useRouter();

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

    // Verifica o conteúdo da resposta
    console.log('Resposta da API:', response);

    if (response.data?.token) {
      // Armazena o token no localStorage
      localStorage.setItem('auth_token', response.data.token);

      // Redireciona para o dashboard
      console.log('Redirecionando para o dashboard...');
      router.push({ name: 'dashboard' });
    } else {
      throw new Error('Token não encontrado na resposta.');
    }

  } catch (error) {
    console.error('Erro no processo de login:', error);

    if (error.response) {
      errorMessage.value = error.response.data?.error || 'Erro ao fazer login (problema de servidor).';
    } else if (error.request) {
      errorMessage.value = 'Erro ao fazer login (sem resposta do servidor).';
    } else {
      errorMessage.value = 'Erro ao fazer login (erro desconhecido).';
    }
  } finally {
    loading.value = false;
  }
};
</script>
