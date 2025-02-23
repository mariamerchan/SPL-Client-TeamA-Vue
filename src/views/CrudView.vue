<template>
  <v-container>
    <h1>CRUD Ofrecimientos</h1>
    <div class="btn-container">
      <v-btn rounded color="primary" class="text-capitalize" @click="openCrearModal">
        <v-icon small color="white" class="mr-2">mdi-plus</v-icon>
        Crear Ofrecimiento
      </v-btn>
    </div>

    <v-alert v-if="mensaje" :type="alertColor" dismissible @input="mensaje = ''">
      {{ mensaje }}
    </v-alert>

    <v-data-table :headers="headers" :items="ofrecimientos" :items-per-page="5" class="elevation-1">
      <!-- eslint-disable-next-line vue/valid-v-slot -->
      <template v-slot:item.actions="{ item }">
        <div class="btn-actions-container">
          <v-btn rounded small class="text-capitalize text-success" color="#eafff6" @click="openActualizarModal(item)">
            <v-icon small color="#65ac84" class="mr-2">mdi-pencil</v-icon>
            Actualizar
          </v-btn>
          <v-btn rounded small class="text-capitalize text-warning" color="#fce7e6"
            @click="eliminarOfrecimientos(item.id)">
            <v-icon small color="#d45253">mdi-delete</v-icon>
            Eliminar
          </v-btn>
        </div>
      </template>
    </v-data-table>

    <!-- Modal para agregar ofrecimiento -->
    <v-dialog v-model="crearModal" max-width="500px">
      <v-card>
        <v-card-title class="d-flex justify-space-between align-center">
          <span class="headline">Crear</span>
          <v-btn icon @click="cerrarCrearModal">
            <v-icon>mdi-close</v-icon>
          </v-btn>
        </v-card-title>
        <v-card-text>
          <v-form ref="crearForm" v-model="valid" @submit.prevent="crearOfrecimiento">
            <v-text-field v-model="nuevoNombre" label="Nombre" :rules="[rules.required]"></v-text-field>
            <v-textarea v-model="nuevaDescripcion" label="Ofrecimiento" :rules="[rules.required]"></v-textarea>
            <v-text-field v-model="nuevaURL" label="URL" :rules="[rules.required]"></v-text-field>
            <v-btn :disabled="!valid" rounded color="primary" type="submit" class="text-capitalize">
              <v-icon small color="white">mdi-plus</v-icon>
              Crear</v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-dialog>

    <!-- Modal para actualizar ofrecimiento -->
    <v-dialog v-model="actualizarModal" max-width="500px">
      <v-card>
        <v-card-title class="d-flex justify-space-between align-center">
          <span class="headline">Actualizar Ofrecimiento</span>
          <v-btn icon @click="cerrarActualizarModal">
            <v-icon>mdi-close</v-icon>
          </v-btn>
        </v-card-title>
        <v-card-text>
          <v-form @submit.prevent="actualizarOfrecimiento">
            <v-text-field v-model="OfrecimientoActual.nombre" label="Nombre"></v-text-field>
            <v-textarea v-model="OfrecimientoActual.descripcion" label="Ofrecimiento"></v-textarea>
            <v-text-field v-model="OfrecimientoActual.socialUrl" label="URL"></v-text-field>
            <v-btn rounded color="primary" type="submit" class="text-capitalize">
              <v-icon small class="mr-2">mdi-pencil</v-icon>
              Actualizar
            </v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>

