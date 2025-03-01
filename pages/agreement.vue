<template>
    <Header></Header>
    {{ documents }}
<div class="title">Все договоры</div>
<div class="agr" v-for="item in documents" @click="openRoomDetails(item.id)">
    <div class="agr_block">
        <div class="agr_block_title">Договор №{{ item.id }}</div>
        <div class="agr_block_info">
            Тороговая точка: {{ item.idRoom }}<br>
            Клиент: {{ item.idclient }}<br>
            Арендная плата: {{ item.rental }}<br>
        </div>
        <ModalsDescDoc
        :user-id="selectedUserId"
         v-if="ModalOpen"
        @close="ModalOpen = false"
        />
    </div>


</div>
 <Footer></Footer>
</template>
<script setup>
import { ref, onMounted } from 'vue';

const documents = ref([]);
const loading = ref(true);
const error = ref(null);
const ModalOpen = ref(false);
const selectedUserId = ref(null);

const fetchDocuments = async () => {
  try {
    loading.value = true;
    error.value = null;

    const response = await fetch('http://127.0.0.1:8000/Document');
    
    if (!response.ok) {
      const errorData = await response.json();
      throw new Error(errorData.detail || `HTTP error! Status: ${response.status}`);
    }
    
    documents.value = await response.json();
  } catch (err) {
    console.error('Ошибка при получении документов:', err);
    error.value = `Ошибка: ${err.message}`;
  } finally {
    loading.value = false;
  }
};

const openRoomDetails = (userId) => {
  selectedUserId.value = userId;
  ModalOpen.value = true;
};

onMounted(() => {
  fetchDocuments();
});


    const updateDocument = async (documentId, documentData) => {
  try {
    const response = await fetch(`http://127.0.0.1:8000/Document/${documentId}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(documentData),
    });
    
    if (!response.ok) {
      const errorData = await response.json();
      throw new Error(errorData.detail || `HTTP error! Status: ${response.status}`);
    }
    
    const result = await response.json();
    console.log('Документ успешно обновлен:', result);
    return result;
  } catch (error) {
    console.error('Ошибка при обновлении документа:', error);
    throw error;
  }
};
</script>