<template>
  <Header></Header>
  <div class="banner">
    <div class="banner_inf">
      <div class="banner_text">
        Аренда помещений
        в торговом центре
      </div>
      <Button 
        placeholder="Смотреть помещения"
        :className="'btn_cee-button'"
        />
      </div>
  </div>

<div class="zag">Помещения</div>
<nuxt-link to="/product">
  <div class="cart">
  <div class="block" v-for="item in items">
    <div class="block_img">
      <img :src="item.photo_url" alt="">
    </div>
    <div class="block_info">
      <div class="block_info_title">{{item.Name}}</div>
      <div class="block_info_desc">{{item.level}},{{ item.square }}</div>
      <div class="block_info_desc">
      <div class="block_info_details">{{item.description}}</div>
</div>
    </div>
  </div>
</div> 
</nuxt-link>
 
  </template>
  <style >
  @import '@/assets/scss/index.scss';
  </style>
  <script setup>
 fetch('http://127.0.0.1:8000/Room')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Ошибка:', error));
  

  import { ref, onMounted } from 'vue';

const items = ref([]); // Хранит полученные данные
const error = ref(null); // Хранит ошибки, если они возникнут

// Функция для получения данных
const fetchItems = async () => {
  try {
    const response = await fetch('http://127.0.0.1:8000/Room');
    if (!response.ok) {
      throw new Error('Ошибка сети');
    }
    const data = await response.json();
    items.value = data; // Сохраняем данные в реактивной переменной
  } catch (err) {
    error.value = err.message; // Сохраняем сообщение об ошибке
  }
};
onMounted(fetchItems);

  </script>
