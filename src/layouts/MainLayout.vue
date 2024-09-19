<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>

        <q-toolbar-title>
          RealSync Button
        </q-toolbar-title>

        <q-btn
          v-if="isAuthenticated"
          @click="logout"
          flat
          round
          dense
          icon="logout"
        />
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script setup>
import { computed } from 'vue'
import { useRouter } from 'vue-router';

defineOptions({
  name: 'MainLayout',
});

const router = useRouter();

const isAuthenticated = computed(() => !!localStorage.getItem('auth_token'));

const logout = () => {
  // Remove o token do localStorage ao fazer logout
  localStorage.removeItem('auth_token');

  // Redireciona o usuário para a página de login
  router.push({ name: 'login' });
};

</script>
