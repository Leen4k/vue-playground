<script setup>
import axios from "axios";
import {computed, ref, watchEffect} from "vue";

const products = ref([]);
const filterKeyword = ref("");
const currentCategory = ref("All"); // Added a ref for the current category
let loading = ref(true);

const buttons = [
  {id:0, name:"All"},
  {id:1, name:"electronics"},
  {id:2, name:"women's clothing"},
  {id:3, name:"men's clothing"},
  {id:4, name:"saint laurent"},
]

const fetchData = async () => {
  try {
    loading = true;
    const response = await fetch('https://fakestoreapi.com/products'); 
    if (!response.ok) {
      throw new Error('Network response was not ok');
    }
    const jsonData = await response.json();
    products.value = jsonData;
    console.log(jsonData)
    loading = false;
  } catch (error) {
    console.error('Error fetching data:', error);
  }
};

watchEffect(() => {
  fetchData();
});

let filteredProducts = computed(()=>{
  const keyword = filterKeyword.value.toLowerCase().trim();
  if(keyword === ""){
    return products.value;
  }else{
    return products.value.filter(product => product.title.toLowerCase().includes(keyword.toLowerCase()))
  }
})

const filterCategory = async (category) => {
  currentCategory.value = category
  try{
    loading = true;
    const response = await fetch('https://fakestoreapi.com/products'); 
    if (!response.ok) {
      throw new Error('Network response was not ok');
    }
    const jsonData = await response.json();
    products.value = jsonData;

    if(currentCategory.value == "All"){
      loading = false;
      return products.value;
    }else{
      products.value = products.value.filter(product => product.category === category);
    }
    
    
    loading = false;
  }catch(err){
    console.log(err)
  }

};

</script>

<template>
  <h1 class="mb-8">products</h1>
  <div class="flex gap-2 my-8 justify-between">
    <div class="flex gap-2 my-8">
      <button :class="button.name === currentCategory?`bg-indigo-400`:``" @click="filterCategory(button.name)" v-for="button in buttons" :key="button.id">{{ button.name }}</button>
    </div>
    <div class="flex items-center">
      <input v-model="filterKeyword" type="text" placeholder="search..." class="rounded-md px-8 py-2">
    </div>
  </div>
  <div class="grid sm:grid-cols-1 md:grid-cols-3 lg:grid-cols-4 gap-2">  
    <div v-if="loading">Loading...</div>
    <div v-if="filteredProducts.length === 0">
      <p>No products found...</p>
    </div>
    <div v-else v-for="product in filteredProducts" :key="product.id" class="shadow-md rounded-md" >
      <div class="flex items-center justify-center">
        <img class="h-64" :src="[product.image]" alt="">
      </div>
      <p>{{ product.title }}</p>
    </div>
  </div>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
