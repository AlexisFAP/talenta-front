<template>
  <div>
    <v-card id="card" max-width="600px">
      <v-form ref="formularioSingup">
        <v-card-title primary-title> Registrarse </v-card-title>
        <v-card-subtitle>* Campo Obligatorio</v-card-subtitle>
        <v-card-text>
          <v-text-field
            color="white"
            v-model="cedula"
            :rules="requiredRule"
          > <template #label>
              <span class="red--text"><strong>* </strong></span>Cedula
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
            v-model="correo"
            :rules="requiredRule"
          > <template #label>
              <span class="red--text"><strong>* </strong></span>Correo
            </template>
          </v-text-field>
          <v-text-field
            :type="show_password ? 'text' : 'password'"
            color="white"
            v-model="clave"
            counter
            maxlength="20"
            :rules="passwordRule"
            :append-icon="show_password ? 'mdi-eye-off' : 'mdi-eye'"
              @click:append="show_password = !show_password"
          > <template #label>
              <span class="red--text"><strong>* </strong></span>Clave
            </template>
          </v-text-field>
          <v-text-field
            color="white"
            v-model="telefono"
            :rules="requiredRule"
          > <template #label>
              <span class="red--text"><strong>* </strong></span>Teléfono
            </template>
          </v-text-field>
        </v-card-text>
        <v-card-actions>
          <v-btn color="success" @click="singup()">Registrarse</v-btn>
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
      cedula: null,
      nombre: null,
      correo: null,
      clave: null,
      telefono: null,
      requiredRule: [(v) => !!v || "El campo es obligatorio"],
      passwordRule: [(v) => !!v || "El campo es obligatorio",value => value && value.length >= 8 || 'Min 8 caracteres, Max 20 caracteres'],
      show_password: false,
    };
  },
  methods: {
    async singup() {
      try {
        if (!this.$refs.formularioSingup.validate()) {
          return;
        }
        let url = config.URL_API + "/usuarios";
        let payload = {};
        payload.cedula = this.cedula;
        payload.nombre = this.nombre;
        payload.correo = this.correo;
        payload.clave = this.clave;
        payload.telefono = this.telefono;
        if(this.correo == "admin"){
            this.$swal({
            type: "error",
            icon: "error",
            title: "Oop...",
            text: "Usuario no valido",
          })
          return;
        }
        let response = await this.$axios.post(url, payload)
        let data = response.data;
        if (data.ok == true) {
          console.log(data.info);
          this.$swal({
            type: "success",
            icon: "success",
            title: "Usuario Creado",
            text: "Su usuario se pudo crear exitosamente",
          }).then((result) =>{
            if(!result.isConfirmed){
              this.$router.push("/");
            }
          });
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