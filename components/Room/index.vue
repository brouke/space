<template>
    <div class="add-room-container">
      
      <div v-if="loading" class="loading">
        <p>Отправка данных...</p>
      </div>
      
      <div v-else-if="error" class="error">
        <p>{{ error }}</p>
        <button @click="resetForm" class="btn btn-secondary">Попробовать снова</button>
      </div>
      
      <div v-else-if="success" class="success">
        <p>Комната успешно добавлена!</p>
        <button @click="resetForm" class="btn btn-primary">Добавить еще комнату</button>
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
          <label for="photo">Фотография комнаты</label>
          <input 
            type="file" 
            id="photo" 
            @change="handleFileUpload" 
            required
            accept="image/*"
            class="form-control-file"
          >
          <small class="form-text text-muted">Рекомендуемый размер: не более 2MB</small>
        </div>
        
        <div class="form-actions">
          <button type="submit" class="btn btn-primary" :disabled="loading">Добавить комнату</button>
          <button type="button" @click="resetForm" class="btn btn-secondary">Очистить форму</button>
        </div>
      </form>
    </div>
  </template>
  
  <script setup>
  import { ref, reactive } from 'vue';
  
  // Состояние компонента
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
    conditioner: false
  });
  
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
  
  // Отправка формы
  const submitForm = async () => {
    try {
      loading.value = true;
      error.value = null;
      
      // Проверка наличия фото
      if (!photo.value) {
        error.value = 'Необходимо загрузить фотографию комнаты';
        loading.value = false;
        return;
      }
      
      // Создаем объект FormData для отправки данных, включая файл
      const formData = new FormData();
      formData.append('Name', roomData.Name);
      formData.append('description', roomData.description);
      formData.append('level', roomData.level);
      formData.append('square', roomData.square);
      formData.append('conditioner', roomData.conditioner);
      formData.append('rental', roomData.rental);
      formData.append('photo', photo.value);
      
      // Получаем токен из куки (если требуется аутентификация)
      const token = getCookie('access_token');
      
      // Настраиваем заголовки запроса
      const headers = {};
      if (token) {
        headers['Authorization'] = `Bearer ${token}`;
      }
      
      // Отправляем запрос на сервер с использованием fetch
      const response = await fetch('http://127.0.0.1:8000/Room', {
        method: 'POST',
        body: formData,
        headers: headers
      });
      
      // Проверяем статус ответа
      if (!response.ok) {
        const errorData = await response.json();
        throw new Error(errorData.detail || `HTTP error! Status: ${response.status}`);
      }
      
      // Получаем данные ответа
      const data = await response.json();
      console.log('Ответ сервера:', data);
      
      // Устанавливаем флаг успешного добавления
      success.value = true;
    } catch (err) {
      console.error('Ошибка при добавлении комнаты:', err);
      error.value = `Ошибка при добавлении комнаты: ${err.message}`;
    } finally {
      loading.value = false;
    }
  };
  
  // Сброс формы
  const resetForm = () => {
    // Сбрасываем данные формы
    roomData.Name = '';
    roomData.description = '';
    roomData.level = '';
    roomData.square = '';
    roomData.rental = '';
    roomData.conditioner = false;
    
    // Сбрасываем файл
    photo.value = null;
    
    // Сбрасываем состояние компонента
    error.value = null;
    success.value = false;
    
    // Сбрасываем input file
    const fileInput = document.getElementById('photo');
    if (fileInput) {
      fileInput.value = '';
    }
  };
  </script>
  
  <style scoped>
  .add-room-container {
    max-width: 600px;
    margin: 0 auto;
    padding: 20px;
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