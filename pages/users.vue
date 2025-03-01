<template>
    <Header></Header>
    <div class="title">Все пользователи</div>
    <div class="info_user">
        <div class="user" v-for="item in users" @click="openUserDetails(item.id)">
        <div class="user_info1">
            <div class="user_info1_title">Данные пользователя:</div>
            <div class="user_info1_desc">
                <div>Login: {{ item.login }}</div>
                <div>Password: {{ item.password }}</div>
                <div>Имя: {{ item.Name }}</div>
                <div>Фамилия: {{ item.surname }}</div>
            </div>
        </div>
        <div class="user_info1">
            <div class="user_info1_title">Дополнительные данные:</div>
            <div class="user_info1_desc">
                <div>Адрес: {{ item.adress }}</div>
                <div>Телефон: {{ item.number }}</div>
                <div>Реквизиты: {{ item.requisites }}</div>
                <div>Контактное лицо: {{ item.contactface }}</div>
            </div>   
        </div> 
    </div>
    </div>
<ModalsDescUser
    :is-open="isModalOpen" 
    :user-id="selectedUserId"
    @close="closeModal"
    @edit="handleEditUser"
    @delete="handleDeleteUser"
/>
</template>
<script setup>
import { ref, onMounted } from 'vue';
import Cookies from 'js-cookie';

const users = ref([]);
const loading = ref(true);
const error = ref(null)
const isModalOpen = ref(false);
const selectedUserId = ref(null);

    const fetchUsers = async () => {
  loading.value = true;
  error.value = null;
  
  try {
    const token = Cookies.get('access_token');
    
    if (!token) {
      throw new Error('Пользователь не авторизован');
    }
    
    const response = await fetch('http://127.0.0.1:8000/User', {
      headers: {
        Authorization: `Bearer ${token}`
      }
    });
    
    if (!response.ok) {
      throw new Error('Не удалось получить список пользователей');
    }
    
    const data = await response.json();
    console.log('Полученные пользователи:', data);
    
    // Предполагаем, что сервер возвращает массив пользователей
    users.value = Array.isArray(data) ? data : [data];
    
  } catch (err) {
    error.value = err.message;
    console.error('Ошибка при получении пользователей:', err);
  } finally {
    loading.value = false;
  }
};

const handleEditUser = (userId) => {
  console.log('Редактирование пользователя с ID:', userId);
  // Здесь можно добавить логику для открытия формы редактирования
  // Например, открыть другое модальное окно с формой редактирования
};

// Обработчик события удаления пользователя
const handleDeleteUser = async (userId) => {
  try {
    const token = Cookies.get('access_token');
    
    if (!token) {
      throw new Error('Пользователь не авторизован');
    }
    
    const response = await fetch(`http://127.0.0.1:8000/user/${userId}`, {
      method: 'DELETE',
      headers: {
        Authorization: `Bearer ${token}`
      }
    });
    
    if (!response.ok) {
      throw new Error('Не удалось удалить пользователя');
    }
    
    // Удаляем пользователя из списка
    users.value = users.value.filter(user => user.id !== userId);
    
    // Закрываем модальное окно
    closeModal();
    
    alert('Пользователь успешно удален');
    
  } catch (err) {
    alert(`Ошибка: ${err.message}`);
    console.error('Ошибка при удалении пользователя:', err);
  }
};

const openUserDetails = (userId) => {
  selectedUserId.value = userId;
  isModalOpen.value = true;
};

// Функция для закрытия модального окна
const closeModal = () => {
  isModalOpen.value = false;
};

onMounted(fetchUsers);
</script>
