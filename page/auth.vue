<template>
    <div class="head">
    <div class="head_logo">
     <a href="/"><h1>Logo</h1></a>
     <div class="head_menu">
      <a href="Info.vue"><h1>О Нас</h1></a>
      <a href="page/auth.vue"><button class="head_menu_but"><h1>Войти</h1></button></a>
     </div>
    </div>
    </div>

    <div class="auth">
        <h2>Авторизация</h2>
        <div class="auth_form">
            <form action="">
                <input type="text" placeholder="Login" name="login">
                <input type="password" name="pass" placeholder="Password">
                <input type="submit" value="Войти">
            </form>
        </div>
    </div>




    <div class="foot">
    <div class="foot_blok1">
      <a href=""><h3>О нас</h3></a>
      <a href=""><h3>Помещения</h3></a>
      <h3>Адрес: г. Ижевск Пушкинская д. 125</h3>
    </div>
    <div class="foot_blok2">
      <h3>Телефоны:</h3>
      <h3>8 (900) 000-88-99</h3>
      <h3>8 (901) 000-99-99</h3>
    </div>
  </div>
</template>