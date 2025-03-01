<template>
  <Header></Header>
    <div id="app">
      <header>
        <nav>
          <nuxt-link to="/">Главная</nuxt-link>
          <nuxt-link to="/info">О Нас</nuxt-link>
        </nav>
      </header>
      <nuxt-child /> <!-- Здесь будут отображаться компоненты страниц -->
    </div>
    <Footer></Footer>
  </template>
  
  <script setup>
  fetch('http://127.0.0.1:8000/user')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Ошибка:', error));
  

  import { ref, onMounted } from 'vue';

const items = ref([]); // Хранит полученные данные
const error = ref(null); // Хранит ошибки, если они возникнут

// Функция для получения данных
const fetchItems = async () => {
  try {
    const response = await fetch('http://127.0.0.1:8000/User');
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
  