<!-- components/modals/UserDetailsModal.vue -->
<template>
    <div v-if="isOpen" class="modal-overlay" @click.self="closeModal">
      <div class="modal-content">
        <div class="modal-header">
          <h2>Детали пользователя</h2>
          <button class="close-button" @click="closeModal">&times;</button>
        </div>
        
        <div v-if="loading" class="modal-body loading">
          Загрузка данных...
        </div>
        <div v-else-if="error" class="modal-body error">
          {{ error }}
        </div>
        <div v-else-if="userData" class="modal-body">
          <div class="user-info">
            <div class="info-item">
              <span class="label">ID:</span>
              <span class="value">{{ userData.id }}</span>
            </div>
            <div class="info-item">
              <span class="label">Имя:</span>
              <span class="value">{{ userData.Name }}</span>
            </div>
            <div class="info-item">
              <span class="label">Фамилия:</span>
              <span class="value">{{ userData.surname }}</span>
            </div>
            <div class="info-item">
              <span class="label">Логин:</span>
              <span class="value">{{ userData.login }}</span>
            </div>
            <div class="info-item">
              <span class="label">Адрес:</span>
              <span class="value">{{ userData.adress }}</span>
            </div>
            <div class="info-item">
              <span class="label">Телефон:</span>
              <span class="value">{{ userData.number }}</span>
            </div>
            <div class="info-item">
              <span class="label">Контактное лицо:</span>
              <span class="value">{{ userData.contactface }}</span>
            </div>
            <div class="info-item">
              <span class="label">Реквизиты:</span>
              <span class="value">{{ userData.requisites }}</span>
            </div>
          </div>
        </div>
        
        <div class="modal-footer">
          <button @click="deleteUser" class="delete-button">Удалить</button>
          <button @click="closeModal" class="cancel-button">Закрыть</button>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, watch } from 'vue';
  import Cookies from 'js-cookie';
  
  const props = defineProps({
    isOpen: {
      type: Boolean,
      default: false
    },
    userId: {
      type: Object,
      default: null
    }
  });
  
  const emit = defineEmits(['close', 'edit', 'delete']);
  
  const userData = ref(null);
  const loading = ref(false);
  const error = ref(null);
  
  // Функция для получения данных пользователя
  const fetchUserData = async () => {
    if (!props.userId) return;
    
    loading.value = true;
    error.value = null;
    
    try {
      const token = Cookies.get('access_token');
      
      if (!token) {
        throw new Error('Пользователь не авторизован');
      }
      
      const response = await fetch(`http://127.0.0.1:8000/user/${props.userId}`, {
        headers: {
          Authorization: `Bearer ${token}`
        }
      });
      
      if (!response.ok) {
        throw new Error('Не удалось получить данные пользователя');
      }
      
      const data = await response.json();
      console.log('Данные пользователя:', data);
      
      userData.value = data;
      
    } catch (err) {
      error.value = err.message;
      console.error('Ошибка при получении данных пользователя:', err);
    } finally {
      loading.value = false;
    }
  };
  
  // Функция для закрытия модального окна
  const closeModal = () => {
    emit('close');
  };
  
  // Функция для редактирования пользователя
  const editUser = () => {
    emit('edit', props.userId);
  };
  
  // Функция для удаления пользователя
  const deleteUser = () => {
    if (confirm('Вы уверены, что хотите удалить этого пользователя?')) {
      emit('delete', props.userId);
    }
  };
  
  // Следим за изменением userId и isOpen
  watch(() => [props.userId, props.isOpen], ([newUserId, newIsOpen]) => {
    if (newUserId && newIsOpen) {
      fetchUserData();
    }
  }, { immediate: true });
  </script>
  
  <style scoped>
  .modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 1000;
  }
  
  .modal-content {
    background-color: white;
    border-radius: 8px;
    width: 90%;
    max-width: 600px;
    max-height: 90vh;
    overflow-y: auto;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
  }
  
  .modal-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 15px 20px;
    border-bottom: 1px solid #eee;
  }
  
  .modal-header h2 {
    margin: 0;
    font-size: 20px;
  }
  
  .close-button {
    background: none;
    border: none;
    font-size: 24px;
    cursor: pointer;
    color: #666;
  }
  
  .modal-body {
    padding: 20px;
  }
  
  .modal-body.loading {
    text-align: center;
    font-style: italic;
  }
  
  .modal-body.error {
    color: red;
  }
  
  .modal-footer {
    padding: 15px 20px;
    border-top: 1px solid #eee;
    display: flex;
    justify-content: flex-end;
    gap: 10px;
  }
  
  .user-info {
    display: grid;
    grid-template-columns: 1fr;
    gap: 10px;
  }
  
  .info-item {
    display: flex;
    margin-bottom: 8px;
  }
  
  .label {
    font-weight: bold;
    width: 150px;
    flex-shrink: 0;
  }
  
  .value {
    flex-grow: 1;
  }
  
  button {
    padding: 8px 15px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }
  
  .edit-button {
    background-color: #4CAF50;
    color: white;
  }
  
  .delete-button {
    background-color: #f44336;
    color: white;
  }
  
  .cancel-button {
    background-color: #ccc;
    color: #333;
  }
  </style>