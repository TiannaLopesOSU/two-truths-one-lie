<template>
  <!-- Header  -->
  <nav class="navbar-light bg-light">
    <h2 class="p-3 navbar-brand m-2 text-center" href="#">
      Two Truths and a Lie
    </h2>
  </nav>

  <!-- Suspects Name -->
  <div class="container">
    <div class="question-mark mt-5 mb-1">?</div>
    <h3>Guess the lie about {{ questions[currentQuestion].name }}</h3>
  </div>

  <!-- Facts -->
  <div class="container">
    <div class="card m-5 w-75" v-for="fact in getFacts" :key="fact.uniqueId">
      <!-- use button instead -->
      <button
        class="card-body p-"
        @click="handleSubmit(fact)"
        :class="{
          red: fact.backgroundColour == 'red',
          green: fact.backgroundColour == 'green',
          grey: fact.backgroundColour == null,
          shaking: fact.shake == true,
          growing: fact.grow == true,
        }"
      >
        {{ fact.info }}
        <!-- incorrect x svg -->
        <svg
          :class="{
            hidden: fact.backgroundColour !== 'red',
          }"
          xmlns="http://www.w3.org/2000/svg"
          width="16"
          height="16"
          fill="currentColor"
          class="bi bi-file-excel"
          viewBox="0 0 16 16"
        >
          <path
            d="M5.18 4.616a.5.5 0 0 1 .704.064L8 7.219l2.116-2.54a.5.5 0 1 1 .768.641L8.651 8l2.233 2.68a.5.5 0 0 1-.768.64L8 8.781l-2.116 2.54a.5.5 0 0 1-.768-.641L7.349 8 5.116 5.32a.5.5 0 0 1 .064-.704z"
          />
          <path
            d="M4 0a2 2 0 0 0-2 2v12a2 2 0 0 0 2 2h8a2 2 0 0 0 2-2V2a2 2 0 0 0-2-2H4zm0 1h8a1 1 0 0 1 1 1v12a1 1 0 0 1-1 1H4a1 1 0 0 1-1-1V2a1 1 0 0 1 1-1z"
          />
        </svg>
        <!-- check mark svg -->
        <svg
          :class="{
            hidden: fact.backgroundColour !== 'green',
          }"
          xmlns="http://www.w3.org/2000/svg"
          width="16"
          height="16"
          fill="currentColor"
          class="bi bi-check2"
          viewBox="0 0 16 16"
        >
          <path
            d="M13.854 3.646a.5.5 0 0 1 0 .708l-7 7a.5.5 0 0 1-.708 0l-3.5-3.5a.5.5 0 1 1 .708-.708L6.5 10.293l6.646-6.647a.5.5 0 0 1 .708 0z"
          />
        </svg>
      </button>
    </div>
  </div>

  <button
    v-on:click="nextQuestion()"
    class="btn btn-secondary"
    :disabled="nextButtonDisabled"
  >
    Next
    <svg
      xmlns="http://www.w3.org/2000/svg"
      width="16"
      height="16"
      fill="currentColor"
      class="bi bi-arrow-right"
      viewBox="0 0 16 16"
    >
      <path
        fill-rule="evenodd"
        d="M1 8a.5.5 0 0 1 .5-.5h11.793l-3.147-3.146a.5.5 0 0 1 .708-.708l4 4a.5.5 0 0 1 0 .708l-4 4a.5.5 0 0 1-.708-.708L13.293 8.5H1.5A.5.5 0 0 1 1 8z"
      />
    </svg>
  </button>
</template>
<script>
export default {
  name: "QuizView",
  props: {
    jsonData: Array,
  },
  mounted() {},
  computed: {
    getFacts() {
      const shuffledFacts = [];
      const randomIndexArray = [];
      while (
        randomIndexArray.length <
        this.questions[this.currentQuestion].facts.length
      ) {
        let r = Math.floor(
          Math.random() * this.questions[this.currentQuestion].facts.length
        );
        if (randomIndexArray.indexOf(r) === -1) randomIndexArray.push(r);
      }
      randomIndexArray.forEach((index) => {
        shuffledFacts.push(this.questions[this.currentQuestion].facts[index]);
      });
      return shuffledFacts;
    },
  },
  data() {
    return {
      questions: this.getQuestions(),
      currentQuestion: 0,
      nextButtonDisabled: true,
    };
  },
  methods: {
    getQuestions() {
      let questionsFromData = [];
      this.jsonData.forEach((question) => {
        questionsFromData.push({
          name: question.name,
          facts: this.shuffleFacts(question.facts),
          date: question.date,
        });
      });

      return questionsFromData;
    },
    shuffleFacts(facts) {
      return facts;
    },
    nextQuestion() {
      if (this.currentQuestion < this.questions.length - 1) {
        this.nextButtonDisabled = true;
        this.currentQuestion++;
        this.questions[this.currentQuestion].facts.forEach((fact) => {
          fact.backgroundColour = "grey";
        });
      } else {
        this.$emit("finishQuiz");
      }
    },
    handleSubmit(answer) {
      // create audio once only (ex: mounted)
      if (answer.truth === false) {
        const correct = new Audio(require("../assets/correct.mp3"));
        correct.play();
        this.questions[this.currentQuestion].facts[
          answer.uniqueId
        ].backgroundColour = "green";
        this.questions[this.currentQuestion].facts[answer.uniqueId].grow = true;
        this.nextButtonDisabled = false;
      } else {
        const incorrctAudio = new Audio(require("../assets/incorrect.mp3"));
        incorrctAudio.play();
        this.questions[this.currentQuestion].facts[
          answer.uniqueId
        ].backgroundColour = "red";
        this.questions[this.currentQuestion].facts[
          answer.uniqueId
        ].shake = true;
      }
    },
  },
};
</script>
<style scoped>
.question-mark {
  background-color: rgb(228, 221, 14);
  font-size: 4em;
  width: 100px;
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.card-body {
  border-radius: 20px;
}
.grey {
  background-color: #d3ecf0;
}
.green {
  background-color: #abd7ab;
}
.red {
  background-color: #eca4a4;
}

/* shake answer */
.shaking {
  animation: shake 1s;
}
@keyframes shake {
  0% {
    transform: translateX(0);
  }
  25% {
    transform: translateX(-5px) rotate(-5deg);
  }
  50% {
    transform: translateX(5px) rotate(5deg);
  }
  75% {
    transform: translateX(-5px) rotate(-5deg);
  }
  100% {
    transform: translateX(0);
  }
}

.growing {
  animation: grow 1s;
}

@keyframes grow {
  0% {
    transform: scale(1);
  }
  25% {
    transform: scale(1.2);
  }
  100% {
    transform: scale(1);
  }
}

.hidden {
  display: none;
}
</style>
