<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar
      :clipped-left="clipped"
      fixed
      app
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-btn
        icon
        @click.stop="miniVariant = !miniVariant"
      >
        <v-icon>mdi-{{ `chevron-${miniVariant ? 'right' : 'left'}` }}</v-icon>
      </v-btn>
      
      <v-toolbar-title v-text="title" />
      <v-spacer />
      <v-btn id="login" @click="cerrar()">Cerrar Sesión</v-btn>
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    
    <v-footer
      :absolute="!fixed"
      app
    >
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  data () {
    return {
      clipped: true,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-apps',
          title: 'Inicio',
          to: '/'
        },
        
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Talenta - Biblioteca',
      show: false,
    }
  },
  beforeMount() {
      this.verificarRol();
      this.sesion();
  },
  methods: {
    verificarRol(){
        if(localStorage.getItem("admin")=="admin"){
            let a = {
          icon: 'mdi-chart-bubble',
          title: 'Adiministrador',
          to: '/admin'
        }
        this.items.push(a)
        let b = {
            icon: 'mdi-chart-bubble',
          title: 'Ingresar Libro',
          to: '/libros'
        }
        this.items.push(b)
        }
    },
    cerrar(){
        localStorage.clear();
        location.reload();
    },
    sesion(){
      if(localStorage.length > 0){
        $nuxt.setLayout('autentificado')
      }
    }
  }
}
</script>
