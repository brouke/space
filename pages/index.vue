<template>
  <div class="banner">
    <div class="banner_inf">
      <div class="banner_text">
        Аренда помещений
        в торговом центре
      </div>
      <Button 
        placeholder="Смотреть помещения"
        :className="'btn_cee-button'"
        />
      </div>
  </div>

<div class="zag">Помещения</div>
<div class="cart">
  <div class="block">
    <div class="block_img">
    </div>
    <div class="block_info">
      <div class="block_info_title">Помещение А 70 000 Р.</div>
      <div class="block_info_desc">1 этаж, 100 кв. м.</div>
      <div class="block_info_desc">
      <div class="block_info_details">Аренда помещения на первом этаже торгового центра 
площадью 100 кв. метров с кондиционером. Идеально 
подходит для розничной торговли или офисов, с большими 
витринными окнами для привлечения клиентов. </div>
</div>
  <Button 
        placeholder="Арендовать"
        :className="'btn_buy-button'"
        />
    </div>
  </div>
</div>  
  </template>
  <style >
  @import '@/assets/scss/index.scss';
  </style>
  <script setup>
  async function fetchProducts() {
    const response = await fetch('/products');

    if (!response.ok) {
        const error = await response.json();
        console.error('Ошибка:', error.detail);
        return;
    }

    const products = await response.json();
    console.log('Товары:', products);
    displayProducts(products);
}

function displayProducts(products) {
    const productList = document.getElementById('product-list');
    productList.innerHTML = '';

    products.forEach(product => {
        const productItem = document.createElement('div');
        productItem.textContent = `Название: ${product.name}, Цена: ${product.price}`;
        productList.appendChild(productItem);
    });
}

fetchProducts();
  
  </script>
  