<template>
  <div class="user-detail">
    <Navbar />
    <div class="container">
      <!-- breadcrumb -->
      <div class="row mt-4">
        <div class="col">
          <nav aria-label="breadcrumb">
            <ol class="breadcrumb">
              <li class="breadcrumb-item">
                <router-link to="/" class="text-dark">Home</router-link>
              </li>
              <li class="breadcrumb-item active" aria-current="page">User Detail</li>
            </ol>
          </nav>
        </div>
      </div>

      <div class="row mt-3">
        <div class="col-md-6">
          <img :src="product.avatar" class="img-fluid shadow" />
        </div>
        <div class="col-md-6">
          <h2>
            <strong>{{ product.first_name }} {{ product.last_name }}</strong>
          </h2>
          <hr />
          <h4>
            Email :
            <strong>{{ product.email }}</strong>
          </h4>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Navbar from "@/components/Navbar.vue";
import axios from "axios";

export default {
  name: "UserDetail",
  components: {
    Navbar,
  },
  data() {
    return {
      product: {},
      pesan: {},
    };
  },
  methods: {
    setProduct(data) {
      this.product = data.data;
      console.log(this.product);
    },
  },
  created(){
    let user = localStorage.getItem('user-info');
    if (!user) {
        this.$router.push({ path: '/login' });
    }
  },
  mounted() {
    axios
      .get("https://reqres.in/api/users/" + this.$route.params.id)
      .then((response) => this.setProduct(response.data))
      .catch((error) => console.log(error));
  },
};
</script>

<style>
</style>