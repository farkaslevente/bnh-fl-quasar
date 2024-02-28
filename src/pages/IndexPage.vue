<script setup lang="ts">
import { ref } from "vue";
import { useStore } from "../stores/store"; //Imany
import { onMounted } from "vue";
import NewDialogComponent from "../components/NewDialogComponent.vue";
import EditDialogComponent from "../components/EditDialogComponent.vue";

const slide = ref(1);
const store = useStore();
const value = ref(true);

onMounted(() => {
  store.many_GetAll();
  store.one_GetAll();
  store.bnhFl_GetAll();
});

function deleteDocument(): void {
  store.many.document = { id: store.app.selectedMany[0].id };
  store.many_DeleteById();
  store.app.selectedMany = [];
}

function editDocument() {
  store.many.document.id = store.app.selectedMany[0].id;
  store.app.showEditDialog = true;
}

// function filterUpdate() {
//   if (!store.app.filter) {
//     store.app.filter = "";
//   }
//   if (store.app.filter.length > 0) {
//     store.many_Filter();
//   } else {
//     store.many_GetAll();
//   }
// }
</script>

<template>
  <q-page>
    <div class="q-pa-md column items-center justify-start">
      <!-- <q-btn-dropdown color="gray" label="Kategória" text-color="black">
        <q-list>
          <q-item
            v-for="label in store.one.documents"
            v-bind:key="label.id"
            v-close-popup
            clickable
            @click="onItemClick"
          >
            <q-item-section>
              <q-item-label> {{ label.categoryNameField }}</q-item-label>
            </q-item-section>
          </q-item>
        </q-list>
      </q-btn-dropdown> -->
      <q-select
            v-model="store.many.document.categoryId"
            clearable
            emit-value
            label="Kategória"
            map-options
            option-label="categoryNameField"
            option-value="id"
            :options="store.one.documents"                                    
          />
    </div>
    <div class="row">
      <div class="col col-lg-12 col-md-4 col-sm-4"></div>
    </div>

    <!-- Design -->
    <div class="row q-col-gutter-sm justify-center">
      <q-card
        v-for="item in store.bnhFl.documents"
        v-bind:key="item._id"
        class="col-xs-12 col-sm-6 col-md-4 col-lg-3"
        style="margin: 10px"
      >
        <div class="col">
          <div class="text-h6 text-center" style="background-color: #c1e2b3">
            {{ item.cim }}
            <div>{{ item.vetelar }} Ft</div>
          </div>

          <div class="text-subtitle2" style="background-color: bisque">
            <ul style="margin: 0">
              <li>Szín:{{ item.szin }}</li>
              <li>Évjárat: {{ item.evjarat }}</li>
              <li>Hengerűrtartalom: {{ item.hengerurtartalom }} cm <sup>3</sup></li>
              <li>Hirdetés dátuma: {{ item.hirdetes_datum }}</li>
            </ul>
          </div>

          <div class="text-h6" style="background-color: #c1e2b3">
            Leírás
            <div style="font-size: small; line-height: 1.1rem; font-weight: normal">
              {{ item.leiras }}
            </div>
            <div Class="q-pa-md q-gutter-sm">
              <q-toggle v-model="value" color="gray" label="Teljes leírás" left-label size="xs" />
            </div>

            <!-- v-for="src in store.bnhFl.documents" :key="src.kepek" -->
            <div class="q-pa-md" style="background-color: bisque">
              <q-carousel v-model="slide" animated infinite swipeable thumbnails>
                <q-carousel-slide
                  img-src="https://imageio.forbes.com/specials-images/imageserve/5d35eacaf1176b0008974b54/0x0.jpg?format=jpg&crop=4560,2565,x790,y784,safe&height=900&width=1600&fit=bounds"
                  :name="1"
                />
              </q-carousel>
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
      </q-card>
    </div>
    <NewDialogComponent />
    <EditDialogComponent />
    <!-- <q-table
      v-model:selected="store.app.selectedMany"
      card-class="bg-green-9 text-white"
      card-style="padding: 22px;"
      :columns="columns"
      grid
      row-key="id"
      :rows="store.many.documents"
      selection="multiple"
    /> -->
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
