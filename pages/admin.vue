<template>
<div>
  <v-card>
    <v-card-title primary-title>
      Gestión de Libros
    </v-card-title>
    <v-data-table
        :headers="headers_libros"
        :items="libros"
        :items-per-page="5"
        class="elevation-1"
      >
        <template slot="item.actions" slot-scope="{ item }">
            <v-icon small class="mr-2" @click="modificarLibro(item)">
            mdi-pencil
          </v-icon>
          <v-icon small @click="eliminarLibro(item)"> mdi-delete </v-icon>
        </template>
      </v-data-table>
  </v-card>
  <br>
  <v-card>
    <v-card-title primary-title>
      Gestión de Usuarios
    </v-card-title>
    <v-data-table
        :headers="headers_usuarios"
        :items="usuarios"
        :items-per-page="5"
        class="elevation-1"
      >
        <template slot="item.actions" slot-scope="{ item }">
        <v-icon small class="mr-2" @click="modificarUsuario(item)">
            mdi-pencil
          </v-icon>
          <v-icon small @click="eliminarUsuario(item)"> mdi-delete </v-icon>
        </template>
      </v-data-table>
  </v-card>
  <br>
  <v-card id="forms">
      <v-form ref="formularioUsuario">
          <v-card-title primary-title>
              Modificar Usuario
          </v-card-title>
          <v-card-subtitle>
          Luego de ingresar los datos presionar el pincel para modificar el usuario
      </v-card-subtitle>
        <v-card-text>
            <v-text-field
            label="Nombre" color="white"
            v-model="nombre"
            :rules="requiredRule"
          ></v-text-field>
          <v-text-field
            label="Correo" color="white"
            v-model="correo"
            :rules="requiredRule"
          ></v-text-field>
          <v-text-field
            label="Teléfono" color="white"
            v-model="telefono"
            :rules="requiredRule"
          ></v-text-field>
        </v-card-text>
      </v-form>
      <v-form ref="formularioLibro" >
          <v-card-title primary-title>
              Modificar Libro
          </v-card-title>
          <v-card-subtitle>
          Luego de ingresar los datos presionar el pincel para modificar el libro.
      </v-card-subtitle>
        <v-card-text> 
            <v-text-field
            label="Nombre" color="white"
            v-model="nombre2"
            :rules="requiredRule"
          ></v-text-field>
          <v-text-field
            label="Descripción" color="white"
            v-model="descripcion"
            :rules="requiredRule"
          ></v-text-field>
          <v-text-field
            label="Género" color="white"
            v-model="genero"
            :rules="requiredRule"
          ></v-text-field>
          <v-text-field
            label="Autor" color="white"
            v-model="autor"
            :rules="requiredRule"
          ></v-text-field>
        </v-card-text>
      </v-form>
  </v-card>
  </div>
</template>
<script>
import config from "../assets/js/config";
export default {
    data() {
      return {
        libros: [],
        headers_libros: [
        { text: "Código", value: "codigo" },
        { text: "Nombre", value: "nombre" },
        { text: "Estado", value: "estado" },
        { text: "Descripción", value: "descripcion" },
        { text: "Género", value: "genero" },
        { text: "Autor", value: "autor" },
        { text: "Acciones", value: "actions" },
      ],
        usuarios: [],
        headers_usuarios: [
        { text: "Cedula", value: "cedula" },
        { text: "Nombre", value: "nombre" },
        { text: "Correo", value: "correo" },
        { text: "Teléfono", value: "telefono" },
        { text: "Acciones", value: "actions" },
      ],
      nombre: null,
      correo: null,
      telefono: null,
      cedula: null,
      requiredRule: [(v) => !!v || "El campo es obligatorio"],
      nombre2: null,
      descripcion: null,
      genero: null,
      autor: null,
      }
    },
    beforeMount() {
      this.cargarLibros();
      this.cargarUsuarios();
    },
    methods: {
      async cargarLibros() {
        let url = config.URL_API + "/libros";
        let payload = {};
        let response = await this.$axios.get(url, payload)
        let data = response.data
        if (data.ok == true) {
          console.log(data.info);
        }
        for(let i=0;i<data.info.length;i++){
            this.libros.push(data.info[i]);
        }
      },
      async cargarUsuarios() {
        let url = config.URL_API + "/usuarios";
        let payload = {};
        let response = await this.$axios.get(url, payload)
        let data = response.data
        if (data.ok == true) {
          console.log(data.info);
        }
        for(let i=0;i<data.info.length;i++){
            this.usuarios.push(data.info[i]);
        }
      },
      async eliminarUsuario(item){
        let url = config.URL_API + "/usuarios";
        let payload = {};
        payload.cedula = item.cedula
        let response = await this.$axios.delete(url, {data:payload})
        let data = response.data
        console.log(data);
        location.reload();
      },
      async eliminarLibro(item){
        let url = config.URL_API + "/libros";
        let payload = {};
        payload.codigo = item.codigo
        let response = await this.$axios.delete(url, {data:payload})
        let data = response.data
        console.log(data);
        location.reload();
      },
      async modificarLibro(item){
          console.log(item.codigo);
          try {
        if (!this.$refs.formularioLibro.validate()) {
          return;
        }
        let url = config.URL_API + "/libros";
        let payload = {};
        payload.nombre = this.nombre2
        payload.descripcion = this.descripcion
        payload.genero = this.genero
        payload.autor = this.autor
        payload.codigo = item.codigo
        let response = await this.$axios.put(url, payload)
        let data = response.data
        console.log(data);
        if(data.ok==true){
            location.reload();
        }
          }catch(error){
            this.$swal({
          type: "error",
          icon: "error",
          title: "Oops...",
          text: "Ha ocurrido un error. No se ha podido conectar a API.",
        });
          }
      },
      async modificarUsuario(item){
          console.log(item.cedula);
          try {
        if (!this.$refs.formularioUsuario.validate()) {
          return;
        }
        let url = config.URL_API + "/usuarios";
        let payload = {};
        payload.nombre = this.nombre
        payload.correo = this.correo
        payload.telefono = this.telefono
        payload.cedula = item.cedula
        let response = await this.$axios.put(url, payload)
        let data = response.data
        console.log(data);
        if(data.ok==true){
            location.reload();
        }
          }catch(error){
              this.$swal({
          type: "error",
          icon: "error",
          title: "Oops...",
          text: "Ha ocurrido un error. No se ha podido conectar a API.",
        });
          }
      }
    },
  }
</script>

<style scoped>
#forms{
    left: auto;
}
.text{
  text-align: center;
}
#container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  margin-bottom: 10px;
  background: rgb(43, 67, 148);
}
</style>