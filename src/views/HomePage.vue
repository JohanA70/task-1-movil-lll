<script setup>
import { IonInput, IonButton, IonList, IonItem, IonCheckbox, IonPage, IonHeader, IonToolbar, IonTitle, IonContent } from '@ionic/vue';
import { ref, onMounted } from 'vue';
import { Preferences } from '@capacitor/preferences';

const tasks = ref([]);
const newTask = ref('');

const loadTasks = async () => {
  const storedTasks = await Preferences.get({ key: 'tasks' });
  tasks.value = storedTasks.value ? JSON.parse(storedTasks.value) : [];
};

const saveTasks = async () => {
  await Preferences.set({ key: 'tasks', value: JSON.stringify(tasks.value) });
};

const addTask = () => {
  if (newTask.value.trim()) {
    tasks.value.push({ text: newTask.value, completed: false });
    newTask.value = '';
    saveTasks();
  }
};

const deleteTask = (index) => {
  tasks.value.splice(index, 1);
  saveTasks();
};

const toggleTask = (event, task) => {
  for (let i = 0; i < tasks.value.length; i++) {
    if (tasks.value[i] === task) {
      tasks.value[i].completed = !event.target.checked;
      break;
    }
  }
  saveTasks();
};

onMounted(loadTasks);
</script>

<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Gestor de Tareas</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true" class="ion-padding">
      <ion-input v-model="newTask" placeholder="Nueva tarea"></ion-input>
      <ion-button @click="addTask">Agregar</ion-button>
      <ion-list>
        <ion-item v-for="(task, index) in tasks" :key="index">
          <ion-checkbox @click="toggleTask($event, task)" :checked="task.completed"></ion-checkbox>
          <span>{{ task.text }}</span>
          <ion-button color="danger" @click="deleteTask(index)">Eliminar</ion-button>
        </ion-item>
      </ion-list>
    </ion-content>
  </ion-page>
</template>

<style scoped>
ion-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

ion-input {
  margin-bottom: 10px;
  width: 90%;
}

ion-button {
  margin-bottom: 10px;
}
</style>
