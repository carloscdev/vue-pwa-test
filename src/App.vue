<template>
  <div>
    <h2>Status: </h2>
    <p v-if="isOnline">Conectado a Internet</p>
    <p v-else>No hay conexión a Internet</p>
  </div>
  <div>
    <h2>Ubicación: </h2>
    <button @click="obtenerUbicacion">Obtener Ubicación</button>
    <p v-if="isLoading">Calculando ubicación...</p>
    <p v-if="!isLoading">Latitud: {{ ubicacion.latitude }}, Longitud: {{ ubicacion.longitude }}</p>
  </div>
  <hr>
  <router-view/>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      isOnline: navigator.onLine,
      isLoading: true,
      ubicacion: {
        latitude: 0,
        longitude: 0,
      },
    }
  },
  created() {
    this.obtenerUbicacion();
  },
  mounted() {

    window.addEventListener('online', this.updateConnectionStatus);
    window.addEventListener('offline', this.updateConnectionStatus);
  },
  methods: {
    async obtenerUbicacion() {
      console.log("Obteniendo ubicación ....")
      this.isLoading = true;
      await navigator.geolocation.getCurrentPosition(
        (position) => {
          console.log("Ubicación obtenida:", position);
          this.ubicacion = position.coords;
        },
        (error) => {
          console.error("Error al obtener la ubicación:", error);
        }
      );
      this.isLoading = false;
    },
  }
}
</script>
