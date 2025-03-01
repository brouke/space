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
      <div class="modal__form">
        <div class="modal__form-name">Регистрация</div>
        <div class="modal__form-input">
          <Input 
             placeholder="Имя"
             inputclass="reg"
             v-model="name"
             id="name"
         />
         <Input 
             placeholder="Фамилия"
             inputclass="reg"
             v-model="surname"
             id="surname"
         />
         <Input 
             placeholder="Логин"
             inputclass="reg"
             v-model="login"
             id="login"
         />
         <Input 
             placeholder="Пароль"
             inputclass="reg"
             v-model="password"
             id="password"
         />
        </div>
        <div class="modal__form-button">
          <Button 
            placeholder="Зарегистрироваться"
            :className="'btn_reg-button'"
            @click="register()"
            />
        </div>
    </div>
    </div>
  </div>
</template>
<script setup>
import { ref } from 'vue';

const name = ref('');
const surname = ref('');
const login = ref('');
const password = ref('');
const error = ref(null);


const register = async () => {
  try {
    const response = await fetch('http://127.0.0.1:8000/User', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        Name: name.value, // Исправлено с Тame на Name
        surname: surname.value,
        login: login.value,
        password: password.value,
      }),
    });

    if (!response.ok) {
      const errorData = await response.json().catch(() => ({}));
      console.error('Ошибка сервера:', errorData);
      throw new Error(`Ошибка регистрации: ${JSON.stringify(errorData)}`);
    }
    
    // Обработка успешного ответа
    const data = await response.json();
    console.log('Успешная регистрация:', data);
    
    // Здесь можно добавить код для закрытия модального окна или перенаправления
    
  } catch (err) {
    error.value = err.message;
    console.error('Ошибка при регистрации:', err);
  }
};
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

</script>