export default {
  data() {
    return {
      valid: false,
      ofrecimientos: [],
      headers: [
        { text: 'ID', value: 'id' },
        { text: 'Autor', value: 'nombre' },
        { text: 'Ofrecimiento', value: 'descripcion' },
        { text: 'Red Social', value: 'socialUrl' },
        { text: 'Acciones', value: 'actions', sortable: false }
      ],
      nuevoNombre: '',
      nuevaDescripcion: '',
      nuevaURL: '',
      OfrecimientoActual: {},
      mensaje: '',
      type: '',
      crearModal: false,
      actualizarModal: false,
      rules: {
        required: value => !!value || 'Este campo es requerido.',
        url: value => {
          const pattern = /^(https?|ftp):\/\/[^\s/$.?#].[^\s]*$/i
          return pattern.test(value) || 'URL no válida.'
        }
      }
    };
  },
  created() {
    // Realizar solicitud HTTP para obtener los ofrecimientos
    this.obtenerOfrecimientos();
  },
  methods: {
    // Solicitud HTTP GET para obtener los ofrecimientos
    async obtenerOfrecimientos() {
      try {
        const response = await fetch(`${process.env.VUE_APP_SERVER_URL}api/obtener-ofrecimientos`);
        const ofrecimientos = await response.json();
        this.ofrecimientos = ofrecimientos;
      } catch (error) {
        console.error(error);
      }
    },
    // Solicitud HTTP POST para crear un ofrecimiento
    async crearOfrecimiento() {
      try {
        const ofrecimiento = {
          nombre: this.nuevoNombre,
          descripcion: this.nuevaDescripcion,
          socialUrl: this.nuevaURL
        };

        const response = await fetch(`${process.env.VUE_APP_SERVER_URL}api/crear-ofrecimiento`, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(ofrecimiento)
        });

        if (response.ok) {
          this.nuevoNombre = '';
          this.nuevaDescripcion = '';
          this.nuevaURL = '';
          this.mensaje = 'Ofrecimiento agregado correctamente';
          this.type = 'Success'
          this.obtenerOfrecimientos();
          this.cerrarCrearModal();
        } else {
          console.error('Error al agregar el ofrecimiento');
        }
      } catch (error) {
        console.error(error);
      }
    },
    // Solicitud HTTP DELETE para eliminar un ofrecimiento
    async eliminarOfrecimientos(id) {
      try {
        const response = await fetch(`${process.env.VUE_APP_SERVER_URL}api/eliminar-ofrecimiento/${id}`, {
          method: 'DELETE'
        });

        if (response.ok) {
          this.mensaje = 'Ofrecimiento eliminado correctamente';
          this.type = 'Success'
          this.obtenerOfrecimientos();
        } else {
          console.error('Error al eliminar el ofrecimiento');
        }
      } catch (error) {
        console.error(error);
      }
    },
    // Solicitud HTTP PUT para actualizar un ofrecimiento
    async actualizarOfrecimiento() {
      try {
        const ofrecimiento = {
          id: this.OfrecimientoActual.id,
          nombre: this.OfrecimientoActual.nombre,
          descripcion: this.OfrecimientoActual.descripcion,
          socialUrl: this.OfrecimientoActual.socialUrl
        };

        const response = await fetch(`${process.env.VUE_APP_SERVER_URL}api/actualizar-ofrecimiento/${ofrecimiento.id}`, {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(ofrecimiento)
        });

        if (response.ok) {
          this.mensaje = 'Ofrecimiento actualizado correctamente';
          this.type = 'Success'
          this.obtenerOfrecimientos();
          this.cerrarActualizarModal();
        } else {
          this.mensaje = 'Ups al parecer tienes un error. ¿Y si miras el código del Back-end?';
          this.type = 'Error'
          this.cerrarActualizarModal();
          console.error('Error al actualizar el ofrecimiento');
        }
      } catch (error) {
        console.error(error);
      }
    },
    openCrearModal() {
      this.crearModal = true;
    },
    openActualizarModal(ofrecimiento) {
      this.OfrecimientoActual = { ...ofrecimiento };
      this.actualizarModal = true;
    },
    cerrarCrearModal() {
      this.crearModal = false;
      this.nuevoNombre = '';
      this.nuevaDescripcion = '';
    },
    cerrarActualizarModal() {
      this.actualizarModal = false;
    },
  },
  watch: {
    mensaje(newValue) {
      if (newValue) {
        setTimeout(() => {
          this.mensaje = '';
        }, 5000);
      }
    }
  },
  computed: {
    alertColor() {
      if (this.type === 'Success') {
        return 'success'; // Cambia 'success' por la clase de color que deseas usar para los mensajes de éxito
      } else if (this.type === 'Error') {
        return 'error'; // Cambia 'error' por la clase de color que deseas usar para los mensajes de error
      } else {
        return 'info'; // Cambia 'info' por la clase de color que deseas usar para los mensajes informativos
      }
    }
  }
};
</script>

<style lang="scss" scoped>
@media only screen and (max-width: 600px) {

  h1 {
    margin: 20px 0;
    font-size: 25px;
    text-align: center;
  }

  .btn-container {
    display: flex;
    justify-content: center !important;
    margin: 20px;
  }

}

h1 {
  margin-top: 5%;
  font-weight: bold;
  color: #165c66;
}

.btn-container {
  display: flex;
  justify-content: flex-end;
  margin-bottom: 5%;
}

.btn-actions-container {
  display: flex;
  justify-content: center;
  gap: 10px;
}

.text-capitalize {
  text-transform: capitalize;
  font-weight: 500;
}

.text-success {
  color: #66ac87;
}

.text-warning {
  color: #db5a5c;
}

.headline {
  font-size: 20px;
  font-weight: bold;
}
</style>
