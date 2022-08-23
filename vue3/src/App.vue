<script setup lang="ts">
import { ref } from "vue";
import type { Ref } from "vue";
import CInput from "./components/CInput.vue";

const components: { [key: string]: any } = {
  input: CInput,
};

const uri = window.location.search.substring(1);
const params = new URLSearchParams(uri);
const formName = params.get("form");
const url = `${window.location.protocol}//${window.location.host}/api/page/form/setting/${formName}`;

const formItems: Ref<{ [key: string]: string }[]> = ref([]);
const formTitle: Ref<string> = ref("");
const loading: Ref<boolean> = ref(false);
const failure: Ref<string> = ref("");

const response = fetch(url);
response
  .then((res) => {
    return res.json();
  })
  .then((data) => {
    formTitle.value = data.name;
    const fieldset = data?.edit?.layout?.fieldset || [];
    const columns = fieldset.length > 0 ? fieldset[0]?.columns : [];
    const mapping = data?.columns || {};
    columns.forEach((col: any) => {
      let column = mapping[col.name] || false;
      if (column?.edit) {
        let name = column.edit.props?.value || "";
        column.edit.label = col.name;
        column.edit.field = name.replace(":", "");
        column.edit.type = components[column.edit.type];
        formItems.value.push(column.edit);
      }
    });

    loading.value = true;
  })
  .catch((err) => {
    console.log("ERR", err);
    failure.value = err.message;
  });
</script>

<template>
  <div v-if="loading">
    <div class="header">{{ formTitle }}</div>
    <form>
      <div class="form-item" v-for="item of formItems" :key="item.label">
        <div class="label">{{ item.label }}</div>
        <component :is="item.type" :name="item.field"></component>
      </div>
      <div class="form-item" v-if="formItems">
        <button class="button" type="button">Submit</button>
      </div>
    </form>
  </div>
  <div v-else-if="failure" class="failure">
    {{ failure }}
  </div>
  <div v-else>
    <div>Loading...</div>
  </div>
</template>

<style scoped>
.header {
  font-size: 1.1rem;
  margin-bottom: 0.5em;
}

.failure {
  color: red;
}
.form-item {
  display: flex;
  width: 400px;
  justify-content: space-between;
  margin-bottom: 15px;
}

.form-item .label {
  font-weight: 400;
  text-align: right;
  width: 80px;
  line-height: 32px;
}

.form-item .button {
  width: 100%;
  height: 32px;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
