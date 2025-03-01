<template>
    <div class="head">
        <div class="head_logo">
            <nuxt-link to="/" class="link_logo">Avenue</nuxt-link>
        </div>
        <div class="head-button">
            <nuxt-link to="/agreement" class="link">Договоры</nuxt-link>
            <nuxt-link to="/users" class="link">Пользователи</nuxt-link>
            <nuxt-link to="/admin" class="link">Помещения</nuxt-link>
            <nuxt-link to="/Info" class="link">О нас</nuxt-link>
            <nuxt-link to="/account" class="link" >Профиль</nuxt-link>
            <nuxt-link to="/auth" class="link" >
                <Button 
                    placeholder="Вход"
                    :className="'btn_auth-button'"
                    />
            </nuxt-link>
        </div>
  </div>
</template>
<script setup>
import { ref } from 'vue';
import Cookies from 'js-cookie';

const hasToken = ref(false);
const error = ref(null);

// Проверяем наличие access token в куки
const checkToken = () => {
  const token = Cookies.get('access_token');// Получаем токен из куки
  hasToken.value = !!token; // Устанавливаем значение в true, если токен существует
};

checkToken(); 

</script>