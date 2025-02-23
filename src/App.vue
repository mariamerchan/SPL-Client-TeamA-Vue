<template>
  <v-app class="app-container">
    <div class="app-background">
      <v-app-bar app color="#165c66" height="110" dark>
        <div class="d-flex justify-space-between align-center header-container">
          <div class="d-flex align-center">
            <img alt="Lihtsus Logo" src="./assets/isotipo-lihtsus.png" width="50" />
            <h2 class="ml-4">Ofrecer App</h2>
          </div>
          <div class="btn-container-app">
            <v-btn class="btns" text to="/" exact>
              Ofrecimientos
            </v-btn>

            <v-btn class="btns" text to="/crud" exact>
              CRUD
            </v-btn>
          </div>
        </div>
      </v-app-bar>

      <v-main>
        <router-view />
      </v-main>
    </div>
  </v-app>
</template>


<script>

export default {
  name: 'App',

  data: () => ({
    //
  }),
};
</script>

<style lang="scss" scoped>
/* Media query para dispositivos móviles */
@media only screen and (max-width: 600px) {

  .header-container {
    margin: 1px;

    img {
      width: 80px;
    }
  }

  .btn-container-app {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
    justify-content: center;
    gap: 3px;

    .btns {
      width: 150px;
      border: 1px solid #FFF;
    }
  }
}

.app-background {
  background: rgba(255, 255, 255, 0.5);
  background-size: cover;
  height: 100vh;
}

.header-container {
  margin: 3px 6px;
  width: 100%;
}

.btn-container {
  display: flex;
}

.btn-container-app {
  display: flex;
  gap: 10px;
}

.btns {
  border: 1px solid #FFF;
}
</style>
