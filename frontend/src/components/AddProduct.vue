<template>
  <div>
    <div class="field">
      <label class="label">Product Name</label>
      <div class="control">
        <input
            class="input"
            type="text"
            placeholder="Product Name"
            v-model="productName"
        />
      </div>
    </div>

    <div class="field">
      <label class="label">Price</label>
      <div class="control">
        <input
            class="input"
            type="text"
            placeholder="Price"
            v-model="productPrice"
        />
      </div>
    </div>

    <div class="field">
      <label class="label">Unit</label>
      <div class="control">
        <input
            class="input"
            type="text"
            placeholder="Unit"
            v-model="productUnit"
        />
      </div>
    </div>


    <div class="control">
      <button class="button is-success is-small" @click="saveProduct">SAVE</button>
    </div>
  </div>
</template>

<script>
// import axios
import axios from "axios";

export default {
  name: "AddProduct",
  data() {
    return {
      productName: "",
      productPrice: "",
      productUnit: "",
    };
  },
  methods: {
    // Create New product
    async saveProduct() {
      try {
        await axios.post("http://localhost:5000/products", {
          product_name: this.productName,
          product_price: this.productPrice,
          product_unit: this.productUnit,
        });
        this.productName = "";
        this.productPrice = "";
        this.productUnit = "";
        this.$router.push("/");
      } catch (err) {
        console.log(err);
      }
    },
  },
};
</script>

<style>
</style>