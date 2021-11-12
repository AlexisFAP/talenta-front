<template>
  <div>
  <v-card>
    <v-card-title primary-title class="text">
      Bienvenido a la biblioteca de talenta
    </v-card-title>
    <v-card-subtitle class="text" >
      Estos son nuestros ejemplares
    </v-card-subtitle>
    <v-row justify="space-around">
            <template v-for="(item) in libros">
              <v-card id="container" width="250" 
              :key="item.id">
                <v-card-title  primary-title> {{item.nombre}}</v-card-title>
                <v-card-text>
                  <b>Código: </b> {{item.codigo}}
                </v-card-text>
                <v-card-text>
                  <b>Estado: </b> {{item.estado}}
                </v-card-text>
                <v-card-text>
                  <b>Descripción: </b> {{item.descripcion}}
                </v-card-text>
                <v-card-text>
                  <b>Género: </b> {{item.genero}}
                </v-card-text>
                <v-card-text>
                  <b>Autor: </b> {{item.autor}}
                </v-card-text>
                <v-btn v-if="show" @click="pedirLibro(item.codigo)">Pedir Libro</v-btn>
              </v-card>
            </template>
            </v-row>
  </v-card>
  <br>
  <v-card v-if="show">
    <v-card-title primary-title class="text">
      Mis prestamos
    </v-card-title>
    <v-row justify="space-around">
            <template v-for="(item) in prestamos">
              <v-card id="container" width="250" 
              :key="item.id">
                <v-card-title  primary-title> Código Libro: {{item.libro}}</v-card-title>
                <v-card-text>
                  <b>Fecha del Prestamo: </b> {{item.fecha_prestamo}}
                </v-card-text>
                <v-card-text>
                  <b>Fecha de Entrega: </b> {{item.fecha_entrega_establecida}}
                </v-card-text>
                <v-card-text>
                  <b>Fecha libro devuelto: </b> {{item.fecha_entrega}}
                </v-card-text>
                <v-btn @click="entregarLibro(item.libro, item.id)">Entregar Libro</v-btn>
              </v-card>
            </template>
            </v-row>
  </v-card>
  </div>
</template>
<script>
import config from "../assets/js/config";
export default {
    data() {
      return {
        libros: [],
        show: false,
        prestamo: false,
        prestamos: [],
        fecha: (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10),
      }
    },
    beforeMount() {
      this.cargarLibros();
      this.cargarPrestamos();
    },
    methods: {
      async cargarPrestamos(){
        let url = config.URL_API + "/prestamos";
        let payload = {};
        let response = await this.$axios.get(url, payload)
        let data = response.data
        for(let i=0;i<data.info.length;i++){
            if(data.info[i].usuario == localStorage.getItem("cedula")){
              this.prestamos.push(data.info[i])
            }
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
            this.libros.push(data.info[i]);
        }
        if(localStorage.length > 0 && localStorage.getItem("admin") != "admin"){
          this.show = true;
        }
      },
      async pedirLibro(codigo){
        localStorage.setItem("codigo_libro", codigo)
        this.$router.push('/prestamo');
      },
      async entregarLibro(libro, id){
        let x = this.libros.find(x => x.codigo == libro)
        if(x.estado == "Prestado"){
          let url = config.URL_API + "/prestamos";
          let payload = {};
          payload.id = id
          payload.fecha_entrega = this.fecha
          let response = await this.$axios.put(url, payload)
          let data = response.data
          if (data.ok == true) {
          this.entregarLibro2(libro)
          console.log(data.info);
          this.$swal({
            type: "success",
            icon: "success",
            title: "Libro Entregado",
            text: "El libro se entrego correctamente",
          })
        } else {
          this.$swal({
            type: "error",
            icon: "error",
            title: "Oops...",
            text: data.data.message,
          });
        }
        }
      }, async entregarLibro2(codigo){
        let url = config.URL_API + "/libro-estado";
        let payload = {};
        payload.estado = "Disponible"
        payload.codigo = codigo
        let response = await this.$axios.put(url, payload)
        let data = response.data
        if(data.ok == true){
          location.reload();
        }
      }
    },
  }
</script>

<style scoped>
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