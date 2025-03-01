<template>
    <div class="authorise">
        <div class="authorise_window">
            <div class="authorise_window_name">Авторизация</div>
            <div class="authorise_window-input">
                <Input 
                id="username"
                    placeholder="Логин"
                    inputclass="auth"
                    v-model="login"
                />
                <Input 
                    placeholder="Пароль"
                    inputclass="auth"
                    v-model="password"
                    id="password"
                />
            </div>
            <div class="authorise_window-button">
                <Button 
                    placeholder="Войти"
                    :className="'btn_authorise-button'"
                    @click="auth()"
                    />
                <div class="authorise_window-text">Нет аккаунта? <div class="authorise_window-reg"@click="ModalOpen = true">Зарегистрируйтесь!</div></div>
            </div>
            <div class="app__body">
                <ModalsAddStaff v-if="ModalOpen"
                @close="ModalOpen = false"  
                @update ="refresh()"
            />
            </div>
        </div>
    </div>
    <Footer></Footer>
</template>
<script setup>
import { ref } from 'vue';
import Cookies from 'js-cookie';

const login = ref('');
const password = ref('');
const error = ref(null);
const ModalOpen = ref(false);

// Функция для отправки логина и пароля
const auth = async () => {
  try {
    const response = await fetch('http://127.0.0.1:8000/login', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ username: login.value, password: password.value }),
    });

    if (!response.ok) {
      throw new Error('Ошибка сети или неверные учетные данные');
    }

    const data = await response.json();
    // Сохраняем access token в куки
    Cookies.set('access_token', data.access_token, { expires: 7 });
    Cookies.set('user_id', data.user_id, { expires: 7 }); // Кука будет действительна 7 дней
    // Здесь вы можете добавить логику для перенаправления пользователя или обновления состояния
  } catch (err) {
    error.value = err.message; // Сохраняем сообщение об ошибке
  }
};
</script>