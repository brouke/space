<template>
    <div class="head">
        <div class="head_logo">
            <nuxt-link to="/" class="link_logo">Logo</nuxt-link>
        </div>
        <div class="head-button">
            <nuxt-link to="/Info" class="link">О нас</nuxt-link>
            <nuxt-link to="" class="link">
                <Button 
                    placeholder="Вход"
                    :className="'btn_auth-button'"
                    />
            </nuxt-link>
        </div>
  </div>
</template>