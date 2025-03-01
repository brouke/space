<template>
  <div
    class="modals"
    ref="email"
    @click.self="$emit('close')"
    @keydown.esc="$emit('close')"
  >
    <div class="modal__info">
      <div class="modal__close">
        <ButtonClose @click="$emit('close')" />
      </div>
    
    <div v-if="initialLoading" class="loading">
      <p>Загрузка данных комнаты...</p>
    </div>
    
    <div v-else-if="success" class="success">
      <p>Комната успешно обновлена!</p>
      <div class="success-actions">
        <button @click="goBack" class="btn btn-primary">Вернуться к списку</button>
        <button @click="fetchRoomData" class="btn btn-secondary">Продолжить редактирование</button>
      </div>
    </div>
    
    <form v-else @submit.prevent="submitForm" class="room-form">
      <div class="form-group">
        <label for="Name">Название комнаты</label>
        <input 
          type="text" 
          id="Name" 
          v-model="roomData.Name" 
          required 
          class="form-control"
          placeholder="Например: Конференц-зал 1"
        >
      </div>
      
      <div class="form-group">
        <label for="description">Описание</label>
        <textarea 
          id="description" 
          v-model="roomData.description" 
          required
          class="form-control"
          rows="4"
          placeholder="Опишите комнату, укажите особенности и преимущества"
        ></textarea>
      </div>
      
      <div class="form-group">
        <label for="level">Этаж</label>
        <input 
          type="number" 
          id="level" 
          v-model="roomData.level" 
          required 
          class="form-control"
          min="1"
          placeholder="Например: 1"
        >
      </div>
      
      <div class="form-group">
        <label for="square">Площадь (м²)</label>
        <input 
          type="number" 
          id="square" 
          v-model="roomData.square" 
          required 
          class="form-control"
          min="1"
          placeholder="Например: 25"
        >
      </div>
      
      <div class="form-group">
        <label for="rental">Стоимость аренды (руб/мес)</label>
        <input 
          type="number" 
          id="rental" 
          v-model="roomData.rental" 
          required 
          class="form-control"
          min="0"
          placeholder="Например: 15000"
        >
      </div>
      
      <div class="form-group checkbox-group">
        <label class="checkbox-label">
          <input 
            type="checkbox" 
            v-model="roomData.conditioner"
          >
          Наличие кондиционера
        </label>
      </div>
      
      <div class="form-group">
        <label>Текущая фотография:</label>
        <div class="current-photo" v-if="roomData.photo">
          <img :src="getPhotoUrl(roomData.photo)" alt="Фото комнаты" class="room-photo">
          <p class="photo-path">{{ roomData.photo }}</p>
        </div>
        <p v-else>Фотография отсутствует</p>
      </div>
      
      <div class="form-group">
        <label for="photo">Загрузить новую фотографию:</label>
        <input 
          type="file" 
          id="photo" 
          @change="handleFileUpload" 
          accept="image/*"
          class="form-control-file"
        >
        <small class="form-text text-muted">Оставьте пустым, если не хотите менять фотографию</small>
      </div>
      
      <div class="form-actions">
        <button type="submit" class="btn btn-primary" @click="updateRoom(roomData)">Сохранить изменения</button>
        <button type="button" @click="goBack" class="btn btn-secondary">Отмена</button>
      </div>
    </form>
  </div>
  </div>
  
</template>

<script setup>
import { ref, reactive, onMounted } from 'vue';
import { useRoute, useRouter } from 'vue-router';

const props = defineProps({

    userId: {
      type: Object,
      default: null
    }
  });

const route = useRoute();
const router = useRouter();
const roomId = route.params.id;

// Состояние компонента
const initialLoading = ref(true);
const initialError = ref(false);
const loading = ref(false);
const error = ref(null);
const success = ref(false);
const photo = ref(null);

// Данные формы
const roomData = reactive({
  Name: '',
  description: '',
  level: '',
  square: '',
  rental: '',
  conditioner: false,
  photo: ''
});

// Функция для получения URL фотографии
const getPhotoUrl = (photoPath) => {
  // Если путь начинается с http или https, возвращаем его как есть
  if (photoPath.startsWith('http://') || photoPath.startsWith('https://')) {
    return photoPath;
  }
  
  // Иначе формируем URL относительно базового URL API
  return `http://127.0.0.1:8000/${photoPath}`;
};

// Обработчик загрузки файла
const handleFileUpload = (event) => {
  photo.value = event.target.files[0];
};

