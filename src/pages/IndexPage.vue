<script setup lang="ts">
import { ref, computed } from "vue";
import { useStore } from "../stores/store";
import { onMounted } from "vue";
import NewDialogComponent from "../components/NewDialogComponent.vue";
import EditDialogComponent from "../components/EditDialogComponent.vue";

const slide = ref(1);
const store = useStore();
const defaultCategoryId = ref(1);
const toggleValues = ref<boolean[]>([]);
let categoryName = "Személyautó";

const filteredDocuments = computed(() => {
  return store.bnhFl.documents.filter((item) => item.kategoria_id === defaultCategoryId.value);
});

onMounted(() => {
  toggleValues.value = new Array(store.bnhFl.documents.length).fill(false);
  console.log(toggleValues.value);
  store.many_GetAll();
  store.one_GetAll();
  store.bnhFl_GetAll();
});

function handleSelectionChange(newValue) {
  store.one.documents.forEach((element) => {
    if (newValue == element._id) {
      categoryName = element.nev;
      defaultCategoryId.value = element._id;
      return;
    }
  });
  if (categoryName !== "") {
    store.bnhFl_GetByCategoryId(categoryName);
  }
}

function shortenString(source_string, max_length) {
  var short = source_string.substr(0, max_length);
  if (/^\S/.test(source_string.substr(max_length))) return short.replace(/\s+\S*$/, "") + "...";
  return short;
}

function deleteDocument(): void {
  store.many.document = { id: store.app.selectedMany[0].id };
  store.many_DeleteById();
  store.app.selectedMany = [];
}

function editDocument() {
  store.many.document.id = store.app.selectedMany[0].id;
  store.app.showEditDialog = true;
}
</script>
<template>
  <q-page>
    <div class="q-pa-md column items-center justify-start">
      <q-select
        v-model="store.one.document.categoryNameField"
        clearable
        emit-value
        filled
        label="Kategória"
        map-options
        option-label="nev"
        option-value="_id"
        :options="store.one.documents"
        @update:model-value="(newValue) => handleSelectionChange(newValue)"
      />
    </div>
    <div class="row">
      <div class="col col-lg-12 col-md-4 col-sm-4"></div>
    </div>

    <!-- Design -->
    <div class="row q-col-gutter-sm justify-center">
      <q-card
        v-for="(item, _id) in filteredDocuments"
        v-bind:key="item._id"
        class="col-xs-12 col-sm-6 col-md-3 col-lg-3"
        style="margin: 10px"
      >
        <div>
          <div class="col">
            <div class="text-h6 text-center" style="background-color: #c1e2b3">
              {{ item.cim }} - {{ item.vetelar }} Ft
            </div>

            <div class="text-subtitle2" style="background-color: bisque">
              <ul style="margin: 0">
                <li>
                  Szín: <b>{{ item.szin }}</b>
                </li>
                <li>
                  Évjárat: <b>{{ item.evjarat }}</b>
                </li>
                <li>
                  Hengerűrtartalom: <b>{{ item.hengerurtartalom }} cm <sup>3</sup></b>
                </li>
                <li>
                  Hirdetés dátuma: <b>{{ item.hirdetes_datum }}</b>
                </li>
              </ul>
            </div>

            <div class="text-h6" style="background-color: #c1e2b3">
              <div style="font-size: small; line-height: 1.1rem; font-weight: normal; padding: 10px">
                <br />
                <div v-if="toggleValues[_id] == false">
                  {{ shortenString(item.leiras, 200) }}
                </div>
                <div v-else>
                  {{ item.leiras }}
                </div>
              </div>
              <div Class="q-pa-md q-gutter-sm">
                <div v-if="item.leiras && item.leiras.length < 120">
                  <q-toggle
                    v-model="toggleValues[_id]"
                    class="custom-toggle"
                    color="gray"
                    :disable="true"
                    label="Teljes leírás"
                    left-label
                    size="xs"
                  />
                </div>

                <div v-else>
                  <q-toggle v-model="toggleValues[_id]" color="gray" label="Teljes leírás" left-label size="xs" />
                </div>
              </div>             
              <div class="q-pa-md" style="background-color: bisque">
                <q-carousel v-model="slide" animated infinite swipeable thumbnails>
                  <!-- Loop through the images in the 'kepek' array -->                 
                  <template v-for="(image, index) in item.kepek" :key="index">
                    <q-carousel-slide :img-src="image" :name="index" />
                  </template>
                
                </q-carousel>
              </div>
              <div style="background-color: bisque">
                <q-btn
                  v-show="true"
                  class="justify-center"
                  color="green"
                  label="Hirdetés szerkesztése"
                  no-caps
                  @click="editDocument()"
                />
              </div>
            </div>
          </div>
        </div>
      </q-card>
    </div>
    <NewDialogComponent />
    <EditDialogComponent />
    <div class="row justify-center q-mt-sm q-gutter-md">
      <q-btn color="green" label="Új hirdetés" no-caps @click="store.app.showNewDialog = true" />
      <q-btn
        v-show="store.app.selectedMany.length == 1"
        color="red"
        label="Hirdetés törlése"
        no-caps
        @click="deleteDocument()"
      />
      <q-btn
        v-show="store.app.selectedMany.length == 1"
        color="red"
        label="Hirdetés szerkesztése"
        no-caps
        @click="editDocument()"
      />
      <q-btn
        v-show="store.app.selectedMany.length != 0"
        color="green"
        label="Kijelölés törlése"
        no-caps
        @click="store.app.selectedMany = []"
      />
    </div>
  </q-page>
</template>

<style lang="scss" scoped>
h2 {
  font-size: 3vw;
}
</style>
