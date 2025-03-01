<template>
     <Header></Header>
     {{ documentInfo }}
     {{ userInfo }}
     <button @click="fetchUserInfo">Загрузить информацию о пользователе</button>
     <div class="prof">
        <div class="prof_block">
        <div class="prof_block_title">
            Здравствуйте, {{userInfo.Name}}!
        </div>
        <div class="prof_block_info">
            <!-- Сделай вывод если заполнены, а если нет, чтоб предложили заполнить -->
            <div class="prof_block_info_title"> 
                Ваши данные
            </div>
            <div class="prof_block_info_block">
                <div class="prof_block_info-desc">Имя: {{ userInfo.Name }}</div>
                <div class="prof_block_info-desc">Фамилия: {{ userInfo.surname }}</div>
                <div class="prof_block_info-desc">Логин: {{ userInfo.login }}</div>
                <div class="prof_block_info-desc">Пароль: {{ userInfo.password }}</div>
                <div class="prof_block_info-desc">Адрес: {{ userInfo.adress }}</div>
                <div class="prof_block_info-desc">Телефон: {{ userInfo.number }}</div>
                <div class="prof_block_info-desc">Реквизиты: {{ userInfo.requisites }}</div>
                <div class="prof_block_info-desc">Контактное лицо: {{ userInfo.contactface }}</div>
            </div>
            <div class="prof_block_info_i">
                <Button 
                @click="ModalOpen = true"
                    placeholder="Изменить"
                    :className="'btn_put-button'"
                    />
                    <ModalsEditStaff v-if="ModalOpen"
                    @close="ModalOpen = false"  
                    @update ="refresh()"
            />
            </div>
        </div>
        <div class="prof_block_dok">
            <div class="prof_block_dok_title">
                Ваши текущие договоры
            </div>
            <div class="prof_block_dok_block">
                <table>
                    <thead>
                    <tr>
                        <th>Номер договора</th>
                        <th>Торговая точка</th>
                        <th>Имя клиента</th>
                        <th>Номер</th>
                        <th>Контактное лицо</th>
                        <th>Реквизиты</th>
                        <th>Адрес</th>
                        <th>Стоимость</th>
                    </tr>
                    </thead>
                    <tbody>
                        <tr v-for="item in documentInfo">
                            <th scope="row">{{ item.id }}</th>
                            <th scope="row">{{ item.idRoom }}</th>
                            <th scope="row">{{ item.Name }}</th>
                            <th scope="row">{{ item.number }}</th>
                            <th scope="row">{{ item.contactface }}</th>
                            <th scope="row">{{ item.requisites }}</th>
                            <th scope="row">{{ item.adress }}</th>
                            <th scope="row">{{ item.rental }}</th>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
     </div>
     </div>
     <Footer></Footer>
</template>
<script setup>
import { ref, onMounted } from 'vue';
import Cookies from 'js-cookie';

const userId = ref(null);
const documentInfo = ref([])
const userInfo = ref([]);
const error = ref(null);
const loading = ref(false);
const ModalOpen = ref(false);

// Функция для получения ID пользователя из куки
const getUserId = () => {
  const id = Cookies.get('user_id');
  if (id) {
    userId.value = id;
    return true;
  }
  return false;
};

// Функция для получения информации о пользователе с сервера
const fetchUserInfo = async () => {
  if (!userId.value) {
    error.value = 'Пользователь не авторизован';
    return;
  }

  loading.value = true;
  try {
    const token = Cookies.get('access_token');
    const response = await fetch(`http://127.0.0.1:8000/user/${userId.value}`, {
      headers: {
        Authorization: `Bearer ${token}`,
      },
    });

    if (!response.ok) {
      throw new Error('Ошибка сети');
    }

    const data = await response.json();
    userInfo.value = data;
  } catch (err) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
};
const fetchUserDocuments = async () => {
  try {
    
    if (!userId.value) {
      console.error('ID пользователя не найден');
      return;
    }
    
    // Используем fetch вместо axios
    const response = await fetch(`http://127.0.0.1:8000/documents/user/${userId.value}`);
    
    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }
    
    const data = await response.json();
    documentInfo.value = data
    // Обработка ответа
    // ...
  } catch (err) {
    console.error('Ошибка при получении договоров:', err);
  }
};
// Вызываем функцию при монтировании компонента
onMounted(() => {
  if (getUserId()) {
    fetchUserInfo();
    fetchUserDocuments(); // Добавлен вызов функции для получения договоров пользователя
  }
});
</script>