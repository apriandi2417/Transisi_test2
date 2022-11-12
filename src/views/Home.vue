<!-- eslint-disable vue/require-v-for-key -->
<template>
  <div class="home">
    <Navbar />
    <div class="container">
      <!-- <Hero /> -->

      <div class="row mt-4 mb-3">
        <div class="col">
          <b-button v-b-modal.modal-prevent-closing class="btn btn-success float-right" @click.prevent="resetModal()">Tambah Data</b-button>
        </div>
      </div>

      <div class="card mb-3 w-100" v-for="item in users">
        <div class="row g-0">
          <div class="col-md-2">
            <img :src="item.avatar" class="img-fluid rounded-start w-100" alt="">
          </div>
          <div class="col-md-8">
            <div class="card-body">
              <h5 class="card-title">{{ item.first_name }} {{ item.last_name }}</h5>
              <p class="card-text">Email : {{ item.email }}</p>
            </div>
          </div>
          <div class="col-md-2 text-center">
            <div class="card-body">
              <router-link class="btn btn-info btn-sm mb-2" :to="'/users/'+item.id">Detail</router-link><br>
              <!-- <button class="btn btn-warning btn-sm mb-2" @click.prevent="updateData(item)">Edit</button><br> -->
              <b-button v-b-modal.modal-prevent-closing class="btn btn-warning btn-sm mb-2" @click.prevent="updateData(item)">Edit</b-button><br>
              <button class="btn btn-danger btn-sm" @click.prevent="hapus(item)">Delete</button>
            </div>
          </div>
        </div>
      </div>

      <div class="row">
        <div class="col-lg-12">
          <div class="shop-pagination text-center">
            <!-- <ul class="list-unstyled"> -->
            <ul class="list-unstyled" id="page">
              <li v-for="x in total_pages">
                <a class="active" @click.prevent="gantiHalaman(x)">{{ x }}</a>
              </li>
            </ul>
          </div>
        </div>
      </div>

    </div>

    <b-modal id="modal-prevent-closing" ref="modal" :title="title + ' Data'" @hidden="resetModal" @ok="handleOk">
      <form ref="form" @submit.stop.prevent="handleSubmit">
        <b-form-group label="Name" label-for="name-input" invalid-feedback="name is required" :state="nameState">
          <b-form-input id="name-input" v-model="name" :state="nameState" required></b-form-input>
        </b-form-group>
        <b-form-group label="Job" label-for="job-input" invalid-feedback="job is required" :state="jobState">
          <b-form-input id="job-input" v-model="job" :state="jobState" required></b-form-input>
        </b-form-group>
      </form>
    </b-modal>

  </div>
</template>

<script>
import Navbar from "@/components/Navbar.vue";
import axios from "axios";
import swal from "sweetalert2"

export default {
  name: "Home",
  components: {
    Navbar,
  },
  data() {
    return {
      users: [],
      name:'',
      job:'',
      id:'',
      nameState: null,
      jobState: null,
      isEdit: false,
      title: 'Tambah',
      total_pages:'',

    };
  },
  methods: {
    setUsers(data) {
      this.users = data.data;
      this.total_pages = data.total_pages;
    },
    gantiHalaman(x){
      axios
      .get("https://reqres.in/api/users?page=" + x)
      .then((response) => this.setUsers(response.data))
      .catch((error) => console.log(error));
    },
    updateData(item){
      if (item) {
        this.isEdit = true;
        this.title = "Edit";
        this.name = item.first_name + item.last_name;
        this.id = item.id;
      }
    },
    hapus(item){
      // swal.fire('Test!', 'Hello test message','success');
      swal.fire({
        title: 'Are you sure?',
        text: 'Kamu yakin ingin menghapus data '+item.first_name +" "+item.last_name+"?",
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'Yes'
      }).then((result) => {
        if (result.isConfirmed) {
          axios
          .delete("https://reqres.in/api/users/" + item.id,{
            name : this.name,
            job : this.job,
          })
          .then((response) => {
            // console.log(response);
            if (response.status == 204) {
              this.$nextTick(() => {
                this.$bvModal.hide('modal-prevent-closing')
              });
              swal.fire('Deleted!','Data Berhasil Dihapus.','success')
            }
          })
          .catch((error) => {
              this.$nextTick(() => {
                this.$bvModal.hide('modal-prevent-closing')
              });
              console.log(error);
              // swal.fire('Deleted!','Data Berhasil Dihapus.','success')
          });
        }
      })

    },
    checkFormValidity() {
      const valid = this.$refs.form.checkValidity()
      this.nameState = valid
      this.jobState = valid
      return valid
    },
    resetModal() {
      this.name = '';
      this.job = '';
      this.id = '';
      this.nameState = null;
      this.jobState = null;
      this.isEdit = false;
      this.title = 'Tambah';
    },
    handleOk(bvModalEvent) {
      // Prevent modal from closing
      bvModalEvent.preventDefault()
      // Trigger submit handler
      this.handleSubmit()
    },
    handleSubmit() {
      // Exit when the form isn't valid
      if (!this.checkFormValidity()) {
        return
      }
      // Push the name to submitted names
      if (this.isEdit) {
        //edit data
        axios
        .put("https://reqres.in/api/users/" + this.id,{
          name : this.name,
          job : this.job,
        })
        .then((response) => {
          if (response.status == 200) {
            this.$nextTick(() => {
              this.$bvModal.hide('modal-prevent-closing')
            });
            swal.fire('Berhasil!', 'Data Berhasil Diubah','success');
          }
        })
        .catch((error) => {
            this.$nextTick(() => {
              this.$bvModal.hide('modal-prevent-closing')
            });
            swal.fire('Gagal!', 'Data Gagal Diubah' +error,'error');
        });

      }else{
        //create data
        axios
        .post("https://reqres.in/api/users",{
          name : this.name,
          job : this.job,
        })
        .then((response) => {
          if (response.status == 201) {
            this.$nextTick(() => {
              this.$bvModal.hide('modal-prevent-closing')
            });
            swal.fire('Berhasil!', 'Data Berhasil Ditambah','success');
          }
        })
        .catch((error) => {
            this.$nextTick(() => {
              this.$bvModal.hide('modal-prevent-closing')
            });
            swal.fire('Gagal!', 'Data Gagal Ditambah' +error,'error');
        });
      }
    }

  },
  created(){
    let user = localStorage.getItem('user-info');
    if (!user) {
        this.$router.push({ path: '/login' });
    }
  },
  mounted() {
    axios
      .get("https://reqres.in/api/users?page=1")
      .then((response) => this.setUsers(response.data))
      .catch((error) => console.log(error));
  },
};
</script>

<style>

.shop-pagination {
  padding-top: 40px;
}

@media (min-width: 768px) {
  .shop-pagination {
    padding-top: 60px;
  }
}

.shop-pagination ul {
  margin: 0px -3px 0px -3px;
}

.shop-pagination ul li {
  display: inline-flex;
  margin: 0px 3px 0px 3px;
}

.shop-pagination a {
  width: 40px;
  height: 40px;
  line-height: 40px;
  border-radius: 50%;
  text-align: center;
  border: 1px solid #EAECED;
  background-color: #fff;
  text-decoration: none;
  color: #0D152E;
  font-size: 16px;
  font-weight: 700;
  font-style: normal;
  letter-spacing: -0.53px;
  transition: all 0.4s ease-in-out;
}

.shop-pagination a:hover {
  background: #416ff4;
  color: #fff;
}

.shop-pagination a.active {
  background: #416ff4;
  color: #fff;
}
</style>
