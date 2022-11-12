<template >
   <div id="login" class="container">
      <div class="row justify-content-center">
         <div class="col-md-8 col-lg-5 col-sm-10">
            <div class="card">
               <div class="card-body p-5">
                  <h2 class="text-center">LOGIN</h2>
                  <p class="text-center text-muted mb-4"><i>Silakan masukkan email dan password</i></p>
                  <b>Email</b>
                  <div class="input-group mb-3 mt-2">
                     <div class="input-group-prepend">
                        <span class="input-group-text"><b-icon icon="person-fill"></b-icon></span>
                     </div>
                     <input id="u" type="text" class="form-control" placeholder="Tulis email disini" tabindex="1" v-model="email">
                  </div>
                  <b>Password</b>
                  <div class="input-group mb-3 mt-2">
                     <div class="input-group-prepend">
                        <span class="input-group-text"><b-icon icon="lock-fill"></b-icon></span>
                     </div>
                     <input id="p" type="password" class="form-control" placeholder="Tulis password disini" tabindex="2" v-model="password">
                     <button id="l" class="btn btn-lg btn-block btn-primary mt-5" @click.prevent="Login()"> Login </button>
                  </div>
               </div>
               <div class="create-new-acc-text text-center">
                  <p>Don't have account? <a class="btn" @click.prevent="Register()">Create an account</a></p>
               </div>
            </div>
         </div>
      </div>
   </div>
</template>

<script>
import axios from "axios";
import swal from "sweetalert2"

   export default {
      data() {
         return {
            email:'',
            password:'',
         };
      },

      methods: {
         Register(){
            this.$router.push({ path: '/register' })
         },

         Login(){
            if (this.email == "" && this.password == "") {
               swal.fire('GAGAL!','Email Dan Password Tidak Boleh Kosong','error')
            }
            else if (this.email == "") {
               swal.fire('GAGAL!','Email Tidak Boleh Kosong','error')
            }
            else if (this.password == "") {
               swal.fire('GAGAL!','Password Tidak Boleh Kosong','error')
            }
            else{
               axios
               .post("https://reqres.in/api/login",{
                  email: this.email,
                  password: this.password,
               })
               .then((response) => {
                  if (response.status == 200) {
                     localStorage.setItem("user-info", response.data.token)
                     this.$router.push({ path: '/' })
                  }
               })
               .catch((error) => {
                  console.log(error);
                  swal.fire('GAGAL!','Periksa lagi email dan password','error')
               });
            }

            
         }
      },
      mounted() {
         let user = localStorage.getItem('user-info');
         if (user) {
            this.$router.push({ path: '/' });
         }
      },
   }
</script>

<style>
      #login{
         margin-bottom: 100px;
         margin-top: 100px;
      }
</style>