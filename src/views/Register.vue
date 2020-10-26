<template>
  <div class="register">
    <div class="row mt-5 justify-content-center">
      <div class="col-md-5 text-center">
        <h2><strong>Kamu belum punya akun??</strong></h2>
        <h3>Isi form dibawah ini dengan benar yah :D</h3>
      </div>
    </div>

    <div class="row mt-5 justify-content-center">
      <div class="col-md-5">
        <div class="login">
          <form @submit.prevent>
            <div class="form-group">
              <label for="nama">Nama Lengkap</label>
              <input type="text" class="form-control" v-model="nama" />
            </div>
            <div class="form-group">
              <label for="email">Email</label>
              <input type="email" class="form-control" v-model="email" />
            </div>
            <div class="form-group">
              <label for="password">Password</label>
              <input type="password" class="form-control" v-model="password" />
            </div>
            <div class="form-group">
              <label for="konfirmasi_password">Konfirmasi Password</label>
              <input
                type="password"
                class="form-control"
                v-model="konfirmasi_password"
              />
            </div>

            <button
              @click="handleRegister"
              type="submit"
              class="btn btn-success w-100 btn-lg"
            >
              Registrasi
            </button>
          </form>
          <div class="row justify-content-center mt-3">
            <router-link to="/" class="text-center font-weight-bold">
              Sudah punya akun
            </router-link>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Register",
  data() {
    return {
      nama: "",
      email: "",
      password: "",
      konfirmasi_password: "",
    };
  },
  methods: {
    handleRegister() {
      if (this.password !== this.konfirmasi_password) {
        this.$toast.error("Password tidak sama, harap ketik ulang", {
          type: "error",
          position: "top-right",
          duration: 2000,
          dismissible: true,
        });
        return;
      }

      const data = {
        nama: this.nama,
        email: this.email,
        password: this.password,
        konfirmasi_password: this.konfirmasi_password,
      };

      axios
        .post("http://localhost:3000/users", data)
        .then(() => {
          this.$router.push({ path: "/" });
          this.$toast.success("Registrasi Berhasil yayy!!", {
            type: "error",
            position: "top-right",
            duration: 2000,
            dismissible: true,
          });
        })
        .catch((error) => console.error(error));
    },
  },
};
</script>

<style></style>
