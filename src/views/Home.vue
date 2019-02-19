<template>
  <div class="home">
    <h1>New Product</h1>
    <div>
      <div>
        Name: <input v-model="newProductName">
      </div>
      <div>
        Description: <input v-model="newProductDescription">
      </div>
      <div>
        Price: <input v-model="newProductPrice">
      </div>
      <div>
        Image URL: <input v-model="newProductImageUrl">
      </div>
      <button v-on:click="createProduct()">Create</button>
    </div>
    <h1> All Products </h1>
    <div v-for="product in products">
      <h2> {{ product.name }} </h2>
        <img v-bind:src="product.image_url" v-bind:alt="product.name">
      <div>
        <button v-on:click="showProduct(product)"> More Info </button>
      </div>
      <div v-if="product === currentProduct">
        <p>Description: {{product.description}} </p>
        <p>Price: {{product.formatted.price}} </p>
        <div>
          <h4> Edit Product </h4>
          <div>
            <div>
              Name: <input v-model="product.name">
            </div>
            <div>
              Description: <input v-model="product.description">
            </div>
            <div>
              Price: <input v-model="product.price">
            </div>
            <div>
              Image URL: <input v-model="product.image_url">
            </div>
            <button v-on:click="updateProduct(product)"> Update </button>
            <button v-on:click="destroyProduct(product)"> Delete </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
  img{
    width: 300px;
    height: 250px;
  }
</style>

<script>
  var axios = require("axios");

  export default {
    data: function() {
      return {
        products: [],
        newProductName: "",
        newProductDescription: "",
        newProductPrice: "",
        newProductImageUrl: "",
        currentProduct: {}
      };
    },
    created: function() {
      axios.get("/api/products")
        .then(response => {
          this.products = response.data;
        });
    },
    methods: {
      createProduct: function() {
        var params = {
                      name: this.newProductName,
                      description: this.newProductDescription,
                      price: this.newProductPrice,
                      image_url: this.newProductImageUrl
                      };
        axios.post("/api/products", params)
          .then(response => {
            console.log("Product Successfully Created!", response.data);
            this.products.push(response.data)
          });
      },
      showProduct: function(inputProduct) {
        if (this.currentProduct === inputProduct) {
          this.currentProduct = {};
        } else {
          this.currentProduct = inputProduct;
        }
      },
      updateProduct: function(inputProduct) {
        var params = {
                      name: inputProduct.name,
                      description: inputProduct.description,
                      price: inputProduct.price,
                      image_url: inputProduct.image_url
                    };
        axios.patch("/api/products/" + inputProduct.id, params)
        .then(response => {
          console.log("Successfully updated product!", response.data);
          inputProduct = response.data
        });
      },
      destroyProduct: function(inputProduct) {
        axios.delete("/api/products/" + inputProduct.id)
        .then(response => {
          console.log("Successfully deleted product", response.data)
          var index = this.products.indexOf(inputProduct);
          this.products.splice(index, 1)
        });
      }
    }
    };
</script>