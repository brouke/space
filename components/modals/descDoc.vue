<template>
    <div class="edit-document-container" @click="">
      <h2>Редактирование документа</h2>
      
      <div v-if="loading" class="loading">
        <p>Загрузка документа...</p>
      </div>
      
      <div v-else-if="error" class="error">
        <p>{{ error }}</p>
        <button @click="fetchDocument" class="btn btn-secondary">Попробовать снова</button>
      </div>
      
      <div v-else>
        <form @submit.prevent="submitForm" class="document-form">
          <div class="form-group">
            <label for="Name">Название:</label>
            <input type="text" id="Name" v-model="document.Name" required class="form-control" />
          </div>
          
          <div class="form-group">
            <label for="login">Логин:</label>
            <input type="text" id="login" v-model="document.login" required class="form-control" />
          </div>
          
          <div class="form-group">
            <label for="password">Пароль:</label>
            <input type="password" id="password" v-model="document.password" required class="form-control" />
          </div>
          
          <div class="form-group">
            <label for="number">Номер:</label>
            <input type="number" id="number" v-model="document.number" required class="form-control" />
          </div>
          
          <div class="form-group">
            <label for="requisites">Реквизиты:</label>
            <input type="number" id="requisites" v-model="document.requisites" required class="form-control" />
          </div>
          
          <div class="form-group">
            <label for="contactface">Контактное лицо:</label>
            <input type="text" id="contactface" v-model="document.contactface" required class="form-control" />
          </div>
          
          <div class="form-group">
            <label for="adress">Адрес:</label>
            <input type="text" id="adress" v-model="document.adress" required class="form-control" />
          </div>
          
          <div class="form-group">
            <label for="rental">Аренда:</label>
            <input type="number" id="rental" v-model="document.rental" required class="form-control" />
          </div>
          
          <div class="form-group">
            <label for="idclient">ID клиента:</label>
            <input type="number" id="idclient" v-model="document.idclient" required class="form-control" />
          </div>
          
          <div class="form-group">
            <label for="idRoom">ID комнаты:</label>
            <input type="number" id="idRoom" v-model="document.idRoom" required class="form-control" />
          </div>
          
          <button type="submit" class="btn btn-primary" :disabled="loading">Сохранить изменения</button>
        </form>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import {  useRouter } from 'vue-router';
  import { defineProps  } from 'vue';

const props = defineProps({
  userId: {
    type: Object,
    required: true
  }
});

  const router = useRouter();
  const documentId = props.userId;
  
  const document = ref({
    Name: '',
    login: '',
    password: '',
    number: 0,
    requisites: 0,
    contactface: '',
    adress: '',
    rental: 0,
    idclient: 0,
    idRoom: 0
  });
  
  const loading = ref(true);
  const error = ref(null);
  
  const fetchDocument = async () => {
  try {
    loading.value = true;
    error.value = null;

    const response = await fetch(`http://127.0.0.1:8000/documents/${documentId}`);
    
    if (!response.ok) {
      const errorData = await response.json(); // Получаем данные об ошибке
      throw new Error(errorData.detail || `HTTP error! Status: ${response.status}`);
    }
    
    document.value = await response.json();
  } catch (err) {
    console.error('Ошибка при получении документа:', err);
    // Проверяем, является ли ошибка объектом
    if (err instanceof Error) {
      error.value = `Ошибка: ${err.message}`;
    } else {
      error.value = `Неизвестная ошибка: ${JSON.stringify(err)}`;
    }
  } finally {
    loading.value = false;
  }
};

  const submitForm = async () => {
    try {
      loading.value = true;
      error.value = null;
  
      const response = await fetch(`http://127.0.0.1:8000/documents/${documentId}`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(document.value),
      });
      
      if (!response.ok) {
        const errorData = await response.json();
        throw new Error(errorData.detail || `HTTP error! Status: ${response.status}`);
      }
      
      // Redirect or show success message
      router.push('/documents'); // Redirect to the documents list or detail page
    } catch (err) {
      console.error('Ошибка при обновлении документа:', err);
      error.value = `Ошибка: ${err.message}`;
    } finally {
      loading.value = false;
    }
  };
  
  // Fetch the document data when the component is mounted
  onMounted(() => {
    fetchDocument();
  });
  </script>
  
  <style scoped>
  .edit-document-container {
    max-width: 600px;
    margin: 0 auto;
    padding: 20px;
  }
  
  .loading, .error {
    text-align: center;
    padding: 20px;
    margin: 20px 0;
    border-radius: 8px;
  }
  
  .error {
    color: #721c24;
    background-color: #f8d7da;
    border: 1px solid #f5c6cb;
  }
  
  .document-form {
    background-color: #f9f9f9;
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
  
  .form-control {
    width: 100%;
    padding: 8px;
    border: 1px solid #ddd;
    border-radius: 4px;
  }
  </style>