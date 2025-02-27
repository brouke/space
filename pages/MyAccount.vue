<template>
  <Header></Header>
    <div id="app">
      <header>
        <nav>
          <nuxt-link to="/">Главная</nuxt-link>
          <nuxt-link to="/info">О Нас</nuxt-link>
        </nav>
      </header>
      <nuxt-child /> <!-- Здесь будут отображаться компоненты страниц -->
    </div>
  </template>
  
  <script setup>
  </script>
  
  <style>
  /* Глобальные стили */
  </style>