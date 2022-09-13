<script setup>
import { ref, computed } from "vue";
import data from "./questions.json";

const questions = ref(data);
const quizCompleted = ref(false);
const quizSubmitted = ref(false);

const setAnswer = (index, answer) => {
  questions.value[index].selected = answer
};

const score = computed(() => {
  let numCorrect = 0;
  questions.value.map((q) => (q.selected === q.answer ? numCorrect++ : null));
  return numCorrect;
});

const checkQuiz = (evt) => {
  evt.preventDefault();
  quizSubmitted.value = true;
  const finalScore = document.querySelector('.quiz-info')
  const errorMessage = document.querySelector('.error')
  const resultStatus = document.querySelectorAll('.result-status')
  const quizInputs = document.querySelectorAll('.quiz-input')

  let unanswered = questions.value.filter((q) => q.selected == null);
  if (unanswered.length === 0) {
    quizCompleted.value = true
    finalScore.style.display = 'flex'
    errorMessage.style.display = 'none'
    resultStatus.forEach(result => result.style.display = 'block')
    quizInputs.forEach(input => input.disabled = true)
  } else {
    errorMessage.style.display = 'block'
  }
};
</script>

<template>
  <main class="app">
    <div class="header">
      <p>Quiz Application</p>
    </div>
    <section class="quiz">
      <h1 class="quiz-title">Quiz 1 - Random Trivia</h1>
      <div class="quiz-info">
        <h3 id="result-title">Results</h3>
        <h3>You got {{ score }} / {{ questions.length }} questions correct</h3>
        <h3 id="result-score">{{ (score / questions.length) * 100 }} %</h3>
      </div>

      <form @submit="checkQuiz">
        <div
          v-for="(question, index) in questions"
          class="questions"
          :class="` 
              ${
                question.selected == null && quizSubmitted ? 'unanswered' : ''
              } 
              ${
                quizCompleted
                  ? question.selected === question.answer
                    ? 'correct'
                    : 'incorrect'
                  : ''
              }
            `"
        >
          <p>{{ index + 1 + ". " + question.question }}</p>

          <label 
            v-for="(option, i) in question.options"
            :class="`
              ${
                quizCompleted
                  ? question.selected != question.answer && question.answer === i
                    ? 'answer'
                    : ''
                  : ''
              }
            `"
          >
            <input
              class="quiz-input"
              type="radio"
              :value="i"
              v-model="question.selected"
              @change="setAnswer(index, i)"
            />
            {{ option }}
          </label>
          <p 
            class="result-status"
            :class="`
              ${
                quizCompleted && question.selected === question.answer
                  ? 'green'
                  : 'red'
              }
            `"
          >
            {{
              quizCompleted && question.selected === question.answer 
                ? `Correct` 
                : `Wrong`
            }}
          </p>
        </div>
        <input type="submit" id="submit-button" />
        <p class="error">Answer all questions before submitting. Unanswered questions are displayed in yellow.</p>
      </form>
    </section>
    
  </main>
</template>

<style scoped>

</style>
