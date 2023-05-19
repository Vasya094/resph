<template>
  <div>
    <div class="container">
      <div class="flex flex-row justify-between">
        <h1 class="text-2xl self-center">List of Projects</h1>
        <button
          class="
            flex flex-row
            justify-center
            items-center
            p-4
            text-white
            gap-3
            w-22
            h-16
            bg-blue-500
            hover:bg-blue-400
            rounded-md
          "
          @click="createNewItem"
        >
          Add New Project
        </button>
      </div>
      <ul>
        <li
          v-for="(item, index) in itemList"
          :key="item.id"
          class="
            flex flex-col
            items-start
            px-12
            gap-8
            bg-white
            border border-gray-300
            rounded-lg
            shadow-outline
          "
        >
          Project {{ item.id }}
          <div class="w-full">
            <div
              v-for="subItem in item.themes"
              :key="subItem"
              class="
                flex flex-col
                items-start
                px-12
                my-2
                bg-white
                border border-gray-300
                rounded-lg
                shadow-outline
              "
            >
              {{ subItem }}
            </div>
          </div>
          <div class="flex flex-row">
            <button
              class="
                flex flex-row
                justify-center
                items-center
                p-4
                text-white
                gap-3
                h-8
                bg-gamik-500
                hover:bg-gamik-400
                rounded-md
              "
              @click="editItem(item.id)"
            >
              <img alt="logo" class="w-3" src="./../assets/icons/pen.svg" />
              Edit
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
              @click="deleteItem(item.id)"
            >
              <img alt="logo" class="w-5" src="./../assets/icons/delete.svg" />
              Delete
            </button>
          </div>
        </li>
      </ul>
    </div>
    <project-form-modal
      :show="showModal"
      :project-id="projectIdToEdit"
      @close="showModal = false"
      @update-list="$fetch"
    />
    <notifications group="err" position="top right" classes="bg-red-100">
      <template slot="body" slot-scope="{ item }">
        <div class="my-notification flex flex-row p-4 rounded-md bg-red-100 text-white">
          <img alt="logo" class="w-4 mr-4" src="./../assets/icons/error.svg" />
          <div>
            <h4 class="title">
              {{ item.title }}
            </h4>
            <div>
              <p>
                {{ item.text }}
              </p>
            </div>
          </div>
        </div>
      </template>
    </notifications>
    <notifications group="suc" position="top right" classes="bg-green-100">
      <template slot="body" slot-scope="{ item }">
        <div class="my-notification p-4 rounded-md bg-red-100 text-white">
          <h4 class="title">
            {{ item.title }}
          </h4>
          <div>
            <p>
              {{ item.text }}
            </p>
          </div>
        </div>
      </template>
    </notifications>
  </div>
</template>

<script>
import ProjectFormModal from "../components/ProjectFormModal.vue";

export default {
  data() {
    return {
      itemList: [],
      expandedLines: [],
      showModal: false,
      projectIdToEdit: 0,
    };
  },
  components: {
    ProjectFormModal,
  },
  async fetch() {
    let { data } = await this.$axios.get("/neural/all");
    this.itemList = data;
  },
  methods: {
    editItem(index) {
      this.projectIdToEdit = index;
      this.showModal = true;
    },
    deleteItem(index) {
      this.$axios
        .delete(`/neural/${index}`)
        .then(() => {
          this.$notify({
            group: "suc",
            title: "Successfully",
            text: "Project deleted successfully",
            type: "warn",
          });
        })
        .catch((e) => {
          console.log("err");
          this.$notify({
            group: "err",
            title: "An error has occurred",
            text: e.message,
            type: "warn",
          });
        });
    },
    createNewItem() {
      this.projectIdToEdit = 0;
      this.showModal = true;
    },
  },
};
</script>

<style>
.container {
  max-width: 600px;
  margin: auto;
}
button {
  margin: 10px;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  margin-bottom: 10px;
  border: 1px solid #ccc;
  padding: 10px;
}
li ul {
  margin-top: 5px;
  margin-bottom: 15px;
  padding-left: 20px;
  border: 1px solid #ccc;
}
</style>
