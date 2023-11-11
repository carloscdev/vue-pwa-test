<template>
  <div>
    <p v-if="isOnline">Conectado a Internet</p>
    <p v-else>No hay conexión a Internet</p>
  </div>
  <router-view/>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      isOnline: navigator.onLine
    }
  },
  mounted() {
    window.addEventListener('online', this.updateConnectionStatus);
    window.addEventListener('offline', this.updateConnectionStatus);
  },
}
</script>
