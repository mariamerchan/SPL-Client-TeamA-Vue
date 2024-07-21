<template>
  <div class="my-8">
    <h2>Ofrecer 🤝 SPL</h2>
    <p>Desde lo que somos, nuestras profesiones, nuestros roles, gustos y
      pasiones ¿Qué
      nos gustaría ofrecer al equipo?</p>
    <!-- Esto es un comentario en HTML -->
    <!-- La línea de abajo es el título que tiene el nombre del equipo -->
    <h2>Team A</h2>
    <v-row class="row-container mt-9">
      <v-col v-for="ofrecimiento in ofrecimientos" :key="ofrecimiento.id" cols="12" sm="6" md="4" lg="3">
        <v-card shaped class="mb-4">
          <v-card-title class="headline">
            <v-icon class="mr-2">mdi-account</v-icon>
            {{ ofrecimiento.nombre }}
          </v-card-title>
          <v-card-text>
            <v-icon class="mr-2">mdi-text-account</v-icon>
            {{ ofrecimiento.descripcion }}
          </v-card-text>
          <v-card-text>
            <v-icon class="mr-2">mdi-linkedin</v-icon>
            <a :href="ofrecimiento.socialUrl" target="_blank" rel="noopener noreferrer">{{ ofrecimiento.socialUrl }}</a>
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
      ofrecimientos: [],
    };
  },

  created() {
    this.obtenerOfrecimientos();
  },

  methods: {
    // Solicitud HTTP GET para obtener los ofrecimientos desde el servidor
    obtenerOfrecimientos() {
      fetch(`${process.env.VUE_APP_SERVER_URL}api/obtener-ofrecimientos`)
        .then(response => response.json())
        .then(data => {
          this.ofrecimientos = data;
        })
        .catch(error => {
          console.error(error);
        });
    },
  },
};
</script>

<style lang="scss" scoped>
@media only screen and (max-width: 600px) {

  h2 {
    font-size: 25px !important;
    padding: 0 20px !important;
  }

}

h2,
h3 {
  text-align: center;
  font-weight: bold;
  color: #051330;
}

h2 {
  font-size: 30px;
}

h3 {
  font-size: 25px;
}

p {
  text-align: center;
  font-weight: 500;
  width: 30%;
  margin: 0 auto;
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
