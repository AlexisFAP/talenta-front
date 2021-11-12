<template>
  <div>
    <v-card id="card" max-width="600px">
      <v-form ref="formularioLibro">
        <v-card-title primary-title> Ingresar Libro Nuevo </v-card-title>
        <v-card-subtitle>* Campo Obligatorio</v-card-subtitle>
        <v-card-text>
          <v-text-field
            color="white"
            v-model="codigo"
            :rules="requiredRule"
          > <template #label>
              <span class="red--text"><strong>* </strong></span>Codigo
            </template>
          </v-text-field>
          <v-text-field
            color="white"
            v-model="nombre"
            :rules="requiredRule"
          > <template #label>
              <span class="red--text"><strong>* </strong></span>Nombre
            </template>
          </v-text-field>
          <v-text-field
            color="white"
            v-model="descripcion"
            :rules="requiredRule"
          > <template #label>
              <span class="red--text"><strong>* </strong></span>Descripción
            </template>
          </v-text-field>
          <v-text-field
            color="white"
            v-model="genero"
            :rules="requiredRule"
          > <template #label>
              <span class="red--text"><strong>* </strong></span>Género
            </template>
          </v-text-field>
          <v-text-field
            color="white"
            v-model="autor"
            :rules="requiredRule"
          > <template #label>
              <span class="red--text"><strong>* </strong></span>Autor
            </template>
          </v-text-field>
        </v-card-text>
        <v-card-actions>
          <v-btn color="success" @click="ingresarLibro()">Ingresar Libro</v-btn>
        </v-card-actions>
      </v-form>
    </v-card>
  </div>
</template>
<script>
import config from "../assets/js/config";
export default {
  data() {
    return{
      codigo: null,
      nombre: null,
      descripcion: null,
      genero: null,
      autor: null,
      requiredRule: [(v) => !!v || "El campo es obligatorio"],
      passwordRule: [(v) => !!v || "El campo es obligatorio",value => value && value.length >= 8 || 'Min 8 caracteres, Max 20 caracteres'],
      show_password: false,
    };
  },
  methods: {
    async ingresarLibro() {
      try {
        if (!this.$refs.formularioLibro.validate()) {
          return;
        }
        let url = config.URL_API + "/libros";
        let payload = {};
        payload.codigo = this.codigo;
        payload.nombre = this.nombre;
        payload.estado = "Disponible"
        payload.descripcion = this.descripcion;
        payload.genero = this.genero;
        payload.autor = this.autor;
        let response = await this.$axios.post(url, payload)
        let data = response.data;
        if (data.ok == true) {
          console.log(data.info);
          this.$swal({
            type: "success",
            icon: "success",
            title: "Libro Creado",
            text: "Su libro se pudo crear exitosamente",
          })
        } else {
          this.$swal({
            type: "error",
            icon: "error",
            title: "Oops...",
            text: data.data.message,
          });
        }
        console.log(response);
      } catch (error) {
        this.$swal({
          type: "error",
          icon: "error",
          title: "Oops...",
          text: "Ha ocurrido un error. No se ha podido conectar a API.",
        });
        console.log(error);
      }
    },
  },
};
</script>
<style scoped>
#card{
  margin: 0 auto;
}
.theme--dark.v-card {
  background-color: #0077B6;
}
.theme--dark.success {
  background-color: rgb(115, 204, 219) !important;
  border-color: rgb(115, 204, 219) !important;
}
</style>