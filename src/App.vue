<template>
  <div v-if="showFileBrowser" class="middle-popup">
    <FileExplorer
      v-bind:message="message2FileExplorer"
      v-on:message="catch_message"
    />
  </div>
  <button v-on:click="showFileBrowser = !showFileBrowser">show</button>
  <button v-on:click="change_content_to_1">Content 1</button>
  <button v-on:click="change_content_to_2">Content 2</button>
</template>

<script>
import FileExplorer from "./components/FileExplorer.vue";
export default {
  name: "App",
  components: {
    FileExplorer,
  },
  data() {
    return {
      message2FileExplorer: null,
      showFileBrowser: false,
    };
  },
  methods: {
    catch_message(message) {
      console.log("Message from file explorer");
      console.log(message);
      switch(message.command) {
        case "Cancel":
          this.showFileBrowser = false;
          break;
      }
    },
    change_content_to_1() {
      this.message2FileExplorer = {
        command: "ChangeContent",
        data: {
          entries: [
            { name: "a file", isFolder: false },
            { name: "a folder", isFolder: true },
            { name: "another file", isFolder: false },
            { name: "another folder", isFolder: true },
          ],
          path: "a random folder/",
        },
      };
    },
    change_content_to_2() {
      this.message2FileExplorer = {
        command: "ChangeContent",
        data: {
          entries: [
            { name: "b file", isFolder: false },
            { name: "b folder", isFolder: true },
            { name: "bnother file", isFolder: false },
            { name: "bnother folder", isFolder: true },
          ],
          path: "a random folder/",
        },
      };
    },
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

.middle-popup {
  background-color: lightblue;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  padding: 5px;
}
</style>
