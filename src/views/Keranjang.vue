<template>
  <div class="keranjang">
    <Navbar :updateKeranjangs="keranjangs" />
    <div class="container">
      <div class="row mt-5">
        <div class="col">
          <nav aria-label="breadcrumb">
            <ol class="breadcrumb">
              <li class="breadcrumb-item">
                <router-link to="/" class="text-dark">Home</router-link>
              </li>
              <li class="breadcrumb-item">
                <router-link to="/foods" class="text-dark">Foods</router-link>
              </li>
              <li class="breadcrumb-item">Keranjang</li>
            </ol>
          </nav>
        </div>
      </div>

      <div class="row">
        <div class="col">
          <h2>Keranjang <strong>Saya</strong></h2>
          <div class="table-responsive">
            <table class="table">
              <thead class="thead-dark">
                <tr class="text-center">
                  <th scope="col">No.</th>
                  <th scope="col">Foto</th>
                  <th scope="col">Makanan</th>
                  <th scope="col">Keterangan</th>
                  <th scope="col">Jumlah</th>
                  <th scope="col">Harga</th>
                  <th scope="col">Total Harga</th>
                  <th scope="col">Hapus</th>
                </tr>
              </thead>
              <tbody>
                <tr
                  v-for="(keranjang, index) in keranjangs"
                  :key="keranjang.id"
                  class="text-center"
                >
                  <th>
                    <strong>{{ index + 1 }}</strong>
                  </th>
                  <td>
                    <img
                      :src="'../assets/images/' + keranjang.products.gambar"
                      alt="gambar"
                      class="img-fluid shadow"
                      width="200"
                    />
                  </td>
                  <td>
                    <strong>{{ keranjang.products.nama }}</strong>
                  </td>
                  <td>
                    <strong>
                      {{ keranjang.keterangan ? keranjang.keterangan : "-" }}
                    </strong>
                  </td>
                  <td>
                    <strong>{{ keranjang.jumlah_pemesanan }}</strong>
                  </td>
                  <td>
                    <strong>{{ keranjang.products.harga }}</strong>
                  </td>
                  <td>
                    <strong>
                      {{
                        keranjang.products.harga * keranjang.jumlah_pemesanan
                      }}
                    </strong>
                  </td>
                  <td class="text-danger">
                    <b-icon-trash
                      @click="hapusPesanan(keranjang.id)"
                    ></b-icon-trash>
                  </td>
                </tr>

                <tr>
                  <td colspan="6" class="text-right">
                    <strong>Total Pembayaran :</strong>
                  </td>
                  <td colspan="2" class="text-center">
                    <strong>Rp. {{ totalBayar }}</strong>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>

      <!-- Form checkout -->
      <div class="row justify-content-end">
        <form class="mt-4" v-on:submit.prevent>
          <div class="form-group">
            <label for="nama">Nama Pemesan: </label>
            <input type="text" class="form-control" v-model="pesan.nama" />
          </div>

          <div class="form-group">
            <label for="nomor_meja">Nomor Meja: </label>
            <input
              type="text"
              class="form-control"
              v-model="pesan.nomor_meja"
            />
          </div>

          <button
            type="submit"
            class="btn btn-success float-right"
            @click="checkout"
          >
            <b-icon-cart></b-icon-cart>
            Pesan
          </button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import Navbar from "../components/Navbar.vue";
import axios from "axios";

export default {
  name: "Keranjang",
  components: {
    Navbar,
  },
  data() {
    return {
      keranjangs: [],
      pesan: {},
    };
  },
  methods: {
    setKeranjangs(data) {
      this.keranjangs = data;
    },
    getKeranjangs() {
      return axios
        .get("http://localhost:3000/keranjangs")
        .then((response) => this.setKeranjangs(response.data))
        .catch((error) => console.error(error));
    },
    hapusPesanan(id) {
      axios
        .delete(`http://localhost:3000/keranjangs/${id}`)
        .then(() => {
          this.$toast.error("Pesanan berhasil dihapus dari keranjang", {
            type: "error",
            position: "top-right",
            duration: 2000,
            dismissible: true,
          });
          this.getKeranjangs();
        })
        .catch((error) => console.error(error));
    },
    hapusKeranjangs() {
      return this.keranjangs.map((item) => {
        return axios
          .delete(`http://localhost:3000/keranjangs/${item.id}`)
          .catch((error) => console.error(error));
      });
    },
    checkout() {
      if (this.pesan.nama && this.pesan.nomor_meja) {
        this.pesan.keranjangs = this.keranjangs;
        axios
          .post("http://localhost:3000/pesanans", this.pesan)
          .then(() => {
            this.$router.push({ path: "/pesanan-sukses" });
            this.hapusKeranjangs();
            this.$toast.success("Pesanan berhasil dipesan", {
              type: "success",
              position: "top-right",
              duration: 2000,
              dismissible: true,
            });
          })
          .catch((error) => console.error(error));
      } else {
        this.$toast.error("Nama Pemesan dan Nomor meja wajib diisi", {
          type: "error",
          position: "top-right",
          duration: 2000,
          dismissible: true,
        });
      }
    },
  },
  mounted() {
    this.getKeranjangs();
  },
  computed: {
    totalBayar() {
      return this.keranjangs.reduce((item, value) => {
        return item + Number(value.products.harga * value.jumlah_pemesanan);
      }, 0);
    },
  },
};
</script>

<style></style>
