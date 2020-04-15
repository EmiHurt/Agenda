<template>
 
	<div id="app" class="container">
    
		 
       <b-form v-if="is_signed">
    
        <div>
  <b-card
   title="Agenda De compras"
    class="text-center"
    
    
  >
   
   
  </b-card>
</div>
  <div>
    <b-col cols="10" md="12">
            
            <b-flex md12>
  <b-card bg-variant="light">
    
      <b-form-input
          
          v-model="lista.producto"
          type="Producto"
          
          placeholder="Producto"
           @keyup.enter="agregar"
        ></b-form-input>
   <b-form-input
          
          v-model="lista.precio"
          type="Precio"
          
          placeholder="Precio"
           @keyup.enter="agregar"
        ></b-form-input>

    
    
  </b-card>
  </b-flex>
  </b-col>
   <b-button variant="outline-success"  @click="agregar()">Agregar</b-button>
</div>
 <b-card
   title="Productos Agregados"
    class="text-center"
    
    
  >
   
    <b-table-simple>
                  <template v-slot:default>
                    <b-thead>
                      <b-tr>
                        <b-th class="text-left">Producto</b-th>
                        <b-th class="text-left">Precio</b-th>
                      </b-tr>
                    </b-thead>
                    <b-tbody>
                      
       <b-tr v-for="item in items" :key="item.producto">
          <b-td>{{ item.producto }}</b-td>
          <b-td>{{ item.precio }}</b-td>
           
    <b-button variant="outline-danger"  size="sm" @click="borrar(item)">borrar</b-button>
     
        </b-tr>
     
                      
                    </b-tbody>
                      
                  </template>
                  
                  
               </b-table-simple>
               <b-button variant="outline-info"   v-on:click="sumar()" >sumar</b-button>
                {{ total }}$
             

  </b-card>
  
  <b-button variant="primary"  @click="cerrarSesion()">Cerrar Sesion</b-button>
  </b-form>
   <div v-if="!is_signed" class="auth">

      <div class="option-buttons">
          <h3>Agenda De Compras</h3>
          <h5>Ingrese con su cuenta,sino tiene puede creearse una</h5>
          <a @click="modal_show('modal_login')" class="waves-effect waves-light cyan accent-4 btn-large button welcome-login">Log In</a>
          <a @click="modal_show('modal_signup')" class="waves-effect waves-light cyan accent-4 btn-large button welcome-signup">Sign Up</a>
      </div>

      <div id="modal_login" class="modal">
        <div class="modal-content">
          <h4>Log In</h4>
          <div class="login-form">
            <label for="email">Email</label>
            <input class="email" type="text" name="email" value="">
            <label for="password">Password</label>
            <input type="password" class="password">
            <button @click="login" type="button" class="btn cyan accent-4 register-login-button">Log in</button>
            <form>

 </form>
          </div>
        </div>
      </div>

      <div id="modal_signup" class="modal">
        <div class="modal-content">
          <h4>Sign Up</h4>
          <div class="register-form">
            <label for="email">Email</label>
            <input class="email" type="text" name="email" value="">
            <label for="password">Password</label>
            <input type="password" class="password">
            <label for="password-confirm">Confirm Password</label>
            <input type="password" class="password-confirm">
            <button @click="register" type="button" class="btn cyan accent-4" name="login">Register</button>
            <form>

 </form>
          </div>
        </div>
      </div>

      </div>
    
  </div>
       
       
         
      
  
    
    
      
    
    
      
    
	</div>
  
</template>



<script>

