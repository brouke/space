<template>
  <Header></Header>
  {{ items }}
  <input type="file" ref="fileInput" />
    <button @click="sendData">Отправить</button>
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
<div class="cart">
  <div class="block">
    <div class="block_img">
    </div>
    <div class="block_info">
      <div class="block_info_title">Помещение А 70 000 Р.</div>
      <div class="block_info_desc">1 этаж, 100 кв. м.</div>
      <div class="block_info_desc">
      <div class="block_info_details">Аренда помещения на первом этаже торгового центра 
площадью 100 кв. метров с кондиционером. Идеально 
подходит для розничной торговли или офисов, с большими 
витринными окнами для привлечения клиентов. </div>
</div>
  <Button 
        placeholder="Арендовать"
        :className="'btn_buy-button'"
        />
    </div>
  </div>
</div>  
  </template>
  <style >
  @import '@/assets/scss/index.scss';
  </style>
  <script setup>
//  fetch('http://127.0.0.1:8000/Document')
//   .then(response => response.json())
//   .then(data => console.log(data))
//   .catch(error => console.error('Ошибка:', error));
  

//   import { ref, onMounted } from 'vue';

// const items = ref([]); // Хранит полученные данные
// const error = ref(null); // Хранит ошибки, если они возникнут

// // Функция для получения данных
// const fetchItems = async () => {
//   try {
//     const response = await fetch('http://127.0.0.1:8000/Document');
//     if (!response.ok) {
//       throw new Error('Ошибка сети');
//     }
//     const data = await response.json();
//     items.value = data; // Сохраняем данные в реактивной переменной
//   } catch (err) {
//     error.value = err.message; // Сохраняем сообщение об ошибке
//   }
// };
// onMounted(fetchItems);

import { ref } from 'vue';

const fileInput = ref(null);

const sendData = async () => {
  const url = 'http://127.0.0.1:8000/Document';
  const formData = new FormData();

  // Объект данных
  const data = {
    rental: 1,
    idclient: 1,
    number: 1,
    login: "qwe",
    Name: "qwe",
    adress: "qwe",
    requisites: 1,
    password: "qwe",
    contactface: "qwe"
  };

  // Добавляем объект данных в FormData
  for (const key in data) {
    formData.append(`data[${key}]`, data[key]); // Оборачиваем в data
  }

  // Получаем файл из input
  const file = fileInput.value.files[0]; // Используем ref
  if (file) {
    formData.append('photo', file); // Добавляем файл в FormData
  } else {
    console.error('Файл не выбран');
    return;
  }

  try {
    const response = await fetch(url, {
      method: 'POST',
      body: formData // Отправляем FormData
    });

    if (!response.ok) {
      const errorData = await response.json();
      throw new Error(`Ошибка: ${JSON.stringify(errorData)}`); // Выводим более подробную информацию об ошибке
    }

    const result = await response.json();
    console.log('Документ успешно добавлен:', result);
  } catch (error) {
    console.error('Ошибка:', error);
  }
};
  </script>
