<script setup>
import { ref, computed } from 'vue';

const questions = ref([
  {
    id: 1,
    text: '¿Cuál es el primer videojuego de la serie "The Legend of Zelda" lanzado por Nintendo en 1986?',
    answers: ['The Legend of Zelda: Ocarina of Time', 'The Legend of Zelda: A Link to the Past', 'The Legend of Zelda'],
    correctAnswer: 'The Legend of Zelda'
  },
  {
    id: 2,
    text: 'En el juego "Super Mario Bros.", ¿cuál es el nombre del hermano de Mario que a menudo se le representa como jugador 2?',
    answers: ['Luigi', 'Yoshi', 'Wario'],
    correctAnswer: 'Luigi'
  },
  {
    id: 3,
    text: 'Identifica el juego mostrado en la imagen pequeña:',
    answers: ['Bloodborne', 'Dark Souls III', 'Sekiro: Shadows Die Twice'],
    correctAnswer: 'Bloodborne',
    smallImage: '/pequeño.png',
    largeImage: '/grande.png',
  },
  {
    id: 4,
    text: 'El primer videojuego que se jugó en el espacio exterior es...',
    answers: ['Starcraft', 'Exerion', 'Star Wars (1983 – Atari)'],
    correctAnswer: 'Starcraft'
  },
  {
    id: 5,
    text: 'El fantasma naranja del videojuego "Pac-Man" se llama...',
    answers: ['Clyde', 'Siguanaba', 'Slimer '],
    correctAnswer: 'Clyde'
  },
]);

const userAnswers = ref({});
const showResults = ref(false);
const currentQuestionIndex = ref(0);
const results = ref([]);
let moveIntervals = []; // Array para almacenar intervalos de movimiento de GIFs

const gifPosition = ref({ x: 0, y: 0 });

const currentQuestion = computed(() => questions.value[currentQuestionIndex.value]);

const startAnimation = () => {
  moveGif();
};
const stopAnimation = () => {
  clearInterval(moveIntervals);
  gifPosition.value = { x: 0, y: 0 };
};

const moveGif = () => {
  const img = document.createElement('img');
  img.src = gifs[currentQuestionIndex.value % gifs.length];
  img.classList.add('moving-gif');
  document.body.appendChild(img);

  moveIntervals.push(setInterval(() => {
    gifPosition.value.x += Math.random() > 0.5 ? 1 : -1;
    gifPosition.value.y += Math.random() > 0.5 ? 1 : -1;
    img.style.transform = `translate(${gifPosition.value.x}px, ${gifPosition.value.y}px)`;
  }, 10));
};
const checkAnswer = () => {
  results.value = [];
  const currentQuestion = questions.value[currentQuestionIndex.value];
  if (userAnswers.value[currentQuestion.id] === currentQuestion.correctAnswer) {
    results.value.push(`Pregunta ${currentQuestion.id}: Correcta`);
  } else {
    results.value.push(`Pregunta ${currentQuestion.id}: Incorrecta`);
  }
  showResults.value = true;

  // Agregar el GIF correspondiente al DOM
  const gifIndex = currentQuestionIndex.value % gifs.length; // Calcula el índice del GIF
  const img = document.createElement('img');
  img.src = gifs[gifIndex];
  img.classList.add('moving-gif');
  const x = Math.random() * (window.innerWidth - img.width); // Posición X aleatoria
  const y = Math.random() * (window.innerHeight - img.height); // Posición Y aleatoria
  img.style.left = `${x}px`;
  img.style.top = `${y}px`;
  document.body.appendChild(img);
};

const moveToNextQuestion = () => {
  // Detener todos los intervalos de movimiento de los GIFs
  moveIntervals.forEach(interval => clearInterval(interval));
  // Eliminar todos los elementos GIF del DOM
  const gifs = document.querySelectorAll('.moving-gif');
  gifs.forEach(gif => gif.remove());
  
  currentQuestionIndex.value++;
  showResults.value = false;
};

const gifs = [
  '/gif1.gif',
  '/gif2.gif',
  '/gif3.gif',
  '/git4.gif',
  '/gif5.gif'
];
</script>

<template>
  <div id="app">
    <h1 v-bind:class="{ 'highlight': showResults }">TRIVIA</h1>
    <div class="fondoClaro">
      <div v-if="!showResults">
        <div v-for="(question, index) in questions" v-show="index === currentQuestionIndex" :key="index">
          <p>{{ question.text }}</p>
          <br>
          <div v-for="(answer, index) in question.answers" :key="index">
            <input type="radio" :id="answer" :value="answer" v-model="userAnswers[question.id]" :name="question.id">
            <label :for="answer">{{ answer }}</label>
          </div>
          <div class="image-container" v-if="question.smallImage">
            <img :src="question.smallImage" alt="Fragmento de juego">
          </div>
        </div>
        <button @click="checkAnswer">Comprobar respuesta</button>
      </div>
      <div v-else>
        <h2>Resultado</h2>
        <p v-for="(result, index) in results" :key="index">{{ result }}</p>
        <button @click="moveToNextQuestion" v-show="currentQuestionIndex + 1 < questions.length">Siguiente pregunta</button>
        <img :src="currentQuestion.largeImage" v-if="showResults && currentQuestion.id === 3 && userAnswers[currentQuestion.id] === currentQuestion.correctAnswer" alt="Imagen completa del juego">
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh; 
}

img {
  max-width: 100%; 
  max-height: 100%; 
}

.moving-gif {
  position: fixed;
}
</style>