<template>
  <div class="container">
    <h2>Crear Nuevo Usuario</h2>
    <form @submit.prevent="submitForm">
      <div class="form-group">
        <label for="name">Nombre</label>
        <input type="text" id="name" v-model="form.name" class="form-control" required />
        <span v-if="errors.name" class="text-danger">{{ errors.name }}</span>
      </div>

      <div class="form-group">
        <label for="email">Correo Electrónico</label>
        <input type="email" id="email" v-model="form.email" class="form-control" required />
        <span v-if="errors.email" class="text-danger">{{ errors.email }}</span>
      </div>

      <div class="form-group">
        <label for="password">Contraseña</label>
        <input
          type="password"
          id="password"
          v-model="form.password"
          class="form-control"
          required
        />
        <span v-if="errors.password" class="text-danger">{{ errors.password }}</span>
      </div>

      <div class="form-group">
        <label for="password_confirmation">Confirmar Contraseña</label>
        <input
          type="password"
          id="password_confirmation"
          v-model="form.password_confirmation"
          class="form-control"
          required
        />
      </div>

      <button type="submit" class="btn btn-primary">Crear Usuario</button>
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'

const form = ref({
  name: '',
  email: '',
  password: '',
  password_confirmation: '',
})

const errors = ref({})
const message = ref('')
const messageClass = ref('')

const submitForm = async () => {
  try {
    const response = await axios.post('/api/users', form.value)

    form.value = {
      name: '',
      email: '',
      password: '',
      password_confirmation: '',
    }
    message.value = response.data.message
    messageClass.value = 'text-success'
  } catch (error) {
    if (error.response && error.response.data) {
      errors.value = error.response.data.errors || {}
      message.value = error.response.data.message || 'Error al crear el usuario.'
      messageClass.value = 'text-danger'
    }
  }
}
</script>
