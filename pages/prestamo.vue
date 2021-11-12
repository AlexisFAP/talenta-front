<template>
  <div>
    <v-card id="card" max-width="600px">
        <v-card-title primary-title> Elegir la Fecha del prestamo </v-card-title>
        <v-row justify="center">
            <v-date-picker class="date" color="blue light" header-color="primary" v-model="fecha"></v-date-picker>
          </v-row>
          <br>
        <v-card-actions id="a">
          <v-btn color="success" @click="hacerPrestamo()">Efectuar Prestamo</v-btn>
        </v-card-actions>
    </v-card>
  </div>
</template>
<script>
import config from "../assets/js/config";
export default {
  data() {
    return{
        libro:null,
        check:true,
      fecha: (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10),
      fecha_prestamo: (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10)
    };
  },
  beforeMount() {
      this.cargarLibros();
    },
  methods: {
    async hacerPrestamo() {
      try {
        if(this.check == false){
            this.$swal({
            type: "error",
            icon: "error",
            title: "Oops...",
            text: "El libro ya se encuentra prestado",
          });
            return;
        }
        let url = config.URL_API + "/prestamos";
        let payload = {};
        payload.usuario = localStorage.getItem("cedula")
        payload.libro = localStorage.getItem("codigo_libro")
        payload.fecha_prestamo = this.fecha_prestamo;
        payload.fecha_entrega_establecida = this.fecha;
        let response = await this.$axios.post(url, payload)
        let data = response.data;
        if (data.ok == true) {
        this.cambiarEstadoLibro();
          console.log(data.info);
          this.$swal({
            type: "success",
            icon: "success",
            title: "Prestamo Hecho",
            text: "El prestamo se efectuo correctamente",
          }).then((result) =>{
            if(result.isConfirmed){
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
    async cambiarEstadoLibro(){
        let url = config.URL_API + "/libro-estado";
        let payload = {};
        payload.estado = "Prestado"
        payload.codigo = localStorage.getItem("codigo_libro")
        let response = await this.$axios.put(url, payload)
        let data = response.data
        if (data.ok == true) {
          location.reload();
          console.log(data.info);
        }
    },
    async cargarLibros() {
        let url = config.URL_API + "/libros";
        let payload = {};
        let response = await this.$axios.get(url, payload)
        let data = response.data
        if (data.ok == true) {
          console.log(data.info);
        }
        for(let i=0;i<data.info.length;i++){
            if(localStorage.getItem("codigo_libro")==data.info[i].codigo){
                if(data.info[i].estado != "Disponible"){
                    this.check=false;
                }
            }
        }
      },
  },
};
</script>
<style scoped>
#card{
  margin: 0 auto;
}
#a{
    justify-content: center;
}
.theme--dark.v-card {
  background-color: #0077B6;
}
.theme--dark.success {
  background-color: rgb(115, 204, 219) !important;
  border-color: rgb(115, 204, 219) !important;
}
</style>