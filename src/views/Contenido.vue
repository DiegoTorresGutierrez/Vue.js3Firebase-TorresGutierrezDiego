<template>
  <Navbar/>
  <div class="container-md">
    <div id="carouselExampleControls" class="carousel slide" data-bs-ride="carousel">
      <div class="carousel-inner">
        <div class="carousel-item active">
          <img src="https://w.wallhaven.cc/full/v9/wallhaven-v9kw9l.jpg" class="d-block w-100" alt="...">
        </div>
        <div class="carousel-item">
          <img src="https://w.wallhaven.cc/full/8o/wallhaven-8o7jz1.jpg" class="d-block w-100" alt="...">
        </div>
        <div class="carousel-item">
          <img src="https://w.wallhaven.cc/full/l3/wallhaven-l38poq.jpg" class="d-block w-100" alt="...">
        </div>
        <div class="carousel-item">
          <img src="https://w.wallhaven.cc/full/1k/wallhaven-1kdzlg.jpg" class="d-block w-100" alt="...">
        </div>
        <div class="carousel-item">
          <img src="https://w.wallhaven.cc/full/p2/wallhaven-p2q5zj.jpg" class="d-block w-100" alt="...">
        </div>
      </div>
      <button class="carousel-control-prev" type="button" data-bs-target="#carouselExampleControls" data-bs-slide="prev">
        <span class="carousel-control-prev-icon" aria-hidden="true"></span>
        <span class="visually-hidden">Previous</span>
      </button>
      <button class="carousel-control-next" type="button" data-bs-target="#carouselExampleControls" data-bs-slide="next">
        <span class="carousel-control-next-icon" aria-hidden="true"></span>
        <span class="visually-hidden">Next</span>
      </button>
    </div>
  </div>
</template>

<script>
import Navbar from '@/components/Navbar.vue'
import { collection, getDocs, addDoc, deleteDoc, doc, getDoc, updateDoc  } from 'firebase/firestore/lite';
import { db, storage } from "../main";
import firebase from 'firebase/compat/app';
import router from '../router/index'

export default {
  name: 'About',
  components: {
    Navbar
  },
  data() {
    return {
      file: null,
      datoImagen: null,
      error: null,
      editar: false,
      loading: false,

      usuarios: [],
      usuario: {
      nombre: '',
      correo: '',
      foto: ''
    }
  }
  },
  methods: {
    async obtenerDatos () { 
      const querySnapshot = await getDocs(collection(db, "usuarios"));
        querySnapshot.forEach((doc) => {
        let usuario = doc.data()
        usuario.id = doc.id
        this.usuarios.push(usuario)
        console.log(usuario)
      });
    },
    // DELETE / ELIMINAR / BORRAR
    async eliminarDato (id){
      await deleteDoc(doc(db, "usuarios", id ));
      router.go('/')
    },
    // GET BY ID / OBTENER POR ID
    async obtenerDatoID (id){
      const docRef = doc(db, "usuarios", id);
      const docSnap = await getDoc(docRef);
          if (docSnap.exists()) {
            this.usuario = docSnap.data()
            this.usuario.id = docSnap.id
            } 
            else {
            console.log("¡No existe el documento!");
            }
    },

     // BUSCAR IMAGEN
    buscarImagen(event){
      const tipoArchivo = event.target.files[0].type;
      if(tipoArchivo === 'image/jpeg' || tipoArchivo === 'image/png'){
          this.file = event.target.files[0]
          this.error = null
      }
          else{
          this.error = 'Archivo no válido'
          this.file = null
          return 
          }
          const reader = new FileReader();
          reader.readAsDataURL(this.file);
          reader.onload = (e) => {
          this.datoImagen = e.target.result
          }
    },
    // SUBIR IMAGEN STORAGE
  async agregarDato(){
    try {
     this.loading = true
      var storageRef = firebase.storage().ref();
      await storageRef.child('imagenes').child(this.file.name).put(this.file)
      const urlDescarga = await storageRef.child('imagenes').child(this.file.name).getDownloadURL()
      await addDoc(collection(db, "usuarios"), {
          nombre: this.usuario.nombre,
          correo: this.usuario.correo,
          foto: urlDescarga
        })
        this.error = 'Imagen subida con éxito'
        this.file = null
    } 
      catch (error) {
        console.log(error);
      } 
      finally {
        router.push('/')
        this.loading = false
      }
    },

// MÉTODO actualizarDato
async actualizarDato(){
    try {
      this.loading = true
      var storageRef = firebase.storage().ref();
      await storageRef.child('imagenes').child(this.file.name).put(this.file)
      const urlDescarga = await storageRef.child('imagenes').child(this.file.name).getDownloadURL()
      const elemento = doc(db, "usuarios", this.usuario.id )
      await updateDoc(elemento, {
          nombre: this.usuario.nombre,
          correo: this.usuario.correo,
          foto: urlDescarga
        })
        this.error = 'Imagen subida con éxito'
        this.file = null
    } 
      catch (error) {
        console.log(error);
      } 
      finally {
        router.push('/')
        this.loading = false
      }}
  },
  mounted() {
    this.obtenerDatos();
  },
}

</script>