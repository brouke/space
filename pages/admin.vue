<template>
    <Header></Header>
    {{ rooms }}
    <div class="blck">
        <div class="blck_title">Помещения</div>

        <div class="blck_all">
        <div class="blck_all_add">
            <div class="blck_all_add_title">Добавить помещения</div>
            <div class="form">
                <AddRoom />
            </div>
        </div>

        <div class="blck_all_room">
            <div class="blck_all_room_title">Все помещения</div>
            
  <div class="admin_cart">
  <div class="block" v-for="item in items" @click="openRoomDetails(item.id)">
    <div class="block_img">
      <img :src="item.photo_url" alt="">
    </div>
    <div class="block_info">
      <div class="block_info_title">{{item.Name}}</div>
      <div class="block_info_desc">{{item.level}}этаж, Площадь (м²):{{ item.square }}</div>
      <div class="block_info_desc">
      <div class="block_info_details">{{item.description}}</div>
</div>
    </div>
  </div>
</div> 
        </div>
        </div>
    </div>
       <ModalsDescRoom
       :user-id="selectedUserId"
       v-if="ModalOpen"
      @close="ModalOpen = false"
       />
</template>
<script setup>
const selectedUserId = ref(null)
const ModalOpen = ref(false);

import AddRoom from '@/components/Room';
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
const openRoomDetails = (userId) => {
  selectedUserId.value = userId;
  ModalOpen.value = true;
};
onMounted(fetchItems);
</script>
