<template>
  <q-page class="row items-center justify-evenly" style="background-color: #262626;">
    <q-card dark class="my-card" style="width: 90%;">
      <q-card-section>
        <div class="row justify-center">
          <div  class="col-8 q-py-md">
            <div class="text-h4" style="color: #ffd102;">NYC Product Store</div>
          </div>
          <div class="col-auto q-py-md">
              <q-btn dense style="background: #ffd102; color: #000;" icon="add" @click="prompt = addPrompt()" label="Add Product"/>
          </div>
        </div>
      </q-card-section>
      <q-card-section>
        <div class="row justify-left">
          <div  class="col-3 q-py-md">
            <div class="text-h6">Product Name</div>
          </div>
          <div  class="col-3 q-py-md">
            <div class="text-h6">Price</div>
          </div>
          <div  class="col-4 q-py-md">
            <div class="text-h6">Description</div>
          </div>
          <div class="col-auto q-py-md">
          </div>
        </div>
      </q-card-section>
      <q-separator dark inset/>
      <q-card-section v-if="(products.length > 0 && products[0].id.trim()!=='')">
        <div class="row justify-left" v-for="(item,index) in products" :key="index">
          <div  class="col-3 q-py-md" style="font-size:large;">
            {{ item.name }}
          </div>
          <div  class="col-3 q-py-md" style="font-size:large;">
            {{ item.price }}
          </div>
          <div  class="col-4 q-py-md" style="font-size:large;">
            {{ item.description }}
          </div>
          <div class="col-auto col-1 q-py-md">
            <q-btn dense color="blue" flat icon="create" @click="editPrompt = updatePrompt(index)" label="Update"/>
          </div>
          <div class="col-auto q-py-md">
            <q-btn dense color="red" flat icon="delete_forever" @click="deletePrompt = deleteProductPrompt(index)" label="Delete"/>
          </div>
        </div>
      </q-card-section>
      <q-card-section v-else>
        <div class="row justify-left">
          <div  class="col-12 q-py-md" style="font-size:large;">
            <div class="text-h4">Store is empty</div>
          </div>
        </div>
      </q-card-section>
    </q-card>
    <q-dialog v-model="deletePrompt" persistent>
      <q-card style="min-width: 25%">
        <q-card-section>
          <div class="text-h6">Are you sure you want to delete product: <span style="color: #f44336;">{{ toDeleteProduct }}</span>?</div>
        </q-card-section>
        <q-card-actions align="right" class="text-primary">
          <q-btn flat style="color: #000;"  label="Cancel" v-close-popup />
          <q-btn flat style="background: #f44336; color: #fff;" label="Yes" @click="deleteProduct"/>
        </q-card-actions>
      </q-card>
    </q-dialog>

    <q-dialog v-model="editPrompt" persistent>
      <q-card style="min-width: 25%">
        <q-card-section>
          <div class="text-h6">Update Product</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input dense v-model="productName" autofocus @keyup.enter="prompt = false" label="Product Name" />
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input dense v-model="price" autofocus @keyup.enter="prompt = false" label="Price" />
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input dense v-model="description" autofocus @keyup.enter="prompt = false" label="Description" />
        </q-card-section>

        <q-card-actions align="right" class="text-primary">
          <q-btn flat style="color: #000;"  label="Close" v-close-popup />
          <q-btn flat style="background: #ffd102; color: #000;" label="Update Product" @click="updateProduct"/>
        </q-card-actions>
      </q-card>
    </q-dialog>

    <q-dialog v-model="prompt" persistent>
      <q-card style="min-width: 25%">
        <q-card-section>
          <div class="text-h6">Add Product</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input dense v-model="productName" autofocus @keyup.enter="prompt = false" label="Product Name" />
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input dense v-model="price" autofocus @keyup.enter="prompt = false" label="Price" />
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input dense v-model="description" autofocus @keyup.enter="prompt = false" label="Description" />
        </q-card-section>

        <q-card-actions align="right" class="text-primary">
          <q-btn flat style="color: #000;"  label="Close" v-close-popup />
          <q-btn flat style="background: #ffd102; color: #000;" label="Add Product" @click="addProduct"/>
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script lang="ts">
// import { Todo, Meta } from 'components/models';
// import ExampleComponent from 'components/ExampleComponent.vue';
import axios from 'axios' 

import { defineComponent, ref } from 'vue';

const API_BASE_URL='http://localhost:4000/nyc/api';


export default defineComponent({
  name: 'IndexPage',
  getProducts() {
        axios.get(`${API_BASE_URL}/products`)
          .then((response) => {
            this.products = (response.data.status == 200 && response.data.data.length > 0) ? response.data.data: [];
          })
    },
  data() {
    const payload = {
      id: '',
      productName:'',
      price: 0.0,
      description: '',
      toDeleteProduct: '',
      products: [
        {
          id: '',
          name: '',
          price: 0,
          description: ''
        }
      ]
    };
    this.getProducts();
    return payload;
  },
    methods: {
      getProducts() {
        axios.get(`${API_BASE_URL}/products`)
          .then((response) => {
            this.products = (response.data.status == 200 && response.data.data.length > 0) ? response.data.data: [];
          })
      },
      addProduct() {
        const data = {name: this.productName,price: this.price, description: this.description};
        axios.post(`${API_BASE_URL}/products`,data)
        .then((res) => {
          if(res.data.status == 200 && res.data.message === 'SUCCESS') {
            this.getProducts();
          } 
        }).catch((e) => {
          const res = e.response;
          if(res.data.status === 400) {
              alert(res.data.message);
          }
          console.log(e.response);
        })
        
      },
      updateProduct() {
        // update product
        const data = {name: this.productName,price: this.price, description: this.description};
        axios.put(`${API_BASE_URL}/products/${this.id}`,data)
        .then((res) => {
          if(res.data.status === 200 && res.data.message === 'SUCCESS') {
            this.getProducts();
          } 
        }).catch((e) => {
          const res = e.response;
          if(res.data.status === 400) {
              alert(res.data.message);
          }
          console.log(e.response);
        })
      },
      deleteProduct() {
        // delete product
        axios.delete(`${API_BASE_URL}/products/${this.id}`)
        .then((res) => {
          if(res.data.status === 200 && res.data.message === 'SUCCESS') {
            this.getProducts();
          } 
        }).catch((e) => {
          const res = e.response;
          if(res.data.status === 400) {
              alert(res.data.message);
          }
          console.log(e.response);
        })
      },
      deleteProductPrompt(id: number) {
        this.toDeleteProduct = this.products[id].name;
        this.id = this.products[id].id;
        return true;
      },
      updatePrompt(id: number) {
        this.id = this.products[id].id;
        this.productName = this.products[id].name;
        this.price = this.products[id].price;
        this.description = this.products[id].description;
        return true
      },
      addPrompt() {
        this.id = '';
        this.productName = '';
        this.price = 0;
        this.description = '';
        return true
      }
    },
    setup () {
     
      return {
        prompt: ref(false),
        editPrompt: ref(false),
        deletePrompt: ref(false),
        address: ref('')
      }
  }
});
</script>
