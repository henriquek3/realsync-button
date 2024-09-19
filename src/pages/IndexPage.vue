<template>
  <q-page class="flex flex-center">
    <q-btn
      :color="isOn ? 'positive' : 'negative'"
      :label="isOn ? 'Ligado' : 'Desligado'"
      @click="toggle"
      :icon="isOn ? 'alarm_on' : 'alarm_off'"
      class="full-width full-height"
    />
  </q-page>
</template>

<script setup>
defineOptions({
  name: 'IndexPage',
});

import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router'

const router = useRouter()

const logout = () => {
  // Remove o token do localStorage ao fazer logout
  localStorage.removeItem('auth_token')

  // Redireciona o usuário para a página de login
  router.push({ name: 'login' })
}

// Simulação de dados
const button_id = 1;
const production_operator_id = 123; // ID do operador (simulado)

// Variáveis reativas
const isOn = ref(false);
const buttonHistories = ref([]); // Histórico de eventos

// Função para obter o horário formatado
const getFormattedTimestamp = () => {
  const now = new Date();
  return now.toISOString(); // Salva o timestamp no formato ISO
};

// Função para adicionar um registro no histórico
const addLog = (event) => {
  const logEntry = {
    button_id: button_id,
    event: event,
    production_operator_id: production_operator_id,
    created_at: getFormattedTimestamp(),
  };
  buttonHistories.value.push(logEntry);
  localStorage.setItem('button-histories', JSON.stringify(buttonHistories.value));
};

// Carrega o estado e o histórico do localStorage quando o componente é montado
onMounted(() => {
  const savedStatus = localStorage.getItem('button-status');
  const savedHistories = localStorage.getItem('button-histories');

  if (savedStatus !== null) {
    isOn.value = JSON.parse(savedStatus);
  }
  if (savedHistories !== null) {
    buttonHistories.value = JSON.parse(savedHistories);
  }
});

// Alterna o estado e adiciona o evento no log
const toggle = () => {
  isOn.value = !isOn.value;

  if (isOn.value) {
    addLog('Ligado');
  } else {
    addLog('Desligado');
  }

  // Salva o estado atual no localStorage
  localStorage.setItem('button-status', JSON.stringify(isOn.value));
};
</script>


<style scoped>
.q-page {
  height: 100vh;
}

.full-width {
  width: 100%;
}

.full-height {
  height: 100%;
}
</style>