// Функция для получения значения куки по имени
const getCookie = (name) => {
  const cookies = document.cookie.split(';');
  for (let cookie of cookies) {
    const [cookieName, cookieValue] = cookie.trim().split('=');
    if (cookieName === name) {
      return cookieValue;
    }
  }
  return null;
};

// Функция для получения данных комнаты
const fetchRoomData = async () => {
  try {
    initialLoading.value = true;
    initialError.value = false;
    error.value = null;
    
    // Получаем токен из куки (если требуется аутентификация)
    const token = getCookie('access_token');
    
    // Настраиваем заголовки запроса
    const headers = {};
    if (token) {
      headers['Authorization'] = `Bearer ${token}`;
    }
    
    // Отправляем запрос на сервер с использованием fetch
    const response = await fetch(`http://127.0.0.1:8000/Room/${props.userId}`, {
      method: 'GET',
      headers: headers
    });
    
    // Проверяем статус ответа
    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }
    
    // Получаем данные ответа
    const data = await response.json();
    console.log('Данные комнаты:', data);
    
   
    roomData.Name = data.Name;
    roomData.description = data.description;
    roomData.level = data.level;
    roomData.square = data.square;
    roomData.rental = data.rental;
    roomData.conditioner = data.conditioner;
    roomData.photo = data.photo;
    
  } catch (err) {
    console.error('Ошибка при получении данных комнаты:', err);
    error.value = `Ошибка при получении данных комнаты: ${err.message}`;
    initialError.value = true;
  } finally {
    initialLoading.value = false;
  }
};

// Отправка формы
async function updateRoom( roomData) {
    try {
        const response = await fetch(`http://127.0.0.1:8000/Room/${props.userId}`, {
            method: 'PUT',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify(roomData),  // Преобразуем объект в JSON
        });

        if (!response.ok) {
            const errorData = await response.json();
            console.error('Ошибка:', errorData);  // Выводим подробности об ошибке
            throw new Error(`Ошибка: ${errorData.detail}`);
        }

        const updatedRoom = await response.json();  // Получаем обновленную комнату
        console.log('Комната обновлена:', updatedRoom);
        return updatedRoom;  // Возвращаем обновленную комнату
    } catch (error) {
        console.error('Ошибка при обновлении комнаты:', error);
    }
}

// Функция для возврата назад
const goBack = () => {
  router.back();
};

// Загружаем данные комнаты при монтировании компонента
onMounted(() => {
  fetchRoomData();
});
</script>

<style scoped>
.add-room-container {
    max-width: 600px;
    margin: 0 auto;
    padding: 20px;
  }
  .room-photo{
    width: 60%;
  }
  h2 {
    margin-bottom: 20px;
    color: #333;
    text-align: center;
  }
  
  .loading, .error, .success {
    text-align: center;
    padding: 20px;
    margin: 20px 0;
    border-radius: 8px;
  }
  
  .loading {
    background-color: #f8f9fa;
  }
  
  .error {
    color: #721c24;
    background-color: #f8d7da;
    border: 1px solid #f5c6cb;
  }
  
  .success {
    color: #155724;
    background-color: #d4edda;
    border: 1px solid #c3e6cb;
  }
  
  .room-form {
    background-color: white;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
  
  .form-group {
    margin-bottom: 15px;
  }
  
  label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
  }
  
  .checkbox-group {
    display: flex;
    align-items: center;
  }
  
  .checkbox-label {
    display: flex;
    align-items: center;
    font-weight: bold;
    cursor: pointer;
  }
  
  .checkbox-label input {
    margin-right: 8px;
  }
  
  .form-control {
    width: 97%;
    padding: 8px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 16px;
  }
  
  .form-control-file {
    padding: 8px 0;
  }
  
  .form-text {
    display: block;
    margin-top: 5px;
    color: #6c757d;
  }
  
  .form-actions {
    margin-top: 20px;
    display: flex;
    justify-content: space-between;
  }
  
  .btn {
    padding: 10px 15px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 16px;
    transition: background-color 0.3s;
  }
  
  .btn-primary {
    border-radius: 4px !important;
            background: #60617F !important;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 16px;
            padding: 8px;
            border: none;
  }
  
  .btn-primary:hover {
    background-color: #0069d9;
  }
  
  .btn-secondary {
    background-color: #6c757d;
    color: white;
  }
  
  .btn-secondary:hover {
    background-color: #5a6268;
  }
  
  .btn:disabled {
    opacity: 0.65;
    cursor: not-allowed;
  }
</style>