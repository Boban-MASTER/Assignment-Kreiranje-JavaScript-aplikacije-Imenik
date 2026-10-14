<script setup>
import { ref, onMounted } from "vue";

const STORAGE_KEY = "imenik";
const name = ref("");
const phone = ref("");
const contacts = ref([]);

function loadContacts() {
  const saved = localStorage.getItem(STORAGE_KEY);
  contacts.value = saved ? JSON.parse(saved) : [];
}

function saveContacts() {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(contacts.value));
}

function addContact() {
  const trimmedName = name.value.trim();
  const trimmedPhone = phone.value.trim();

  if (!trimmedName || !trimmedPhone) {
    return;
  }

  contacts.value.push({
    id: Date.now(),
    name: trimmedName,
    phone: trimmedPhone
  });
  saveContacts();
  name.value = "";
  phone.value = "";
}

function deleteContact(id) {
  contacts.value = contacts.value.filter((contact) => contact.id !== id);
  saveContacts();
}

onMounted(loadContacts);
</script>

<template>
  <h1>Imenik</h1>

  <form class="form" @submit.prevent="addContact">
    <label>
      Ime
      <input v-model="name" type="text" />
    </label>
    <label>
      Broj telefona
      <input v-model="phone" type="tel" />
    </label>
    <button class="btn-save" type="submit">Sačuvaj</button>
  </form>

  <p v-if="contacts.length === 0" class="empty">Nema sačuvanih kontakata.</p>
  <ul v-else class="list">
    <li v-for="contact in contacts" :key="contact.id">
      <span>{{ contact.name }} — {{ contact.phone }}</span>
      <button class="btn-delete" type="button" @click="deleteContact(contact.id)">
        Obriši
      </button>
    </li>
  </ul>
</template>
