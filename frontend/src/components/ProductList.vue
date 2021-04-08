<template>
  <div>
    <h6 ><b><u>SCG VENDING MACHINE BY PUNYAWEE POS</u></b></h6>
    <strong>
      <i>
        (NOTE: Edit and Add new Products are created for admin but you can try them in this demo project)
      </i>
    </strong>
    <br><br>
    <h6 ><b>#USER</b></h6>
    <p6><i>Insert coins to increase your balance</i>
      <br>

    </p6>


    <table class="table is-striped is-bordered mt-2 is-fullwidth">
      <thead>
      <tr>
        <th>User</th>
        <th>Coins</th>
        <th>Balance</th>
        <th>Item</th>
      </tr>
      </thead>
      <tbody>
      <tr>
        <td>SCG-USER</td>
        <td>
          <button @click='add1'>+1</button>
          <button @click='add5'>+5</button>
          <button @click='add10'>+10</button>
          <button @click='add20'>+20</button>
          <button @click='add50'>+50</button>
          <button @click='add100'>+100</button>
        </td>
        <td>{{ result }}</td>
        <td>{{ userItem }}</td>
      </tr>
      </tbody>
    </table>
    <h6><b>#VENDING MACHINE <i><u>@LOCATION: {{deviceLocation}}</u></i> </b></h6>
    <p6><i>Choose 'GET ITEM' to get your drink. Your balance will deduct automatically</i>
      <br>
    </p6>

    <table class="table is-striped is-bordered mt-2 is-fullwidth">
      <thead>
      <tr>
        <th>Product Name</th>
        <th>Price</th>
        <th>Unit</th>
        <th class="has-text-centered">Actions</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="item in items" :key="item.product_id">
        <td>{{ item.product_name }}</td>
        <td>{{ item.product_price }}</td>
        <td>{{ item.product_unit }}</td>
        <td class="has-text-centered">
          <router-link
              :to="{ name: 'Edit', params: { id: item.product_id } }"
              class="button is-info is-small"
          >EDIT DETAILS</router-link
          >
          <a
              class="button is-danger is-small"
              @click="deleteProduct(item.product_id)"
          >GET ITEM</a
          >
        </td>
      </tr>
      </tbody>
      <br>
      <router-link :to="{ name: 'Create' }" class="button is-success is-small"
      >ADD NEW PRODUCT</router-link
      >
    </table>

  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "ProductList",
  data() {
    return {
      items: [],
      result: 0,
      userItem : [],
      deviceLocation: 'Bangkok, Huaykwang, RAMA9, 10310',

    };
  },
  props: ['value'],
  created() {
    this.getProducts();
  },

  methods: {
    // Get All Products
    async getProducts() {
      try {
        const response = await axios.get("http://localhost:5000/products");
        this.items = response.data;
      } catch (err) {
        console.log(err);
      }
    },

    // Delete Product after user pick up
    async deleteProduct(id) {
      try {
        const temp = await axios.get(`http://localhost:5000/products/${id}`);
        const tempPrice = temp.data.product_price;
        const tempName = temp.data.product_name;
        const tempUnit = temp.data.product_unit;

        //ENOUGH BALANCE
        if(this.result >= tempPrice){
          //ITEM ONLY 1 LEFT
          if(tempUnit==1){
            this.result-=tempPrice;//DEDUCT BALANCE
            await axios.delete(`http://localhost:5000/products/${id}`);//DELETE LAST ITEM
            this.userItem.push(tempName);//USER GET ITEM
            alert('DELETE ITEM')
          }
          //ITEM LEFT LEST THAN 10 BUT GREATER THAN 1
          else if(tempUnit == 10) {
            this.result -= tempPrice;//DEDUCT BALANCE
            await axios.put(`http://localhost:5000/products/${id}/${tempUnit - 1}`,
                {
                  product_name: tempName,
                  product_price: this.productPrice,
                });//UPDATE NEW UNIT-1 TO ITEM
            this.userItem.push(tempName)//USER GET ITEM
            alert('ITEM NOW LESS THAN 10 UNITS')
            await axios.post(`http://localhost:5000/products/email/${this.deviceLocation}`);//EMAIL ADMIN
            alert('EMAIL WARNING SENT');
          }
          else{
            this.result-=tempPrice;//DEDUCT BALANCE
            await axios.put(`http://localhost:5000/products/${id}/${tempUnit-1}`,
                {
                  product_name: tempName,
                  product_price: this.productPrice,
                });//UPDATE NEW UNIT-1 TO ITEM
            this.userItem.push(tempName)//USER GET ITEM
            alert('USER GOT ITEM')
          }
        }
        //NOT ENOUGH BALANCE
        else{
          alert(`You have ${this.result} coins, But the price is ${tempPrice}`)
          alert('TRY INSERT COINS')
        }
        this.getProducts();
      } catch (err) {
        console.log(err);
      }
    },

    emitResult () {
      this.$emit('input', this.result)
    },
    add1 () {
      this.result += 1
      this.emitResult()
    },
    add5 () {
      this.result += 5
      this.emitResult()
    },
    add10 () {
      this.result += 10
      this.emitResult()
    },
    add20 () {
      this.result += 20
      this.emitResult()
    },
    add50 () {
      this.result += 50
      this.emitResult()
    },
    add100 () {
      this.result += 100
      this.emitResult()
    },
  },
};
</script>

<style>
</style>