<template>
  <div>
    <v-card id="card" max-width="600px">
      <v-form ref="formularioLogin">
        <v-card-title primary-title> Ingresar </v-card-title>
        <v-card-text>
          <v-text-field
            label="Correo" color="white"
            v-model="correo"
            :rules="requiredRule"
          ></v-text-field>
          <v-text-field
            type="password"
            label="Clave" color="white"
            v-model="clave"
            :rules="requiredRule"
          ></v-text-field>
        </v-card-text>
        <v-card-actions>
          <v-btn color="success" @click="login()">Ingresar</v-btn>
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
      correo: null,
      clave: null,
      requiredRule: [(v) => !!v || "El campo es obligatorio"],
    };
  },
  methods: {
    async login() {
      try {
        if (!this.$refs.formularioLogin.validate()) {
          return;
        }
        if(this.correo == "admin"){
            localStorage.setItem("admin","admin")
            
            $nuxt.setLayout('autentificado')
            
            this.$router.push('/')
            return;
        }
        let url = config.URL_API + "/login";
        let payload = {};
        payload.correo = this.correo;
        payload.clave = this.clave;
        let response = await this.$axios.post(url, payload);
        let data = response.data;
        if (data.ok == true) {
            let cedula = data.info.cedula
            let nombre =  data.info.nombre
          localStorage.setItem('cedula', cedula)
          localStorage.setItem('nombre', nombre)
          localStorage.setItem('sesion',"Iniciada")
          $nuxt.setLayout('autentificado')
          this.$router.push('/')
        } else {
          this.$swal({
            type: "error",
            icon: "error",
            title: "Oops...",
            text: data.message,
          });
        }
        console.log(response);
      } catch (error) {
        this.sa
        this.$swal({
          type: "error",
          icon: "error",
          title: "Oops...",
          text: "Correo y/o contraseña incorrectos",
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
div.v-input.v-input--is-label-active.v-input--is-dirty.v-input--is-focused {
  caret-color: #fcfcfc !important;
  color: #fcfcfc !important;
}
</style>