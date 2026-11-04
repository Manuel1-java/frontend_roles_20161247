<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const roles = ref([]);
const errorMensaje = ref('');

const obtenerRoles = async () => {
  try {
    const respuesta = await axios.get('http://localhost:3000/api/v2/roles');
    roles.value = respuesta.data.data;
    errorMensaje.value = '';
  } catch (error) {
    errorMensaje.value = 'Error al conectar con el backend.';
    console.error(error);
  }
};

onMounted(() => {
  obtenerRoles();
});
</script>

<template>
  <main style="padding: 2rem; font-family: sans-serif;">
    <h1>Roles</h1>
    
    <p v-if="errorMensaje" style="color: red;">{{ errorMensaje }}</p>
    
    <ul v-if="roles.length > 0">
      <li v-for="rol in roles" :key="rol.id">
        <strong>{{ rol.name }}</strong>: {{ rol.description }}
      </li>
    </ul>
    <p v-else-if="!errorMensaje">Cargando roles...</p>
  </main>
</template>