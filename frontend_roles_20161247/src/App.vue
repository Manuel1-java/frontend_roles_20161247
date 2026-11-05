<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const urlBase = 'http://localhost:3000/api';

const roles = ref([]);
const usuarios = ref([]);
const empleados = ref([]);

const formRol = ref({ id: null, name: '', description: '' });
const formUsuario = ref({ id: null, username: '', email: '', password: '', role_id: '' });
const formEmpleado = ref({ id: null, user_id: '', first_name: '', last_name: '', job_title: '' });

const cargarDatos = async () => {
  try {
    const [resR, resU, resE] = await Promise.all([
      axios.get(`${urlBase}/v2/roles`),
      axios.get(`${urlBase}/v2/usuarios`),
      axios.get(`${urlBase}/v2/employees`)
    ]);
    roles.value = resR.data.data || resR.data;
    usuarios.value = resU.data.data || resU.data;
    empleados.value = resE.data.data || resE.data;
  } catch (error) {
    console.error("Error al cargar los datos:", error);
  }
};

const guardarRol = async () => {
  try {
    const payload = { 
      name: formRol.value.name, 
      description: formRol.value.description || null 
    };
    if (formRol.value.id) await axios.put(`${urlBase}/v2/roles/${formRol.value.id}`, payload);
    else await axios.post(`${urlBase}/v2/roles`, payload);
    
    formRol.value = { id: null, name: '', description: '' };
    cargarDatos();
  } catch (error) { console.error("Error Roles:", error.response?.data); alert("Error al guardar rol"); }
};
const eliminarRol = async (id) => { await axios.delete(`${urlBase}/v2/roles/${id}`); cargarDatos(); };
const editarRol = (item) => { formRol.value = { ...item }; };

const guardarUsuario = async () => {
  try {
    const payload = { 
      username: formUsuario.value.username, 
      email: formUsuario.value.email,
      role_id: Number(formUsuario.value.role_id)
    };
    
    if (formUsuario.value.password) {
      payload.password = String(formUsuario.value.password);
    }
    
    if (formUsuario.value.id) await axios.put(`${urlBase}/v2/usuarios/${formUsuario.value.id}`, payload);
    else await axios.post(`${urlBase}/v2/usuarios`, payload);
    
    formUsuario.value = { id: null, username: '', email: '', password: '', role_id: '' };
    cargarDatos();
  } catch (error) { 
    console.error("Error Usuarios:", error.response?.data); 
    alert(error.response?.data?.message || "Error al guardar usuario"); 
  }
};
const eliminarUsuario = async (id) => { await axios.delete(`${urlBase}/v2/usuarios/${id}`); cargarDatos(); };
const editarUsuario = (item) => { formUsuario.value = { ...item, password: '' }; };

const guardarEmpleado = async () => {
  try {
    const payload = { 
      user_id: Number(formEmpleado.value.user_id),
      first_name: formEmpleado.value.first_name,
      last_name: formEmpleado.value.last_name
    };
    
    if (formEmpleado.value.job_title && formEmpleado.value.job_title.trim() !== '') {
      payload.job_title = formEmpleado.value.job_title;
    }
    
    if (formEmpleado.value.id) await axios.put(`${urlBase}/v2/employees/${formEmpleado.value.id}`, payload);
    else await axios.post(`${urlBase}/v2/employees`, payload);
    
    formEmpleado.value = { id: null, user_id: '', first_name: '', last_name: '', job_title: '' };
    cargarDatos();
  } catch (error) { 
    console.error("Error Empleados:", error.response?.data); 
    alert(error.response?.data?.message || "Error al guardar empleado"); 
  }
};
const eliminarEmpleado = async (id) => { await axios.delete(`${urlBase}/v2/employees/${id}`); cargarDatos(); };
const editarEmpleado = (item) => { formEmpleado.value = { ...item }; };

onMounted(cargarDatos);
</script>

