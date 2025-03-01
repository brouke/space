<template>
    <div class="head">
        <div class="head_logo">
            <nuxt-link to="/" class="link_logo">Avenue</nuxt-link>
        </div>
        <div class="head-button">
            <nuxt-link to="/agreement" class="link" v-if="userId == 1">Договоры</nuxt-link>
            <nuxt-link to="/users" class="link" v-if="userId == 1">Пользователи</nuxt-link>
            <nuxt-link to="/admin" class="link" v-if="userId == 1">Помещения</nuxt-link>
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
const userId = ref(null);
const error = ref(null);

// Проверяем наличие access token в куки
const checkToken = () => {
  const token = Cookies.get('access_token');// Получаем токен из куки
  hasToken.value = !!token; // Устанавливаем значение в true, если токен существует
};

const getUserId = () => {
  const id = Cookies.get('user_id');
  if (id) {
    userId.value = id;
    return true;
  }
}
checkToken();
getUserId() 

</script>