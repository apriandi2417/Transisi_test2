<template>
  <div>
    <b-navbar toggleable="lg" type="light">
      <div class="container">
        <b-navbar-brand href="#">TRANSISI</b-navbar-brand>

        <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

        <b-collapse id="nav-collapse" is-nav>
          <b-navbar-nav>
            <li class="nav-item">
              <router-link class="nav-link" to="/">Home</router-link>
            </li>
          </b-navbar-nav>

          <!-- Right aligned nav items -->
          <b-navbar-nav class="ml-auto">
            <li class="nav-item">
              <button class="btn btn-danger btn-sm" @click.prevent="logout()">Logout</button>
            </li>
          </b-navbar-nav>
        </b-collapse>
      </div>
    </b-navbar>
  </div>
</template>

<script>
// import axios from "axios";
import swal from "sweetalert2"

export default {
  name: "Navbar",
  data() {
    return {

    };
  },
  props: [],
  methods: {
    logout(){
      swal.fire({
        title: 'Are you sure?',
        text: 'Kamu yakin ingin keluar?',
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'Yes'
      }).then((result) => {
        if (result.isConfirmed) {
          localStorage.clear();
          this.$router.push({ path: '/login' });
        }
      })
      
    }
  },
  mounted() {

  },
};
</script>

<style></style>
