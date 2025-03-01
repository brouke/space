<template>
  <div
    class="modal"
    ref="email"
    @click.self="$emit('close')"
    @keydown.esc="$emit('close')"
  >
    <div class="modal__info">
      <div class="modal__close">
        <ButtonClose @click="$emit('close')" />
      </div>
  <div class="modal_edit">
    <h1>Редактирование профиля</h1>
    <div v-if="loading" class="loading">Загрузка данных...</div>
    <form v-else @submit.prevent="updateProfile" class="form_edit">
      <div class="form-group">
        <label for="name">Имя:</label>
        <input type="text" id="name" v-model="userData.Name" required />
      </div>
      
      <div class="form-group">
        <label for="surname">Фамилия:</label>
        <input type="text" id="surname" v-model="userData.surname" required />
      </div>
      
      <div class="form-group">
        <label for="login">Логин:</label>
        <input type="text" id="login" v-model="userData.login" required />
      </div>
      
      <div class="form-group">
        <label for="password">Пароль:</label>
        <input type="password" id="password" v-model="userData.password"/>
      </div>
      
      <div class="form-group">
        <label for="address">Адрес:</label>
        <input type="text" id="address" v-model="userData.adress" />
      </div>
      
      <div class="form-group">
        <label for="phone">Телефон:</label>
        <input type="tel" id="phone" v-model="userData.number" />
      </div>
      
      <div class="form-group">
        <label for="requisites">Реквизиты:</label>
        <textarea id="requisites" v-model="userData.requisites" rows="3"></textarea>
      </div>
      
      <div class="form-group">
        <label for="contact_person">Контактное лицо:</label>
        <input type="text" id="contact_person" v-model="userData.contactface" />
      </div>
      
      <div class="form-actions">
        <button type="submit" :disabled="submitting">Сохранить изменения</button>
      </div>
    </form>
    
    <div v-if="error" class="error">{{ error }}</div>
    <div v-if="success" class="success">{{ success }}</div>
  </div>
  </div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted } from 'vue';
import Cookies from 'js-cookie';

const emit = defineEmits(["close", "update"]);
const handleKeydown = (event) => {
  if (event.key === 'Escape') {
    emit('close');
  }
};

onMounted(() => {
  window.addEventListener('keydown', handleKeydown);
});

onBeforeUnmount(() => {
  window.removeEventListener('keydown', handleKeydown);
});

// Состояние формы
const userData = reactive({
  Name: '',
  surname: '',
  login: '',
  password: '',
  adress: '',
  number: '',
  requisites: '',
  contactface: ''
});

const originalUserData = ref(null); // Для хранения исходных данных
const loading = ref(true);
const submitting = ref(false);
const error = ref(null);
const success = ref(null);

// Получение данных пользователя при загрузке компонента
const fetchUserData = async () => {
  loading.value = true;
  error.value = null;
  
  try {
    const userId = Cookies.get('user_id');
    const token = Cookies.get('access_token');
    
    if (!userId || !token) {
      throw new Error('Пользователь не авторизован');
    }
    
    const response = await fetch(`http://127.0.0.1:8000/user/${userId}`, {
      headers: {
        Authorization: `Bearer ${token}`
      }
    });
    
    if (!response.ok) {
      throw new Error('Не удалось получить данные пользователя');
    }
    
    const data = await response.json();
    console.log('Данные с сервера:', data);
    
    // Явно заполняем каждое поле
    userData.Name = data.Name || '';
    userData.surname = data.surname || '';
    userData.login = data.login || '';
    userData.password = ''; // Пароль обычно не возвращается с сервера
    userData.adress = data.adress || '';
    userData.number = data.number || '';
    userData.requisites = data.requisites || '';
    userData.contactface = data.contactface || '';
    
    // Проверяем, какие поля были заполнены
    console.log('Заполненные данные:', userData);
    
    // Сохраняем копию исходных данных для возможности отмены изменений
    originalUserData.value = { ...userData };
    
  } catch (err) {
    error.value = err.message;
    console.error('Ошибка при получении данных:', err);
  } finally {
    loading.value = false;
  }
};

// Отправка обновленных данных на сервер
// Отправка обновленных данных на сервер
const updateProfile = async () => {
  submitting.value = true;
  error.value = null;
  success.value = null;
  
  try {
    const userId = Cookies.get('user_id');
    const token = Cookies.get('access_token');
    
    if (!userId || !token) {
      throw new Error('Пользователь не авторизован');
    }
    
    // Создаем объект с данными для отправки
    const dataToSend = { ...userData };
    
    // Если пароль не был изменен, удаляем его из отправляемых данных
    if (!dataToSend.password) {
      delete dataToSend.password;
    }
    
    // Проверяем обязательные поля
    if (!dataToSend.Name || !dataToSend.surname || !dataToSend.login) {
      throw new Error('Имя, фамилия и логин обязательны для заполнения');
    }
    
    console.log('Отправляемые данные:', JSON.stringify(dataToSend, null, 2));
    
    const response = await fetch(`http://127.0.0.1:8000/user/${userId}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
        Authorization: `Bearer ${token}`
      },
      body: JSON.stringify(dataToSend)
    });
    
    if (!response.ok) {
      const errorData = await response.json().catch(() => ({}));
      console.error('Ошибка сервера:', errorData);
      throw new Error(`Не удалось обновить данные пользователя: ${JSON.stringify(errorData)}`);
    }
    
    const updatedData = await response.json();
    console.log('Обновленные данные:', updatedData);
    
    // Обновляем исходные данные
    originalUserData.value = { ...userData };
    
    success.value = 'Данные успешно обновлены';
    
  } catch (err) {
    error.value = err.message;
    console.error('Ошибка при обновлении данных:', err);
  } finally {
    submitting.value = false;
  }
};

// Сброс формы к исходным значениям
const resetForm = () => {
  if (originalUserData.value) {
    Object.keys(userData).forEach(key => {
      userData[key] = originalUserData.value[key] || '';
    });
  }
  error.value = null;
  success.value = null;
};
// Загружаем данные пользователя при монтировании компонента
onMounted(fetchUserData);
</script>

<style scoped>
.form_edit{
  display: flex;
    width: 100%;
    flex-direction: column;
    align-items: center;
    padding-bottom: 15px;
}
.modal_edit{
  display: flex;
    flex-direction: column;
    align-items: center;
}
.form-group {
  display: flex;
    width: 100%;
    flex-direction: column;
    align-items: center;
  margin-bottom: 15px;
  width: 100%;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

input, textarea {
  width: 60%;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.form-actions {
  margin-top: 20px;

}

button {
  padding: 10px 15px;
  background-color: #60617F;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

button[type="button"] {
  background-color: #f44336;
}

.error {
  color: red;
  margin-top: 15px;
}

.success {
  color: green;
  margin-top: 15px;
}

.loading {
  margin: 20px 0;
  font-style: italic;
}
</style>