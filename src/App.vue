<template>
  <div>
    <h2>Status: </h2>
    <p v-if="isOnline">Conectado a Internet</p>
    <p v-else>No hay conexión a Internet</p>
  </div>
  <div>
    <h2>Ubicación: </h2>
    <button @click="obtenerUbicacion">Obtener Ubicación</button>
    <small>
      {{ status }}
    </small>
    <p v-if="isLoading">Cargando ...</p>
    <p v-if="!isLoading">Latitud: {{ ubicacion.latitude }}, Longitud: {{ ubicacion.longitude }}</p>
  </div>
  <hr>
  <h1>
    Obtener una foto
  </h1>
  <video width="200" ref="videoElement" autoplay></video>
  <br><br><br>
  <button @click="activarCamara">Activar Cámara</button>
  <br><br><br>
  <button @click="cambiarCamara">Cambiar Cámara</button>
  <br><br><br>
  <button @click="tomarFoto">Tomar Foto</button>
  <br />
  <p>Tipo de camara: {{ facingMode }}</p>

  <div v-if="imagenDataURL">
    <p>Imagen:</p>
    <img :src="imagenDataURL" alt="Imagen tomada">
  </div>
  <hr>
  <router-view/>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      videoElement: null,
      imagenDataURL: null,
      facingMode: 'environment',
      isOnline: navigator.onLine,
      isLoading: true,
      ubicacion: {
        latitude: 0,
        longitude: 0,
      },
      status: '',
    }
  },
  created() {
    this.obtenerUbicacion();
  },
  mounted() {
    this.videoElement = this.$refs.videoElement;
    window.addEventListener('online', this.updateConnectionStatus);
    window.addEventListener('offline', this.updateConnectionStatus);
  },
  methods: {
    obtenerUbicacion() {
        this.status = "Obteniendo ubicación...."
        this.isLoading = true;
        navigator.geolocation.getCurrentPosition(
          (position) => {
            console.log("Ubicación obtenida:", position);
            this.ubicacion = position.coords;
            this.status = "Ubicación obtenida";
            this.isLoading = false;
          },
          (error) => {
            console.error("Error al obtener la ubicación:", error);
            this.status = "Error al obtener la ubicación";
            this.isLoading = false;
          }
        )
      },
    async activarCamara() {
      try {
        const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: this.facingMode } });
        this.videoElement.srcObject = stream;
      } catch (error) {
        console.error('Error al activar la cámara:', error);
      }
    },
    cambiarCamara() {
      // Cambiar entre cámara frontal y trasera
      this.facingMode = this.facingMode === 'user' ? 'environment' : 'user';

      // Si la cámara está activada, recargar el stream con la nueva configuración
      if (this.videoElement.srcObject) {
        this.activarCamara();
      }
    },
    tomarFoto() {
      const canvas = document.createElement('canvas');
      const context = canvas.getContext('2d');
      const video = this.videoElement;

      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;

      context.drawImage(video, 0, 0, canvas.width, canvas.height);

      this.imagenDataURL = canvas.toDataURL('image/png');
    },
  },
}
</script>
