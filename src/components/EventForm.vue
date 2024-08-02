<!-- src/components/EventForm.vue -->
<template>
    <div class="event-form">
      <h2 class="form-title">Agregar Evento</h2>
      <form @submit.prevent="submitForm">
        <div class="form-group">
          <label for="title">Título:</label>
          <input type="text" v-model="title" id="title" required class="form-control" />
        </div>
        <div class="form-group">
          <label for="comment">Comentario:</label>
          <input type="text" v-model="comment" id="comment" required class="form-control" />
        </div>
        <div class="form-actions">
          <button type="submit" class="submit-button">Agregar</button>
          <button type="button" @click="$emit('cancel')" class="cancel-button">Cancelar</button>
        </div>
      </form>
    </div>
  </template>

  <script>
  export default {
    props: ['day'],
    data() {
      return {
        title: '',
        comment: ''
      };
    },
    methods: {
      submitForm() {
        this.$emit('add-event', { id: Date.now(), title: this.title, comment: this.comment });
        this.title = '';
        this.comment = '';
      }
    }
  };
  </script>

  <style>
  .event-form {
    background-color: white;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
    margin-top: 20px;
  }

  .form-title {
    margin-bottom: 20px;
    font-size: 1.5em;
    text-align: center;
  }

  .form-group {
    margin-bottom: 15px;
  }

  .form-group label {
    display: block;
    margin-bottom: 5px;
  }

  .form-control {
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
  }

  .form-actions {
    display: flex;
    justify-content: space-between;
  }

  .submit-button, .cancel-button {
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }

  .submit-button {
    background-color: #28a745;
    color: white;
  }

  .submit-button:hover {
    background-color: #218838;
  }

  .cancel-button {
    background-color: #dc3545;
    color: white;
  }

  .cancel-button:hover {
    background-color: #c82333;
  }
  </style>


