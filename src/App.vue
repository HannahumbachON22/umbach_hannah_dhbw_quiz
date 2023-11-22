<script setup>
import { ref, computed } from 'vue';

// Dateninitialisierung für das Quiz

const questions = ref([
  {
    question: 'In welchem Jahr wurde die DHBW gegründet?',
    answer: 0,
    options: ['2009', '2003', '1998'],
    selected: null
  },
  {
    question: 'An welchem Standort kann man den Studiengang Onlinemedien studieren?',
    answer: 2,
    options: ['Heilbronn', 'Stuttgart', 'Mosbach'],
    selected: null
  },
  {
    question: 'Für was steht die Abkürzung "DHBW"?',
    answer: 1,
    options: [
      'Deutsche Hochschule Baden-Württemberg',
      'Duale Hochschule Baden-Württemberg',
      'Die Hochschule Baden-Württembergs'
    ],
    selected: null
  }
]);

// Eigenschaften für die Fragen

const quizCompleted = ref(false);
const gameOver = ref(false);
const currentQuestion = ref(0);
const timer = ref(60);

const score = ref(0);

const getCurrentQuestion = computed(() => {
  let question = questions.value[currentQuestion.value];
  question.index = currentQuestion.value;
  return question;
});

// Funktion zum Setzen der ausgewählten Antwort

const setAnswer = (index) => {
  questions.value[currentQuestion.value].selected = index;

  if (index === getCurrentQuestion.value.answer) {
    score.value++;
  }
};

// Funktion für die nächste Frage

const nextQuestion = () => {
  if (currentQuestion.value < questions.value.length - 1) {
    currentQuestion.value++;
    getCurrentQuestion.value.selected = null;
  } else {
    quizCompleted.value = true;
  }
};

// Funktion zur Aktualisierung des Timers

const updateTimer = () => {
  timer.value -= 1;

  if (timer.value === 0 && !quizCompleted.value) {
    gameOver.value = true;
    alert('GAME OVER! Deine Zeit ist leider abgelaufen');
  } else if (timer.value > 0) {
    setTimeout(updateTimer, 1000);
  }
};

updateTimer();
</script>

<!-- Quiz-Abschnitt -->

<template>
  <main class="app">
    <h1>
      Das <span style="color: #e74c3c;">DH</span
      ><span style="color: #7f8c8d;">BW</span> Quiz
    </h1>
    <section class="quiz" v-if="!quizCompleted && !gameOver">
      <div class="quiz-info">
        <span class="question">{{ getCurrentQuestion.question }}</span>
        <span class="score">Punktzahl {{ score }}/{{ questions.length }}</span>
      </div>

<!-- Optionen für die aktuelle Frage -->

      <div class="options">
        <label
          v-for="(option, index) in getCurrentQuestion.options"
          :key="index"
          :for="'option' + index"
          :class="`option ${
            getCurrentQuestion.selected === index
              ? index === getCurrentQuestion.answer
                ? 'correct'
                : 'wrong'
              : ''
          } ${
            getCurrentQuestion.selected !== null && index !== getCurrentQuestion.selected
              ? 'disabled'
              : ''
          }`"
        >

<!-- Buttons für die Optionen -->

          <input
            type="radio"
            :id="'option' + index"
            :name="getCurrentQuestion.index"
            :value="index"
            v-model="getCurrentQuestion.selected"
            @change="() => setAnswer(index)"
          />
          <span>{{ option }}</span>
        </label>
      </div>

<!-- Button für die nächste Frage -->

      <button @click="nextQuestion" :disabled="getCurrentQuestion.selected === null">
        {{
          getCurrentQuestion.index === questions.length - 1
            ? 'Quiz beenden'
            : getCurrentQuestion.selected === null
            ? 'Wähle eine Antwort'
            : 'Nächste Frage'
        }}
      </button>

<!-- Timer Anzeige -->

      <div class="timer">
        Spielzeit: {{ timer }} Sekunden
      </div>
    </section>

<!-- Abschnitt für beendetes Quiz -->

    <section v-else-if="!gameOver">
      <h2>Du hast das Quiz beendet!</h2>
      <p>Deine Punktzahl lautet: {{ score }}/{{ questions.length }}</p>
    </section>

<!-- Abschnitt für Game Over -->

    <section v-else>
      <h2>Game Over!</h2>
    </section>
  </main>
</template>

<!-- Abschnitt für Style Bereich -->

  <style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: 'Work Sans', sans-serif;
    font-weight: bold;
  }

  body {
    background: url('dhbw.jpg') no-repeat center center fixed;
    background-size: cover;
    color: #ecf0f1;
    display: flex;
    align-items: center;
    justify-content: center;
    height: 100vh;
  }

  .app {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 2rem;
    max-width: 640px;
    width: 100%;
  }

  .title-box {
    background-color: #ecf0f1; 
    padding: 1rem;
    border-radius: 10px; 
    margin-bottom: 2rem; 
  }

  h1 {
    font-size: 3rem;
    margin-bottom: 2rem;
    color: #000000;
    padding: 1rem;
    border-radius: 10px;
  }

  .quiz {
    background-color: #615f5f;
    padding: 1rem;
    width: 100%;
    max-width: 640px;
    border-radius: 10px;
  }

  .quiz-info {
    display: flex;
    justify-content: space-between;
    margin-bottom: 1rem;
  }

  .quiz-info .question {
    color: #fff;
    font-size: 1.5rem;
  }

  .quiz-info .score {
    color: #080808;
    font-size: 1.rem;
	background-color: #ffffff; 
    padding: 1rem;
    border-radius: 10px; 
  }

  .options {
    margin-bottom: 1rem;
  }

  .option {
    padding: 1rem;
    display: block;
    background-color: #b0afaf;
    margin-bottom: 0.5rem;
    border-radius: 8px;
    cursor: pointer;
  }

  .option:hover {
    background-color: #888888;
  }

  .option.correct {
    background-color: #00933d;
  }

  .option.wrong {
    background-color: #b51354;
  }

  .option:last-of-type {
    margin-bottom: 0;
  }

  .option.disabled {
    opacity: 0.5;
  }

  .option input {
    display: none;
  }

  button {
    appearance: none;
    outline: none;
    border: none;
    cursor: pointer;
    padding: 0.5rem 1rem;
    background-color: #ffffff;
    color: #000000;
    font-weight: 700;
    text-transform: uppercase;
    font-size: 1.2rem;
    border-radius: 8px;
  }

  button:disabled {
    opacity: 0.5;
  }

  h2 {
    font-size: 3rem;
    margin-bottom: 2rem;
    text-align: center;
    color: #ffffff; 
    padding: 1rem;
	background-color: #b51354; 
    padding: 1rem;
    border-radius: 10px; 
  }
  

  p {
    color: #ecf0f1;
    font-size: 1.5rem;
    text-align: center;
    border-radius: 10px; 
  }
  .quiz {
  position: relative;
}

.timer {
  position: absolute;
  bottom: 0;
  right: 0;
  margin: 23px;
  color: #fff;
}
</style>