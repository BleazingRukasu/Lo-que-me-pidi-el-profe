<template>
  <div class="calendar-container">
    <h1 class="calendar-title">Agenda</h1>
    <div class="calendar-wrapper">
      <div class="calendar">
        <div class="calendar-header">
          <button @click="prevYear" class="nav-button small-button">Año Anterior</button>
          <button @click="prevMonth" class="nav-button small-button">Mes Anterior</button>
          <span class="current-date">{{ currentMonth }} {{ currentYear }}</span>
          <button @click="nextMonth" class="nav-button small-button">Mes Siguiente</button>
          <button @click="nextYear" class="nav-button small-button">Año Siguiente</button>
        </div>
        <div class="calendar-grid">
          <div class="day" v-for="day in daysInMonth" :key="day">
            <div @click="selectDay(day)" class="day-number">{{ day }}</div>
          </div>
        </div>
        <EventForm v-if="selectedDay" @add-event="addEvent" :day="selectedDay" />
      </div>
      <div class="event-list">
        <h2 class="event-list-title">Eventos Agendados</h2>
        <input type="text" v-model="searchQuery" placeholder="Buscar eventos..." class="search-input" />
        <div v-for="(eventArray, day) in filteredEvents" :key="day">
          <h3 class="event-day">{{ currentMonth }} {{ day }}, {{ currentYear }}</h3>
          <div v-for="(event, index) in eventArray" :key="event.id" class="event-item">
            <div class="event-content">
              <strong>{{ event.title }}</strong>
              <p>{{ event.comment }}</p>
            </div>
            <div class="event-buttons">
              <button @click="editEvent(day, index)" class="edit-button small-button">Editar</button>
              <button @click="deleteEvent(day, index)" class="delete-button small-button">Eliminar</button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <EditEventModal v-if="editingEvent" :event="editingEvent" @update-event="updateEvent" @close-modal="closeEditModal" />
  </div>
</template>

<script>
import EventForm from './EventForm.vue';
import EditEventModal from './EditEventModal.vue';

export default {
  components: { EventForm, EditEventModal },
  data() {
    return {
      currentYear: new Date().getFullYear(),
      currentMonth: new Date().getMonth(),
      daysInMonth: [],
      events: {},
      selectedDay: null,
      searchQuery: '',
      editingEvent: null,
      editingEventIndex: null,
      editingEventDay: null
    };
  },
  computed: {
    currentMonth() {
      return new Date(this.currentYear, this.currentMonth).toLocaleString('default', { month: 'long' });
    },
    filteredEvents() {
      const query = this.searchQuery.toLowerCase();
      if (!query) return this.events;

      return Object.keys(this.events).reduce((acc, day) => {
        const filtered = this.events[day].filter(event =>
          event.title.toLowerCase().includes(query) || event.comment.toLowerCase().includes(query)
        );
        if (filtered.length) acc[day] = filtered;
        return acc;
      }, {});
    }
  },
  methods: {
    getDaysInMonth() {
      const days = new Date(this.currentYear, this.currentMonth + 1, 0).getDate();
      this.daysInMonth = Array.from({ length: days }, (_, i) => i + 1);
    },
    prevMonth() {
      if (this.currentMonth === 0) {
        this.currentMonth = 11;
        this.currentYear--;
      } else {
        this.currentMonth--;
      }
      this.getDaysInMonth();
    },
    nextMonth() {
      if (this.currentMonth === 11) {
        this.currentMonth = 0;
        this.currentYear++;
      } else {
        this.currentMonth++;
      }
      this.getDaysInMonth();
    },
    prevYear() {
      this.currentYear--;
      this.getDaysInMonth();
    },
    nextYear() {
      this.currentYear++;
      this.getDaysInMonth();
    },
    selectDay(day) {
      this.selectedDay = day;
    },
    addEvent(event) {
      if (!this.events[this.selectedDay]) {
        this.events[this.selectedDay] = [];
      }
      this.events[this.selectedDay].push(event);
      this.selectedDay = null;
    },
    editEvent(day, index) {
      this.editingEvent = { ...this.events[day][index] };
      this.editingEventIndex = index;
      this.editingEventDay = day;
    },
    updateEvent(updatedEvent) {
      if (this.editingEventDay !== null && this.editingEventIndex !== null) {
        this.events[this.editingEventDay].splice(this.editingEventIndex, 1, updatedEvent);
      }
      this.closeEditModal();
    },
    closeEditModal() {
      this.editingEvent = null;
      this.editingEventIndex = null;
      this.editingEventDay = null;
    },
    deleteEvent(day, index) {
      this.events[day].splice(index, 1);
      if (this.events[day].length === 0) {
        delete this.events[day];
      }
    }
  },
  mounted() {
    this.getDaysInMonth();
  }
  }

</script>

<style>
.calendar-container {
  max-width: 900px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

.calendar-title {
  text-align: center;
  font-size: 2em;
  margin-bottom: 20px;
}

.calendar-wrapper {
  display: flex;
  justify-content: space-between;
}

.calendar {
  flex: 2;
}

.calendar-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.nav-button {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 5px 10px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 0.9em;
}

.small-button {
  padding: 2.5px 5px;
  font-size: 0.9em;
}

.nav-button:hover {
  background-color: #0056b3;
}

.current-date {
  font-size: 1.0em;
}

.calendar-grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 10px;
}

.day {
  border: 1px solid #2b8601;
  border-radius: 5px;
  padding: 10px;
  text-align: center;
  cursor: pointer;
  background-color: #47b905;
  transition: background-color 0.3s;
}

.day:hover {
  background-color: #f1f1f1;
}

.day-number {
  font-size: 1.2em;
  margin-bottom: 5px;
}

.event-list {
  flex: 1;
  margin-left: 20px;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 5px;
  background-color: #f9f9f9;
}

.event-list-title {
  font-size: 1.5em;
  margin-bottom: 20px;
  text-align: center;
}

.search-input {
  width: 100%;
  padding: 10px;
  margin-left: -10px;
  margin-bottom: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.event-day {
  font-size: 1.2em;
  margin-bottom: 10px;
  border-bottom: 1px solid #ddd;
  padding-bottom: 5px;
}

.event-item {
  background-color: #007bff;
  color: white;
  padding: 10px;
  border-radius: 5px;
  margin-bottom: 10px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.event-content {
  flex: 1;
  margin-right: 10px;
}

.event-buttons {
  display: flex;
  gap: 5px;
}

.edit-button, .delete-button {
  background-color: #ff9800;
  color: white;
  border: none;
  padding: 5px 10px;
  border-radius: 3px;
  cursor: pointer;
}

.edit-button:hover {
  background-color: #e68900;
}

.delete-button {
  background-color: #f44336;
}

.delete-button:hover {
  background-color: #d32f2f;
}
</style>