import Firebase from 'firebase';
import toastr from 'toastr';
let config = {
	apiKey: "AIzaSyBVPt6Wr9I5aMmmftEYmE1jvplOaqZ6Z24",
    authDomain: "agenda-compras-e1166.firebaseapp.com",
    databaseURL: "https://agenda-compras-e1166.firebaseio.com",
    projectId: "agenda-compras-e1166",
    storageBucket: "agenda-compras-e1166.appspot.com",
    messagingSenderId: "850952973407",
    appId: "1:850952973407:web:54a9fc38bd991daab73fea",
    measurementId: "G-5EF1Y7M44C"
};
let app = Firebase.initializeApp(config);
let db = app.database();
//let items = db.ref('items');
const auth = Firebase.auth();
export default {
name: 'app',
  beforeCreate: function() {
    // Our earliest lifecycle hook and first access
    // to $bindAsArray() / $bindAsObject() from VueFire
    // Start Firebase authentication here:
        auth.onAuthStateChanged(function(user) {
          if (user) {
            // Cache user - an anonymously authenticated firebase.User account
            //  - https://firebase.google.com/docs/reference/js/firebase.User
          this.is_signed = true;
            // Bind this instance's 'messages' property to the 'messages/${uid}'
            // Firebase reference via vuefire.js' $bindAsArray() method
            this.$bindAsArray('items', db.ref('items/' + user.uid + '/items'))
            // Note: Child component instances will have access to these
            // references via this.$root.user and this.$root.messages
            this.is_signeddIn = true
            
        } else {
            if(this.is_signed) {
                this.is_signed = false;
            }
        }
        }.bind(this));
  },
	data() {
		return {
		 
         is_signed: false,
      usuario: {
        email: '',
        contrasena: ''
      },
      
      
      
      
    
     
    lista:{
     precio: '',
      producto: '',
    },
    total:0,
     items: []
     
    };
  },
 
	methods: {
 
     register() {
      let email = $('.register-form .email').val();
      let password = $('.register-form .password').val();
      let passwordConfirm = $('.register-form .password').val();

      if (password == passwordConfirm) {
        auth.createUserWithEmailAndPassword(email, password)
          .catch(error => {
            if (error.code == 'auth/weak-password') {
              this.flash('Password has to be longer than 6 characters.', 'red accent-4');
            } else {
              this.flash(error.message, 'red accent-4');
            }
          });
      }
    },
    
    login() {
      let email = $('.login-form .email').val();
      let password = $('.login-form .password').val();

      auth.signInWithEmailAndPassword(email, password)
        .catch(error => this.flash(error.message, 'red accent-4'));
    },
    modal_show(modal) {
        $('#' + modal).fadeIn();
    },
    iniciarSesion: function(){
      var that = this;
      Firebase.auth().signInWithEmailAndPassword(that.usuario.email, that.usuario.contrasena).then(function(){
	toastr.success('Iniciaste sesión correctamente.');
      }, function(){
	toastr.error('El correo o la contrasena son incorrectos.');
      }).catch(function(error) {
	toastr.error('Error al intentar iniciar sesión.');
      });
    },
    cerrarSesion: function(){
      Firebase.auth().signOut().then(function() {
        // Sign-out successful.
      }).catch(function(error) {
        toastr.error('Error al intentar cerrar sesión.', 'Aviso');
      });
    },
		 agregar: function(){
       
       
      this.$firebaseRefs.items.push(this.lista, function(error){
        if (error){
          toastr.error('Error al intentar agregar el registro.', 'Aviso');
        }else{          
          toastr.success('Registro agregado correctamente.', 'Aviso');
        }
      });
      this.lista.producto = '';
      this.lista.precio = ''; 
    },
   
   
		borrar: function(item) {
			 this.$firebaseRefs.items.child(item['.key']).remove();
			toastr.success('Producto Borrado');
		},
    sumar() {
      this.total = 0;
      // eslint-disable-next-line no-plusplus
      for (let index = 0; index < this.items.length; index++) {
        this.total += parseFloat(this.items[index].precio);
      }
    },
	}
}
</script>

<style>
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  height: 100%;
  width: 100%;
    overflow: auto;
    padding-right: 20px;
}

.auth {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}

.option-buttons {
    text-align: center;
}

input {
    text-align: center;
}

.modal {
    position: absolute;
    top: 25%;
    z-index: 999;
}



.register-login-button {
    margin: 25px;
}

nav {
    text-align: left;
}

nav ul li {
    margin-right: 15px;
}



.register-form, .existing-notes {
    display: block !important;
}

.modal-content {
    opacity: 1 !important;
}



</style>