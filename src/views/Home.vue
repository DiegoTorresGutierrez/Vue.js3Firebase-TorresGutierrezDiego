<template class="position-relative">
  <div>
      <Navbar />
      <div class="container my-5">
          <div class="row row-cols-1 row-cols-md-3 g-4">
              <div class="col" v-for="(item, index) in usuarios" :key="index">
                  <div class="card">
                  <img :src= "item.foto" class="card-img-top">
                  <div class="card-body">
                      <h5 class="card-title text-center"> {{item.nombre}}</h5>
                      <p class="card-text text-center"> {{item.correo}}</p>
                  </div>
                  </div>
              </div>
          </div>
      </div>
  </div>
  
  <div class="cover-container ">
    <div>
      <h2 class="position-absolute top-50 start-50 translate-middle">Debes tener una cuenta para ver el contenido.</h2>
    </div>
  </div>
</template>

<script>
import Navbar from '../components/Navbar.vue'
import { collection, getDocs } from 'firebase/firestore/lite';
import { db } from "../main";
export default {
  name: 'Home',
  components: { 
    Navbar,   
  },
  data() {
      return {
        usuarios: [],
        usuario: {
          nombre: '',
          correo: '',
          foto: ''
        }
      }
  },
  methods:{
// GET
  async obtenerDatos () { 
    const querySnapshot = await getDocs(collection(db, "usuarios"));
      querySnapshot.forEach((doc) => {
      let usuario = doc.data()
      usuario.id = doc.id
      this.usuarios.push(usuario)
      console.log(usuario)
    });
  }
  },
  mounted() {
      this.obtenerDatos();
  },
}
</script>