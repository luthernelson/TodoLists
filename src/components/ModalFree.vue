<script setup>
import { defineProps, defineEmits } from "vue";

//Definition des proprietes du modal

defineProps({

    isVisible: {
        type: Boolean,
        required: true,
    }, 
    data: {
      require: true,
      type: Object
    }
})

//Definition des evenements
const emit = defineEmits(["close"]);

//Methode pour fermer le Modal
const closeModal = () =>{
    emit("close")
}
</script>


<template>
    <div v-if="isVisible" class="modal-overlay" @click.self="closeModal">
        <div class="modal-content">
            <header>
                    <h2 style="padding:40px 20px;"> Details de la tache {{ data.title }} !</h2>
                <button class="close-button" @click="closeModal">×</button>
            </header>
      <!-- Corps du Modal -->
      <div class="modal-body"   >
        <!-- Description -->
        <h3 style="Padding: 0px 20px;">
          {{ data.description }}
        </h3>

        <!-- Étapes de Réalisation (To-Do List) -->
        <div>
          <strong></strong>
          <ul>
            <li v-for="(subTask, index) in data.subTodoList" :key="index">{{ subTask.subTodoList}}</li>
          </ul>
        </div>
      </div>

        </div>
    </div>
</template>

<style scoped>
/* Arrière-plan semi-transparent */
.modal-overlay {
  position: fixed;
  padding:0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(255, 255, 255, 0.103);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  animation: fadeIn 0.3s ease-in-out;
  overflow: hidden;
}

/* Contenu de la modal avec taille augmentée */
.modal-content {
  background: #fff;
  width: 35%; /* Augmenté pour occuper 80% de la largeur de l'écran */
  max-width: 900px; /* Taille maximale augmentée */
  height: 70%; /* Augmenté pour occuper 70% de la hauteur de l'écran */
  border: 1px solid #ddd;
  position: relative;
  animation: scaleUp 0.3s ease-in-out;
  overflow: auto; /* Gère le défilement si le contenu dépasse */
}

/* Bouton de fermeture "X" */
.close-button {
  position: absolute;
  top: 10px;
  right: 10px;
  background: none;
  border: none;
  font-size: 2em;
  font-weight: bold;
  cursor: pointer;
  color: #333;
  transition: color 0.3s ease;
  z-index: 10;
}

.close-button:hover {
  color: #ff0000;
}

/* En-tête de la modal */
.modal-header {
  padding: 20px;
  background: #f4f4f4;
  border-bottom: 1px solid #ddd;
  font-size: 1.5rem;
  font-weight: bold;
}

/* Corps de la modal */
.modal-body {
  padding: 20px;
  font-size: 1rem;
  color: #333;
  line-height: 1.5;
}

/* Pied de la modal */
.modal-footer {
  padding: 20px;
  text-align: right;
  background: #f4f4f4;
  border-top: 1px solid #ddd;
}

/* Bouton d'action */
.btn {
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.btn:hover {
  background-color: #0056b3;
}

/* Animations */
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes scaleUp {
  from {
    transform: scale(0.9);
  }
  to {
    transform: scale(1);
  }
}
</style>

