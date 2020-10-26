<template>
  <div class="landing">
    <div class="row mt-5 justify-content-center">
      <div class="col-md-5 text-center">
        <h2><strong>Ayo!!! segara masuk</strong></h2>
        <h2>Dapatkan berbagai macam pilihan menu enak dari kami</h2>
      </div>
    </div>

    <div class="row mt-5 justify-content-center">
      <div class="col-md-5">
        <div class="login">
          <form @submit.prevent>
            <div class="form-group">
              <label for="email">Email</label>
              <input type="email" class="form-control" v-model="email" />
            </div>
            <div class="form-group">
              <label for="password">Password</label>
              <input type="password" class="form-control" v-model="password" />
            </div>

            <button
              @click="handleSubmit"
              type="submit"
              class="btn btn-success w-100 btn-lg"
            >
              Masuk
            </button>
          </form>
          <div class="row justify-content-center mt-3">
            <router-link to="/register" class="text-center font-weight-bold">
              Klik disini, bila kamu belum punya akun
            </router-link>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

const validateFakeToken = localStorage.getItem("fake-token");

export default {
  name: "Landing",
  data() {
    return {
      email: "",
      password: "",
    };
  },
  methods: {
    async handleSubmit() {
      const email = this.email;
      const password = this.password;

      await axios
        .get("http://localhost:3000/users")
        .then((response) => {
          const dataUsers = response.data;
          const findUser = dataUsers.filter((user) => {
            return user.email === email && user.password === password;
          });

          if (findUser === undefined || findUser.length == 0) {
            this.$toast.error("Email dan Password tidak cocok", {
              type: "error",
              position: "top-right",
              duration: 2000,
              dismissible: true,
            });
            return;
          }

          const fakeToken = Math.random();
          localStorage.setItem("fake-token", fakeToken);
          this.$router.push({ path: "/home" });
        })
        .catch((err) => console.error(err));
    },
  },

  beforeMount() {
    if (validateFakeToken !== null) {
      this.$router.push({ path: "/home" });
    }
  },
};
</script>

<style></style>