<template>
  <div style="padding: 20px; font-family: Arial, sans-serif;">
    <h1>Roles, Usuarios y Empleados</h1>

    <section style="margin-bottom: 40px;">
      <h2>Roles</h2>
      <form @submit.prevent="guardarRol" style="margin-bottom: 10px; background: #f9f9f9; padding: 10px; border: 1px solid #ddd;">
        <input v-model="formRol.name" placeholder="Nombre" required style="margin-right: 5px;" />
        <input v-model="formRol.description" placeholder="Descripción" style="margin-right: 5px;" />
        <button type="submit">{{ formRol.id ? 'Guardar Cambios' : 'Crear Rol' }}</button>
        <button v-if="formRol.id" @click="formRol = { id: null, name: '', description: '' }" type="button">Cancelar</button>
      </form>
      <table border="1" cellpadding="8" style="border-collapse: collapse; width: 100%;">
        <tr style="background-color: #eee;"><th>ID</th><th>Nombre</th><th>Descripción</th><th>Acciones</th></tr>
        <tr v-for="rol in roles" :key="rol.id">
          <td>{{ rol.id }}</td><td>{{ rol.name }}</td><td>{{ rol.description }}</td>
          <td>
            <button @click="editarRol(rol)" style="margin-right: 5px;">Editar</button>
            <button @click="eliminarRol(rol.id)">Eliminar</button>
          </td>
        </tr>
      </table>
    </section>

    <hr>

    <section style="margin-bottom: 40px;">
      <h2>Usuarios</h2>
      <form @submit.prevent="guardarUsuario" style="margin-bottom: 10px; background: #f9f9f9; padding: 10px; border: 1px solid #ddd;">
        <input v-model="formUsuario.username" placeholder="Username" required style="margin-right: 5px;" />
        <input v-model="formUsuario.email" placeholder="Correo" type="email" required style="margin-right: 5px;" />
        <input v-model="formUsuario.password" placeholder="Contraseña" type="password" :required="!formUsuario.id" style="margin-right: 5px;" />
        <input v-model="formUsuario.role_id" placeholder="ID de Rol" type="number" required style="margin-right: 5px; width: 120px;" />
        <button type="submit">{{ formUsuario.id ? 'Guardar Cambios' : 'Crear Usuario' }}</button>
        <button v-if="formUsuario.id" @click="formUsuario = { id: null, username: '', email: '', password: '', role_id: '' }" type="button">Cancelar</button>
      </form>
      <table border="1" cellpadding="8" style="border-collapse: collapse; width: 100%;">
        <tr style="background-color: #eee;"><th>ID</th><th>Username</th><th>Email</th><th>Rol ID</th><th>Acciones</th></tr>
        <tr v-for="user in usuarios" :key="user.id">
          <td>{{ user.id }}</td><td>{{ user.username }}</td><td>{{ user.email }}</td><td>{{ user.role_id }}</td>
          <td>
            <button @click="editarUsuario(user)" style="margin-right: 5px;">Editar</button>
            <button @click="eliminarUsuario(user.id)">Eliminar</button>
          </td>
        </tr>
      </table>
    </section>

    <hr>

    <section>
      <h2>Empleados</h2>
      <form @submit.prevent="guardarEmpleado" style="margin-bottom: 10px; background: #f9f9f9; padding: 10px; border: 1px solid #ddd;">
        <input v-model="formEmpleado.user_id" placeholder="ID del Usuario" type="number" required style="margin-right: 5px; width: 120px;" />
        <input v-model="formEmpleado.first_name" placeholder="Nombre" required style="margin-right: 5px;" />
        <input v-model="formEmpleado.last_name" placeholder="Apellido" required style="margin-right: 5px;" />
        <input v-model="formEmpleado.job_title" placeholder="Puesto" style="margin-right: 5px;" />
        <button type="submit">{{ formEmpleado.id ? 'Guardar Cambios' : 'Crear Empleado' }}</button>
        <button v-if="formEmpleado.id" @click="formEmpleado = { id: null, user_id: '', first_name: '', last_name: '', job_title: '' }" type="button">Cancelar</button>
      </form>
      <table border="1" cellpadding="8" style="border-collapse: collapse; width: 100%;">
        <tr style="background-color: #eee;"><th>ID</th><th>User ID</th><th>Nombre Completo</th><th>Puesto</th><th>Acciones</th></tr>
        <tr v-for="emp in empleados" :key="emp.id">
          <td>{{ emp.id }}</td><td>{{ emp.user_id }}</td><td>{{ emp.first_name }} {{ emp.last_name }}</td><td>{{ emp.job_title }}</td>
          <td>
            <button @click="editarEmpleado(emp)" style="margin-right: 5px;">Editar</button>
            <button @click="eliminarEmpleado(emp.id)">Eliminar</button>
          </td>
        </tr>
      </table>
    </section>

  </div>
</template>