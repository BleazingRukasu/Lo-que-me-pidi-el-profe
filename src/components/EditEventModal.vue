<!-- src/components/EditEventModal.vue -->
<template>
  <div class="modal-overlay">
    <div class="modal-content">
      <h2>Editar Evento</h2>
      <form @submit.prevent="updateEvent">
        <div>
          <label for="title">Título:</label>
          <input type="text" v-model="updatedEvent.title" required />
        </div>
        <div>
          <label for="comment">Comentario:</label>
          <textarea v-model="updatedEvent.comment" required></textarea>
        </div>
        <div class="modal-buttons">
          <button type="submit">Actualizar</button>
          <button type="button" @click="$emit('close-modal')">Cancelar</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  props: ['event'],
  data() {
    return {
      updatedEvent: { ...this.event }
    };
  },
  methods: {
    updateEvent() {
      this.$emit('update-event', this.updatedEvent);
    }
  }
};
</script>

<style>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  background: white;
  padding: 20px;
  border-radius: 5px;
  max-width: 500px;
  width: 100%;
}

.modal-buttons {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
}

.modal-buttons button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.modal-buttons button[type="submit"] {
  background-color: #007bff;
  color: white;
}

.modal-buttons button[type="button"] {
  background-color: #f44336;
  color: white;
}
</style>
