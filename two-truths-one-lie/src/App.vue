<template>
  <div>
    <div v-if="isLoaded === true">
      <Introduction
        v-if="view !== 'startQuiz' && view !== 'endQuiz'"
        @startQuiz="view = 'startQuiz'"
      />

      <Quiz
        v-else-if="view === 'startQuiz' && view !== 'endQuiz'"
        :jsonData="jsonData"
        @finishQuiz="view = 'endQuiz'"
      />

      <EndingPage v-else />
    </div>
    <div v-else>
      <LoadingBar />
    </div>
  </div>
</template>

<script>
import Introduction from "./components/IntroductionView.vue";
import Quiz from "./components/QuizView.vue";
import LoadingBar from "./components/LoadingBar.vue";
import EndingPage from "./components/EndingPage.vue";
export default {
  name: "App",
  components: { LoadingBar, Introduction, Quiz, EndingPage },
  created() {
    const url =
      "https://docs.google.com/spreadsheets/d/1e8J1lgyWgekhPsZjgL5Fsh4Atkf5S9JjyTGQFs0pO7w/gviz/tq?tqx=out:json&sheet=Locations";
    let ref = this;
    fetch(url)
      .then(function (response) {
        return response.text();
      })
      .then(function (data) {
        const r = data.match(
          /google\.visualization\.Query\.setResponse\(([\s\S\w]+)\)/
        );
        if (r && r.length == 2) {
          const obj = JSON.parse(r[1]);
          const table = obj.table;
          const rows = table.rows.map(({ c }) =>
            c.map((e) => (e ? e.v || "" : ""))
          );
          const theData = rows.map((row) => {
            let datum = {
              date: row[0],
              name: row[1],
              facts: [
                {
                  info: row[2],
                  truth: true,
                  backgroundColour: null,
                  uniqueId: 0,
                  shake: false,
                  grow: false,
                },
                {
                  info: row[3],
                  truth: true,
                  backgroundColour: null,
                  uniqueId: 1,
                  shake: false,
                  grow: false,
                },
                {
                  info: row[4],
                  truth: false,
                  backgroundColour: null,
                  uniqueId: 2,
                  shake: false,
                  grow: false,
                },
              ],
            };
            return datum;
          });
          ref.jsonData = [...theData];
          ref.isLoaded = true;
        }
      });
  },
  data() {
    return {
      jsonData: [],
      isLoaded: false,
      view: "start",
    };
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
