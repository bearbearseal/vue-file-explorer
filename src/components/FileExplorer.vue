<!--
Entries shall be [{name: <string>, isFolder: <boolean>} ...]
-->
<template>
  <div class="align-left">
    <button v-on:click="up_folder">
      <i class="arrow up"></i>
    </button>
    <div>Path: ./{{ path }}</div>
    <div id="BrowseWindow">
      <div
        v-for="item in entries"
        v-bind:key="item.name"
        v-bind:class="{ SelectedItem: item.selected }"
        class="Item"
        v-on:click="handle_item_click(item)"
      >
        <img
          class="ItemImage"
          v-if="item.isFolder"
          src="../../Icons/folder_close.jpeg"
        />
        <img
          class="ItemImage"
          v-if="!item.isFolder"
          src="../../Icons/file_icon.png"
        />
        <span class="ItemText">{{ item.name }}</span>
      </div>
    </div>
    <button v-bind:disabled="selectedItem==null" v-on:click="handle_select_click">Select</button>
    <button v-on:click="cancel_click">Cancel</button>
  </div>
</template>

<script>
export default {
  props: ["message"],
  watch: {
    message() {
      switch (this.message.command) {
        case "ChangeContent":
          this.entries = this.message.data.entries;
          this.path = this.message.data.path;
          this.selectedItem = null;
          break;
      }
    },
  },
  emits: ["message"],
  data() {
    return {
      entries: [
        { name: "file1", isFolder: false },
        { name: "file2", isFolder: false },
        { name: "folder1", isFolder: true },
      ],
      path: "",
      selectedItem: null,
      clickTimer: null,
      clickCount: 0,
    };
  },
  methods: {
    get_selected_item() {
      return this.selectedItem;
    },
    handle_item_click(itemClicked) {
      ++this.clickCount;
      if (this.clickCount == 1) {
        this.clickTimer = setTimeout(
          this.handle_item_single_click,
          350,
          itemClicked
        );
      } else {
        this.handle_item_double_click(itemClicked);
      }
    },
    handle_item_single_click(itemClicked) {
      console.log("Single click");
      console.log(itemClicked);
      this.clickCount = 0;
      if (this.selectedItem != null) {
        this.selectedItem.selected = false;
      }
      this.selectedItem = itemClicked;
      this.selectedItem.selected = true;
    },
    handle_item_double_click(itemClicked) {
      let _command = itemClicked.isFolder ? "OpenFolder" : "SelectFile";
      this.$emit("message", {
        command: _command,
        name: this.path + itemClicked.name,
      });
    },
    handle_select_click() {
      let _command = this.selectedItem.isFolder ? "OpenFolder" : "SelectFile";
      this.$emit("message", {
        command: _command,
        name: this.path + this.selectedItem.name,
      });
    },
    up_folder() {
      if (this.path.length === 0) {
        return;
      }
      //remove the last '/'
      let tempPath = this.path.substring(0, this.path.length - 1);
      let index = tempPath.lastIndexOf("/");
      if (index === -1) {
        this.path = "";
      } else {
        this.path = tempPath.substring(0, index);
      }
      this.$emit("message", {
        command: "OpenFolder",
        data: this.path,
      });
    },
    cancel_click() {
      this.$emit("message", {command: "Cancel"});
    }
  },
};
</script>

<style scoped>
#BrowseWindow {
  background-color: white;
  min-width: 300px;
  max-width: 600px;
  min-height: 200px;
  max-height: 400px;
  margin-bottom: 4px;
  padding-top: 2px;
}

.Item {
  text-align: left;
  height: 28px;
}

.Item:hover {
  background-color: lightgray;
}

.ItemImage {
  height: 24px;
  width: 24px;
  object-fit: fill;
  vertical-align: middle;
}

.SelectedItem {
  border: solid;
}

.arrow {
  border: solid black;
  border-width: 0 3px 3px 0;
  display: inline-block;
  padding: 3px;
}

.up {
  transform: rotate(-135deg);
  -webkit-transform: rotate(-135deg);
}

.align-left {
  text-align: left;
}

button {
  margin-right:2px;
}
</style>