<template>
    <div class="container">
      <h1>LISTE DES TACHES</h1>
      <div class="actions">
        <!--<input
          type="text"
          v-model="searchQuery"
          placeholder="Rechercher une tâche..."
          class="search-bar"
        />-->
      </div>
      <button @click="toggleForm" class="add-button">Créer une nouvelle tâche</button>
  
      <form v-if="showForm" @submit.prevent="saveTodoList">
        <label style="color:black;">Titre de la Tache</label>
        <br><input type="text" v-model="todo.title" placeholder="Titre de la tache" required />
        <br><label style="color:black;">Sélectionner une image</label>
        <br><input type="file" @change="handleFileUpload" required />
        <br><label style="color:black;">Description</label>
        <br><textarea v-model="todo.description" required></textarea>
  
        <div v-for="(subTodo, index) in todo.subTodoLists" :key="index">
          <label style="color:black;">Éléments caractéristiques de la tâche</label>
          <br><input v-model="todo.subTodoLists[index].name" type="text" required />
          <ul><li>{{ subTodo.name }}</li></ul>
        </div>
        <button class="btn btn-secondary" @click.prevent="addSubTodo">
          <i class="fa-solid fa-plus" style="color: #020af2;"></i>
        </button>
        <br><button type="submit">ENREGISTRER</button>
      </form>
  
      <div v-if="todoLists.length === 0" style="color:black;">
        <i class="fa-solid fa-hourglass-start" style="color: #0400ff;"></i>
        Aucune Tâches disponible .......
      </div>
      <div class="tasks-grid" v-else>
        <div class="task-card" v-for="(list, index) in todoLists" :key="index">
          <div class="task-image">
            <img v-if="list.file" :src="list.file" alt="Task Image" style="width:80px; height: 80px; border-radius:100%;" />
          </div>
          <div class="task-info">
            <h2 @click.self="openModal(list)">{{ list.title }}</h2>
            <p>{{ list.description }}</p>
            <span class="task-state" :class="{ completed: list.etat === 'Terminé' }">
              {{ list.etat || 'En cours' }}
            </span>
            <label>
              <input type="checkbox" v-model="list.checked" @change="toggleTaskState(list)" />
            </label>
            <button class="btn btn-primary" @click="removeTask(index)">
              <i class="fa-solid fa-trash-can fa-2x" style="color: #ff0000;"></i>
            </button>
          </div>
        </div>
      </div>
  
      <ModalFree :isVisible="showModal" @close="showModal = false" :data="selectedTodo">
        <template #header>
          <h3>{{ selectedTodo?.title }}</h3>
          <p>{{ selectedTodo.description }}</p>
          <ul v-if="selectedTodo?.subTodoLists?.length > 0">
            <li v-for="(task, index) in selectedTodo?.subTodoLists" :key="index">
              {{ task.name }}
            </li>
          </ul>
        </template>
        <template #footer>
          <button class="btn" @click="showModal = false">Fermer</button>
        </template>
      </ModalFree>
    </div>
  </template>
  
  <script setup>
  import { ref, defineEmits } from 'vue';
  import ModalFree from './ModalFree.vue';
  
  const props = defineProps({
    todoLists: Array
  });
  
  // -----------------------------  ecouteur d'evenement --------------------------------

  const emit = defineEmits(['update:todoLists']);
  
  //------------------------ Reference --------------------------
  const showForm = ref(false);
  const selectedTodo = ref(null);
  const showModal = ref(false);

  // ----------------------------------------- Objet ----------------------------------
  const todo = ref({
    title: '',
    description: '',
    file: '',
    subTodoLists: []
  });
  
  
  //-------------------------------------- fonction ----------------------------------
  
  const toggleForm = () => { // Fonction gerant l'affichage
    showForm.value = !showForm.value;
    if (showForm.value && todo.value.subTodoLists.length === 0) {
      todo.value.subTodoLists.push({ name: '' });
    }
  };
  
  const openModal = (subTodoLists) => { //Fonction gerant l'affichage du modal
    selectedTodo.value = subTodoLists; 
    showModal.value = true;
  };
  
  const handleFileUpload = (event) => { // fonction pour l'affichage des images 
    const file = event.target.files[0];
    const reader = new FileReader();
    reader.onload = () => {
      todo.value.file = reader.result;
    };
    if (file) {
      reader.readAsDataURL(file);
    }
  };
  
  const addSubTodo = () => {
    todo.value.subTodoLists.push({ name: '' });
  };
  
  const saveTodoList = () => { // fonction ajoutant un nouvelle tache
    const newList = {
      title: todo.value.title,
      description: todo.value.description,
      file: todo.value.file,
      subTodoLists: [...todo.value.subTodoLists]
    };
    emit('update:todoLists', [...props.todoLists, newList]);
    resetForm();
  };
  
  const resetForm = () => { // fonction de ractualisants des champs apre enregisrement d'une tache
    showForm.value = false;
    todo.value = { title: '', description: '', file: '', subTodoLists: [] };
  };
  
  const toggleTaskState = (task) => { // fonction de gestion des statuts
    task.etat = task.etat === 'Terminé' ? 'En cours' : 'Terminé';
  };
  
  const removeTask = (index) => { // fonction pour supprimer une nouvelle tache
    const updatedLists = [...props.todoLists];
    updatedLists.splice(index, 1);
    emit('update:todoLists', updatedLists);
  };
  </script>
  
  <style scoped>

  .container {
    width: 100%;
      height: 100%;
      display: flex;
      flex-direction: column;
      padding:0px 60px; /* Espacement interne */
      box-sizing: border-box; /* Inclut le padding dans la taille totale */   
      background-color: white;  
  }
  
  h1 {
      font-size: 24px;
      text-transform: uppercase;
      color: black;
      font-weight: bold;
      margin-bottom: 20px;
  }
  
  .add-button {
    width: 300px;
      background-color: #059211;
      color: #ffffff;
      border: none;
      padding: 10px 20px;
      border-radius: 5px;
      cursor: pointer;
      float: right;
      margin-bottom: 20px;
  }
  
  
  
  /* Bloc contenant toutes les tâches */
  .tasks-grid {
    display: grid;
    grid-template-columns: repeat(2, minmax(300px, 1fr)); /* Responsive */
    gap: 40px;
    padding: 0px;
  }
  
  /* Carte individuelle représentant une tâche */
  .task-card {
    background-color: #f3eded; /* Fond blanc pour contraste */
    border-radius: 10px; /* Coins arrondis */
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1); /* Ombre douce */
    padding: 20px;
    display: flex;
    align-items: flex-start;
    gap: 15px;
    position: relative; /* Positionnement pour les enfants absolus */
    transition: transform 0.2s ease, box-shadow 0.2s ease; /* Animation hover */
  }
  
  /* Animation au survol */
  .task-card:hover {
    transform: translateY(-5px); /* Légère élévation */
    box-shadow: 0 8px 12px rgb(0, 0, 0); /* Ombre plus prononcée */
    cursor:auto;
  }
  
  /* Image ou icône représentant une tâche */
  .task-image {
    width: 80px;
    height: 80px;
    background-color: #d54c4c; /* Rouge vif */
    border-radius:100%;
    color: #ffffff; /* Texte blanc */
    display: flex;
    justify-content: center;
    align-items: center;
    font-weight: bold;
    text-transform: uppercase;
    font-size: 1rem;
      top: 10px; 
  }
  
  /* Conteneur des informations sur la tâche */
  .task-info {
    flex: 1; /* Occupe tout l'espace disponible */
  }
  
  /* Titre de la tâche */
  .task-info h2 {
    margin: 0;
    color: #333333; /* Texte noir doux */
    font-size: 1.2rem;
    font-weight: bold;
    cursor:pointer;
  }
  
  /* Description de la tâche */
  .task-info p {
    margin: 10px 0;
    font-size: 1rem;
    color: #666666; /* Texte gris doux */
    line-height: 1.5;
  }
  
  /* Badge d'état */
  .task-state {
    background-color: #6b5b95; /* Couleur violet doux */
    color: #fdfdfd;
    padding: 5px 10px;
    border-radius: 5px;
    font-size: 0.8rem;
    text-transform: uppercase;
    position: absolute;
    top: 10px; /* En haut */
    right: 10px; /* À droite */
  }
  
  /* Bouton pour supprimer une tâche */
  .task-card button {
    background-color: transparent; /* Fond transparent */
    border: none; /* Pas de bordure */
    position: absolute;
    bottom: 10px; /* En bas */
    right: 10px; /* À droite */
    cursor: pointer;
    transition: transform 0.2s ease; /* Animation au survol */
  }
  
  /* Icône de suppression */
  .task-card button i {
    font-size: 1.5rem;
    color: #ff0000; /* Rouge vif */
  }
  
  /* Animation au survol du bouton */
  .task-card button:hover i {
    transform: scale(1.2); /* Grossissement */
  }
  
  form {
    background-color: #f9f9f9;
    border-radius: 10px;
    padding: 20px;
    margin-bottom: 20px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  }
  
  form label {
    font-weight: bold;
    color: #333;
    margin-bottom: 8px;
    display: block;
  }
  
  form input[type="text"],
  form input[type="file"],
  form textarea {
    width: 100%;
    padding: 12px 15px;
    margin-bottom: 15px;
    border: 1px solid #ccc;
    border-radius: 5px;
    font-size: 1rem;
    box-sizing: border-box;
    transition: border-color 0.3s ease;
  }
  
  form input:focus,
  form textarea:focus {
    border-color: #4caf50;
    outline: none;
  }
  
  form button[type="submit"],
  form .btn {
    background-color: #4caf50;
    color: white;
    padding: 12px 20px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-size: 1rem;
    transition: background-color 0.3s ease, transform 0.2s ease;
  }
  
  form button[type="submit"]:hover,
  form .btn:hover {
    background-color: #45a049;
    transform: scale(1.05);
  }
  
  form button[type="submit"]:disabled {
    background-color: #ccc;
    cursor: not-allowed;
  }
  
  form .btn {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    padding: 8px 12px;
    background-color: #eaecee;
    color: white;
    border-radius: 5px;
    font-size: 1rem;
    cursor: pointer;
    margin-left: 10px;
    transition: background-color 0.3s ease;
  }
  
  form .btn:hover {
    background-color: #0056b3;
  }
  
  form .btn i {
    margin-right: 5px;
  }
  
  div {
    margin-top: 20px;
  }
  
  div ul {
    list-style-type: disc;
    padding-left: 20px;
  }
  
  div ul li {
    color: #555;
    font-size: 1rem;
    margin-bottom: 5px;
    position: relative;
  }
  
  div ul li::before {
    content: "•";
    color: #4caf50;
    position: absolute;
    left: -20px;
  }
  
  div ul li:hover {
    color: #333;
  }
  .actions {
    display: flex;
    align-items: center;
    gap: 10px; /* Espacement entre la barre de recherche et le bouton */
  }
  
  .search-bar {
    padding: 8px;
    border: 1px solid #ccc;
    border-radius: 4px;
    font-size: 1rem;
    flex: 1; /* Permet à la barre de recherche de s'étendre */
    max-width: 300px; /* Largeur maximale */
  }
  
  </style>
  