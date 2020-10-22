<template>
  <div class="card shadow" style="width: 18rem;">
    <img
      :src="'/assets/images/' + product.gambar"
      class="card-img-top"
      alt="foto makanan"
    />
    <div class="card-body">
      <h5 class="card-title">{{ product.nama }}</h5>
      <p class="card-text">
        Harga : <strong>Rp. {{ product.harga }}</strong>
      </p>
      <router-link
        :to="'/foods/' + product.id"
        href="#"
        class="btn btn-success"
      >
        <b-icon-cart></b-icon-cart>
        Pesan
      </router-link>
    </div>
  </div>
</template>

<script>
export default {
  name: "CardProduct",
  props: ["product"],
};
</script>

<style></style>
