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
      <button class="button is-success is-small" @click="updateProduct">UPDATE</button>
    </div>
  </div>
</template>

<script>
// import axios
import axios from "axios";

export default {
  name: "EditProduct",
  data() {
    return {
      productName: "",
      productPrice: "",
      productUnit: "",
    };
  },
  created: function () {
    this.getProductById();
  },
  methods: {
    // Get Product By Id
    async getProductById() {
      try {
        const response = await axios.get(
            `http://localhost:5000/products/${this.$route.params.id}`
        );
        this.productName = response.data.product_name;
        this.productPrice = response.data.product_price;
        this.productUnit = response.data.product_unit;
      } catch (err) {
        console.log(err);
      }
    },

    // Update product
    async updateProduct() {
      try {
        await axios.put(
            `http://localhost:5000/products/${this.$route.params.id}`,
            {
              product_name: this.productName,
              product_price: this.productPrice,
              product_unit: this.productUnit,
            }
        );
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