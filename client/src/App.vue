<script setup>
import { ref, onMounted } from 'vue'
const API = 'http://localhost:4000/api/items'
const items = ref([])
const form = ref({ name: '', description: '', price: '' })
const editId = ref(null)

async function load() {
  items.value = await fetch(API).then(r => r.json())
}

async function save() {
  if (editId.value) {
    await fetch(`${API}/${editId.value}`, {
    method: 'PUT', headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(form.value) })
    editId.value = null
  } else {
    await fetch(API, { method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(form.value) })
  }
  form.value = { name: '', description: '', price: '' }; load()
}

function startEdit(item) {
  editId.value = item.id
  form.value = { name: item.name, description: item.description, price: item.price }
}

async function remove(id) {
  await fetch(`${API}/${id}`, { method: 'DELETE' }); load()
}

onMounted(load)
</script>

<template>
  <main>
    <h1>Items</h1>
    <form @submit.prevent="save">
      <input v-model="form.name" placeholder="Name" required />
      <input v-model="form.description" placeholder="Description" />
      <input v-model.number="form.price" type="number" step="0.01" placeholder="Price" required />
      <button type="submit">{{ editId ? 'Update' : 'Add' }}</button>
    </form>
    <ul>
      <li v-for="item in items" :key="item.id">
        <strong>{{ item.name }}</strong> — {{ item.description }}
        <span class="price-tag"> — ₱{{ item.price }}</span>

        <button @click="startEdit(item)">Edit</button>
        <button @click="remove(item.id)">Delete</button>
      </li>
    </ul>
  </main>
</template> 

<style scoped>
/* Pwede niyo itong baguhin mamaya para sa sarili ninyong styling requirement */
.price-tag {
  font-weight: bold;
  color: #2e7d32;
  margin-right: 10px;
}
input {
  margin-right: 5px;
  padding: 5px;
}
button {
  margin-right: 5px;
  cursor: pointer;
}
</style>