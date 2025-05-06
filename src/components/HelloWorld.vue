<template>
  <div class="container">
    <header>
      <h1>Daily Quest 🏰</h1>
      <div class="xp-counter">
        <div class="xp-bar" :style="{ width: (totalXP / 100) * 100 + '%' }"></div>
        <span>Total XP: {{ totalXP }}</span>
      </div>
    </header>

    <div class="controls">
      <div class="filter-buttons">
        <button 
          v-for="filter in filters" 
          :key="filter"
          :class="{ active: currentFilter === filter }"
          @click="currentFilter = filter"
        >
          {{ filter }}
        </button>
      </div>
      
      <form @submit.prevent="addQuest" class="add-quest">
        <input
          type="text"
          v-model="newQuest.title"
          placeholder="Nama Quest"
          required
        />
        <textarea
          v-model="newQuest.description"
          placeholder="Deskripsi Quest (opsional)"
        ></textarea>
        <button type="submit">Tambah Quest! ⚔</button>
      </form>
    </div>

    <div class="quest-list">
      <div 
        v-for="quest in filteredQuests"
        :key="quest.id"
        class="quest-card"
        :class="{ completed: quest.completed }"
      >
        <div class="quest-icon">
          {{ quest.completed ? '🛡' : '🗡' }}
        </div>
        <div class="quest-content">
          <h3>{{ quest.title }}</h3>
          <p v-if="quest.description">{{ quest.description }}</p>
          <div class="quest-meta">
            <span>XP: {{ quest.xp }}</span>
          </div>
        </div>
        <div class="quest-actions">
          <button @click="toggleQuest(quest.id)" class="complete-btn">
            {{ quest.completed ? '🥇' : '⚪' }}
          </button>
          <button @click="deleteQuest(quest.id)" class="delete-btn">💀</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const quests = ref([]);
const newQuest = ref({
  title: '',
  description: '',
});
const currentFilter = ref('All');
const filters = ['All', 'Active', 'Completed'];

// Generate random XP antara 10-50
const generateXP = () => Math.floor(Math.random() * 41) + 10;

const addQuest = () => {
  if (!newQuest.value.title) return;
  
  quests.value.push({
    id: Date.now(),
    title: newQuest.value.title,
    description: newQuest.value.description,
    xp: generateXP(),
    completed: false
  });
  
  newQuest.value.title = '';
  newQuest.value.description = '';
};

const deleteQuest = (id) => {
  if (!confirm('Yakin ingin menghapus quest ini?')) return;
  quests.value = quests.value.filter(quest => quest.id !== id);
};

const toggleQuest = (id) => {
  const index = quests.value.findIndex(quest => quest.id === id);
  quests.value[index].completed = !quests.value[index].completed;
};

const totalXP = computed(() => {
  return quests.value.reduce((sum, quest) => {
    return quest.completed ? sum + quest.xp : sum;
  }, 0);
});

const filteredQuests = computed(() => {
  switch(currentFilter.value) {
    case 'Active':
      return quests.value.filter(quest => !quest.completed);
    case 'Completed':
      return quests.value.filter(quest => quest.completed);
    default:
      return quests.value;
  }
});
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=MedievalSharp&display=swap');

* {
  box-sizing: border-box;
  font-family: 'MedievalSharp', cursive;
}

body {
  margin: 0;
  background: linear-gradient(to right, #2c3e50, #3498db);
  min-height: 100vh;
  color: #ecf0f1;
}

.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

header {
  text-align: center;
  margin-bottom: 2rem;
}

.xp-counter {
  background: #2c3e50;
  padding: 10px;
  border-radius: 8px;
  position: relative;
}

.xp-bar {
  position: absolute;
  left: 0;
  top: 0;
  height: 100%;
  background: #27ae60;
  border-radius: 8px;
  transition: width 0.3s ease;
}

.controls {
  background: rgba(44, 62, 80, 0.9);
  padding: 20px;
  border-radius: 10px;
  margin-bottom: 20px;
}

.filter-buttons {
  display: flex;
  gap: 10px;
  margin-bottom: 15px;
}

button {
  background: #e67e22;
  border: none;
  padding: 8px 15px;
  border-radius: 5px;
  cursor: pointer;
  transition: all 0.3s ease;
}

button:hover {
  background: #d35400;
  transform: scale(1.05);
}

button.active {
  background: #27ae60;
}

.quest-list {
  display: grid;
  gap: 15px;
}

.quest-card {
  background: rgba(255, 255, 255, 0.1);
  border-radius: 10px;
  padding: 15px;
  display: flex;
  align-items: center;
  gap: 15px;
  backdrop-filter: blur(5px);
  transition: all 0.3s ease;
}

.quest-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 5px 15px rgba(0,0,0,0.3);
}

.quest-icon {
  font-size: 2rem;
}

.quest-content {
  flex-grow: 1;
}

.quest-content h3 {
  margin: 0 0 5px 0;
}

.quest-meta {
  opacity: 0.8;
  font-size: 0.9em;
}

.completed {
  opacity: 0.6;
}

.completed h3 {
  text-decoration: line-through;
}

.quest-actions {
  display: flex;
  gap: 10px;
}

.complete-btn {
  padding: 5px 10px;
  font-size: 1.2em;
}

.delete-btn {
  background: #e74c3c;
}

.add-quest {
  display: grid;
  gap: 10px;
}

input, textarea {
  padding: 10px;
  border-radius: 5px;
  border: 1px solid #34495e;
  background: rgba(255,255,255,0.1);
  color: white;
}

input::placeholder, textarea::placeholder {
  color: #bdc3c7;
}
</style>