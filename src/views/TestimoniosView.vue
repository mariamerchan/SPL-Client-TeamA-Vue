<template>
  <div class="my-8">
    <h2>Testimonios Software Project Leader</h2>
    <!-- Nombre del Team -->
    <h3>Team A</h3>
    <v-row class="row-container mt-9">
      <v-col v-for="testimonio in testimonios" :key="testimonio.id" cols="12" sm="6" md="4" lg="3">
        <v-card shaped class="mb-4">
          <v-card-title class="headline">
            <v-icon class="mr-2">mdi-account</v-icon>
            {{ testimonio.nombre }}
          </v-card-title>
          <v-card-text>
            <v-icon class="mr-2">mdi-text-account</v-icon>
            {{ testimonio.descripcion }}
          </v-card-text>
          <v-card-text>
            <v-icon class="mr-2">mdi-linkedin</v-icon>
            <a :href="testimonio.socialUrl" target="_blank" rel="noopener noreferrer">{{ testimonio.socialUrl }}</a>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
export default {
  data() {
    return {
      testimonios: [],
    };
  },

  created() {
    this.obtenerTestimonios();
  },

  methods: {
    // Solicitud HTTP GET para obtener los testimonios desde el servidor
    obtenerTestimonios() {
      fetch(`${process.env.VUE_APP_SERVER_URL}api/obtener-testimonios`)
        .then(response => response.json())
        .then(data => {
          this.testimonios = data;
        })
        .catch(error => {
          console.error(error);
        });
    },
  },
};
</script>

<style scoped>
h2,
h3 {
  text-align: center;
  font-size: 35px;
  font-weight: bold;
  color: #165C66;
}

.headline {
  font-size: 18px;
  font-weight: bold;
}

.v-card {
  height: 250px;
  overflow-y: auto;
  padding: 6px;
}

.row-container {
  width: 95%;
  margin: 0 auto;
}
</style>