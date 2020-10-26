<template>
  <div>
    <Navbar />
    <div class="row mt-5 main-container">
      <div class="col-md-3">
        <div class="col mt-4 mb-4">
          <h4>Filter Harga</h4>
          <form class="mt-4" v-on:submit.prevent>
            <div class="form-group">
              <label for="hargaMin">Harga Minimum: </label>
              <input type="number" class="form-control" v-model="hargaMin" />
            </div>

            <div class="form-group">
              <label for="hargaMax">Harga Maksimal: </label>
              <input type="number" class="form-control" v-model="hargaMax" />
            </div>

            <button
              type="submit"
              class="btn btn-success w-100"
              @click="filterHarga"
            >
              Terapkan
            </button>
          </form>
        </div>
        <div class="col mt-4">
          <h4>Filter Jenis Makanan</h4>
          <form class="mt-4" v-on:submit.prevent>
            <div class="checkbox">
              <label>
                <input
                  type="checkbox"
                  value="burger"
                  v-model="kategoriMakanan"
                />
                Burger</label
              >
            </div>
            <div class="checkbox">
              <label>
                <input
                  type="checkbox"
                  value="pizza"
                  v-model="kategoriMakanan"
                />
                Pizza
              </label>
            </div>
            <div class="checkbox">
              <label>
                <input
                  type="checkbox"
                  value="spagetti"
                  v-model="kategoriMakanan"
                />
                Spagetti
              </label>
            </div>
            <div class="checkbox">
              <label>
                <input
                  type="checkbox"
                  value="lainnya"
                  v-model="kategoriMakanan"
                />
                Lainnya
              </label>
            </div>
            <button
              type="submit"
              class="btn btn-success w-100"
              @click="filterKategoriMakanan"
            >
              Terapkan
            </button>
          </form>
        </div>
      </div>

      <div class="col">
        <div class="row mt-4">
          <div class="col">
            <h1>Daftar <strong>Makanan</strong></h1>
          </div>
        </div>

        <div class="row mt-3">
          <div class="col">
            <div class="input-group mb-3">
              <input
                v-model="search"
                type="text"
                class="form-control"
                placeholder="Cari makanan kesukaan Anda..."
                aria-label="Cari"
                aria-describedby="basic-addon1"
                @keyup="searchFood"
              />
              <div class="input-group-prepend">
                <span class="input-group-text" id="basic-addon1">
                  <b-icon-search></b-icon-search>
                </span>
              </div>
            </div>
          </div>
        </div>

        <div class="row mt-5 mb-3">
          <div
            class="col-md-4 mt-4"
            v-for="product in products"
            :key="product.id"
          >
            <CardProduct :product="product" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Navbar from "../components/Navbar.vue";
import CardProduct from "@/components/CardProduct.vue";
import axios from "axios";

export default {
  components: {
    Navbar,
    CardProduct,
  },
  data() {
    return {
      products: [],
      search: "",
      hargaMax: 0,
      hargaMin: 0,
      kategoriMakanan: [],
    };
  },
  methods: {
    setProducts(data) {
      if (data === undefined || data.length == 0) {
        const getData = axios
          .get("http://localhost:3000/products?q=" + this.search)
          .then((response) => this.setProducts(response.data))
          .catch((error) => console.error(error));

        this.$toast.error("INVALID_INPUT", {
          type: "error",
          position: "top-right",
          duration: 2000,
          dismissible: true,
        });
        this.products = getData;
      }

      this.products = data;
      this.hargaMin = 0;
      this.hargaMax = 0;
    },
    searchFood() {
      axios
        .get("http://localhost:3000/products?q=" + this.search)
        .then((response) => this.setProducts(response.data))
        .catch((error) => console.error(error));
    },
    async filterHarga() {
      const hargaMin = this.hargaMin;
      const hargaMax = this.hargaMax;

      const productFilter = await axios
        .get("http://localhost:3000/products")
        .then((response) => {
          const products = response.data;
          if (hargaMin !== 0 && hargaMax !== 0) {
            return products.filter((product) => {
              return (
                Number(product.harga) >= hargaMin &&
                Number(product.harga) <= hargaMax
              );
            });
          } else if (hargaMin !== 0) {
            return products.filter((product) => {
              return Number(product.harga) >= hargaMin;
            });
          } else if (hargaMax !== 0) {
            return products.filter((product) => {
              return Number(product.harga) <= hargaMax;
            });
          } else {
            return this.setProducts(response.data);
          }
        })
        .catch((error) => console.error(error));
      return this.setProducts(productFilter);
    },
    async filterKategoriMakanan() {
      const productFilter = await axios
        .get("http://localhost:3000/products")
        .then((response) => {
          const filterMakanan = this.kategoriMakanan;
          const products = response.data;

          const filterResult = filterMakanan.map((value) => {
            return products.filter(
              (product) => product.kategori_makanan === value
            );
          });

          return [].concat(...filterResult);
        })
        .catch((error) => console.error(error));

      return this.setProducts(productFilter);
    },
  },
  mounted() {
    axios
      .get("http://localhost:3000/products")
      .then((response) => this.setProducts(response.data))
      .catch((error) => console.error(error));
  },
};
</script>

<style></style>
