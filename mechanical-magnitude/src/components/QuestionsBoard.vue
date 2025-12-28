<template>
  <div class="row">
    <div class="col-md-4">
      <h4>Kategorie</h4>
      <div class="list-group mb-4">
        <button v-for="tag in tags" :key="tag" @click="filterByTag(tag)" 
                class="list-group-item list-group-item-action"
                :class="{ active: selectedTag === tag }">
          {{ tag }}
        </button>
      </div>
    </div>
    <div class="col-md-8">
      <div class="d-flex justify-content-between align-items-center mb-4">
        <h2 class="text-primary-theme">Dotazy</h2>
        <button class="btn btn-primary-theme" @click="showForm = !showForm">
          {{ showForm ? 'Zavřít formulář' : 'Položit dotaz' }}
        </button>
      </div>

      <div v-if="showForm" class="card mb-4 shadow-sm bg-accent-theme">
        <div class="card-body">
          <h5 class="text-primary-theme">Nový dotaz</h5>
          <form @submit.prevent="submitQuestion">
            <div class="mb-3">
              <label class="form-label">Předmět</label>
              <input v-model="newQuestion.title" type="text" class="form-control" required>
            </div>
            <div class="mb-3">
              <label class="form-label">Detaily</label>
              <textarea v-model="newQuestion.details" class="form-control" rows="3" required></textarea>
            </div>
            <div class="mb-3">
              <label class="form-label">Tag</label>
              <select v-model="newQuestion.tag" class="form-select" required>
                <option v-for="tag in tags.slice(1)" :key="tag" :value="tag">{{ tag }}</option>
              </select>
            </div>
            <div class="mb-3">
              <label class="form-label">Vaše jméno</label>
              <input v-model="newQuestion.name" type="text" class="form-control" required>
            </div>
            <button type="submit" class="btn btn-secondary-theme">Odeslat dotaz</button>
          </form>
        </div>
      </div>

      <div v-for="q in filteredQuestions" :key="q.id" class="card mb-3 shadow-sm">
        <div class="card-body">
          <div class="d-flex justify-content-between">
            <h5 class="card-title text-primary-theme">{{ q.title }}</h5>
            <span class="badge bg-secondary-theme">{{ q.tag }}</span>
          </div>
          <p class="card-text">{{ q.details }}</p>
          <div class="d-flex justify-content-between align-items-center">
            <small class="text-muted">Od: {{ q.name }}</small>
            <button class="btn btn-sm btn-outline-primary">Odpovědět</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

const tags = ['Vše', 'Daně', 'Legislativa', 'Pojištění', 'Smlouvy', 'Ostatní'];
const selectedTag = ref('Vše');
const showForm = ref(false);

const questions = ref([
  { id: 1, title: 'Jak na paušální daň?', details: 'Můžete mi poradit, jestli se mi vyplatí paušální daň při příjmu 800k?', tag: 'Daně', name: 'Petr N.' },
  { id: 2, title: 'Změny v dohodách (DPP/DPČ)', details: 'Jaké jsou hlavní změny v dohodách od ledna 2026?', tag: 'Legislativa', name: 'Jana M.' },
]);

const newQuestion = ref({
  title: '',
  details: '',
  tag: 'Ostatní',
  name: ''
});

onMounted(() => {
  const saved = localStorage.getItem('freelance_questions');
  if (saved) {
    questions.value = JSON.parse(saved);
  }
});

const filteredQuestions = computed(() => {
  if (selectedTag.value === 'Vše') return questions.value;
  return questions.value.filter(q => q.tag === selectedTag.value);
});

function filterByTag(tag) {
  selectedTag.value = tag;
}

function submitQuestion() {
  const q = {
    ...newQuestion.value,
    id: Date.now()
  };
  questions.value.unshift(q);
  localStorage.setItem('freelance_questions', JSON.stringify(questions.value));
  
  // Reset form
  newQuestion.value = { title: '', details: '', tag: 'Ostatní', name: '' };
  showForm.value = false;
}
</script>
