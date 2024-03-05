<script setup lang="ts">
import { ref } from "vue";
import { useStore } from "../stores/store";
import { onMounted } from "vue";
import NewDialogComponent from "../components/NewDialogComponent.vue";
import EditDialogComponent from "../components/EditDialogComponent.vue";

const slide = ref(1);
const store = useStore();
//const value = ref(true);
const defaultCategoryId = ref(1);
const toggleValues = ref<boolean[]>([]);
const selectedCategory = ref();

// const myMap = new Map<number, string>();

onMounted(() => {
  store.many_GetAll();
  store.one_GetAll();
  store.bnhFl_GetAll();

  toggleValues.value = new Array(store.bnhFl.documents.length).fill(true);
  // store.bnhFl.documents.forEach(item => {
  //   getPics(item._id);
  // });
});

function handleSelectionChange(newValue) {
  console.log("New value:");
  console.log("New value:", newValue);
  selectedCategory.value = newValue;
  store.bnhFl_GetByCategoryId(selectedCategory.value);
}
//  function getPics(id){
//    for (let i = 0; i < store.bnhFl.documents.length; index++) {
//     if (id == store.bnhFl.documents[i]._id) {
//        myMap.set(id, store.bnhFl.documents[i].kepek);
//        console.log(id);
//      }

//   }
//  }

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
        v-model="store.many.document.categoryId"
        clearable
        :default-value="defaultCategoryId"
        emit-value
        filled
        label="Kategória"
        map-options
        option-label="categoryNameField"
        option-value="categoryNameField"
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
        v-for="(item, _id) in store.bnhFl.documents"
        v-bind:key="item._id"
        class="col-xs-12 col-sm-6 col-md-3 col-lg-3"
        style="margin: 10px"
      >
        <!-- |
              | v-if="item.kategoria_id == defaultCategoryId"
              V -->
        <div>
          <div class="col">
            <div class="text-h6 text-center" style="background-color: #c1e2b3;">
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
                  {{ item.leiras && item.leiras.length > 120 ? item.leiras.substring(0, 120) + "..." : item.leiras }}
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
                    :default-value="true"
                    :disable="true"
                    label="Teljes leírás"
                    left-label
                    size="xs"
                  />
                </div>

                <div v-else>
                  <q-toggle
                    v-model="toggleValues[_id]"
                    color="gray"
                    :default-value="true"
                    label="Teljes leírás"
                    left-label
                    size="xs"
                  />
                </div>
              </div>

              <!-- v-for="src in store.bnhFl.documents" :key="src.kepek" -->
              <div class="q-pa-md" style="background-color: bisque">
                <q-carousel v-model="slide" animated infinite swipeable thumbnails>
                  <q-carousel-slide
                    img-src="https://imageio.forbes.com/specials-images/imageserve/5d35eacaf1176b0008974b54/0x0.jpg?format=jpg&crop=4560,2565,x790,y784,safe&height=900&width=1600&fit=bounds"
                    :name="1"
                  />
                </q-carousel>
                <!-- <q-carousel v-model="slide" animated infinite swipeable thumbnails>
                  <q-carousel-slide v-for="(value, key) in myMap" :key="key" :img-src="value" :name="key"/>
                </q-carousel> -->
              </div>
              <div style="background-color: bisque">
                <q-btn
                  v-show="true"
                  class="justify-center row"
                  color="green"
                  label="Hirdetés szerkesztése"
                  no-caps
                  @click="editDocument()"
                />
              </div>
            </div>
          </div>
        </div>
        <!-- <div v-else></div> -->
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
