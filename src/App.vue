<template>
  <div id="app">
    <div class="wrapper-title has-text-centered">
      <h1 class="title">
        <i>Compilador</i>
        U.L.A
      </h1>
    </div>
    <div class="card wrapper-editor">
      <div class="columns">
        <div class="column">
          <editor v-model="input" @lang="clean" />
          <div class="wrapper-button has-text-right">
            <b-button class="mr-10" outlined @click="clean" type="is-warning">Limpiar todo</b-button>
            <b-button :loading="isLoading" @click="compile" type="is-info">Compilar</b-button>
          </div>
        </div>
        <div class="column">
          <run-shell :output="output" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Editor from "./components/Editor.vue";
import RunShell from "./components/RunShell.vue";

const URL_API = "http://localhost:3000";

export default {
  name: "app",
  components: {
    Editor,
    RunShell
  },
  data() {
    return {
      isLoading: false,
      input: {},
      output: ""
    };
  },
  methods: {
    clean() {
      this.input = {};
      this.output = "";
    },
    async compile() {
      let { codes, lang } = this.input;
      if (codes === "") return;

      this.isLoading = true;
      const myConf = {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ codes, lang }),
        mode: "cors"
      };
      try {
        const res = await fetch(URL_API, myConf);
        const data = await res.json();
        this.output = data.result;
        this.isLoading = false;
      } catch (error) {
        this.isLoading = false;
        this.$buefy.toast.open({
          message: "Algo salio mal. ¿El server esta activo ?",
          type: "is-danger"
        });
      }
    }
  }
};
</script>

<style scoped>
.title {
  color: aliceblue;
}
</style>

<style>
.card {
  padding: 1.5rem;
}

.wrapper-button {
  padding-top: 1.5rem;
}

.wrapper-title {
  padding: 1.5rem;
}

.wrapper-editor {
  margin: 1.5rem;
}

.mr-10 {
  margin-right: 10px;
}
</style>
