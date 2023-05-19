<template>
  <div class="fixed inset-0 z-40 flex items-center justify-center" v-if="show">
    <div class="fixed inset-0 bg-black opacity-50"></div>
    <div class="bg-white p-6 z-50 rounded shadow-lg">
      <div>
        <div>
          <input
            class="
              border
              rounded-lg
              py-2
              px-4
              focus:outline-none focus:ring-2 focus:ring-blue-500
            "
            type="text"
            v-model="newText"
            @keyup.enter="addText"
          />
          <button
            class="
              text-white
              h-12
              px-4
              bg-gamik-500
              hover:bg-gamik-400
              rounded-md
            "
            @click="addText"
          >
            Add Tag to Project
          </button>
          <ul class="my-4">
            <li v-for="(text, index) in themes" :key="index">
              <div class="flex flex-row justify-between">
                <span> {{ `${index + 1} - ${text}` }} </span>
                <img
                  class="cursor-pointer w-3"
                  alt="logo"
                  src="./../assets/icons/black-close.svg"
                  @click="deleteTagByIndex(index)"
                />
              </div>
            </li>
          </ul>
        </div>
      </div>
      <div class="flex flex-row justify-end">
        <button
          v-if="projectId"
          class="
            flex flex-row
            justify-center
            items-center
            p-4
            text-white
            gap-3
            h-8
            bg-blue-500
            hover:bg-blue-400
            rounded-md
          "
          @click="selectFile"
        >
          <img alt="logo" class="w-4" src="./../assets/icons/icons8-upload-50.png" />
          Upload
        </button>
        <button
          class="
            flex flex-row
            justify-center
            items-center
            p-4
            text-white
            gap-3
            h-8
            bg-blue-500
            hover:bg-blue-400
            rounded-md
          "
          @click="saveProject"
        >
          <img alt="logo" class="w-3" src="./../assets/icons/pen.svg" />
          Save
        </button>
        <button
          class="
            flex flex-row
            justify-center
            items-center
            p-4
            text-white
            gap-3
            h-8
            bg-red-500
            hover:bg-red-400
            rounded-md
          "
          @click="close"
        >
          <img alt="logo" class="w-3" src="./../assets/icons/close.svg" />
          Close
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    show: {
      type: Boolean,
      required: true,
    },
    projectId: {
      type: Number,
      required: false,
    },
  },
  data() {
    return {
      newText: "",
      themes: [],
    };
  },
  emits: ["updateList"],
  methods: {
    close() {
      this.$emit("close");
      this.themes = [];
    },
    addText() {
      this.themes.push(this.newText);
      this.newText = "";
    },
    deleteTagByIndex(ind) {
      this.themes.splice(ind, 1);
    },
    selectFile() {
      const input = document.createElement("input");
      input.type = "file";
      input.click();
    },
    getProjectInfo() {
      this.$axios
        .get(`/neural/${this.projectId}`)
        .then(({ data }) => {
          this.themes = data.themes;
        })
        .catch(() => {
          this.close();
        });
    },
    saveProject() {
      const id = this.projectId || Math.floor(Math.random() * 9999) + 1;

      this.$axios[this.projectId ? "patch" : "post"]("/neural", {
        id,
        themes: this.themes,
      })
        .then(() => {
          this.themes = [];
          this.$emit("updateList");
          this.$notify({
            group: "suc",
            title: "Successfully",
            text: "Project created successfully",
            type: "warn",
          });
        })
        .catch((e) => {
          this.close();
          this.$notify({
            group: "err",
            title: "An error has occurred",
            text: e.message,
            type: "warn",
          });
        });
    },
  },
  watch: {
    show: function (newVal) {
      if (newVal && this.projectId) {
        this.getProjectInfo();
      }
    },
  },
};
</script>
