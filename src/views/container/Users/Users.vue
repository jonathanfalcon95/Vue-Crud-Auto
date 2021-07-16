<template>
  <v-container id="data-tables" tag="section">
    <base-material-card
      color="primary"
      icon="mdi-account"
      inline
      class="px-5 py-3"
    >
      <template v-slot:after-heading>
        <div class="display-2 font-weight-light">
          {{ title }}
        </div>
      </template>

      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        class="ml-auto"
        :label="searchLabel"
        hide-details
        single-line
        style="max-width: 250px"
      />

      <v-divider class="mt-3" />

      <v-data-table
        :headers="headers"
        :items="items"
        :search.sync="search"
        :sort-by="['name', 'office']"
        :sort-desc="[false, true]"
        multi-sort
        class="elevation-1"
      >
        <template v-slot:item.actions="{ item }">
          <v-btn outlined color="white" :key="1" @click="showUser(item)">
            <v-img width="30px"  height="30px" :src="item.firm"></v-img>
          </v-btn>
        </template>
         <template v-slot:item.document="{ item }">
          <v-btn outlined color="white" :key="1" @click="showUser(item)">
            <v-img width="30px"  height="30px" :src="item.document"></v-img>
          </v-btn>
        </template>
          <template v-slot:item.picture="{ item }">
          <v-btn outlined color="white" :key="1" @click="showUser(item)">
            <v-img width="30px" height="30px" :src="item.pic"></v-img>
          </v-btn>
        </template>
         <template v-slot:item.entry="{ item }">
          <v-btn  small  outlined  color="primary" :key="1" @click="editUser(item)">
             <v-icon>mdi-clock</v-icon>
            Registrar Entrada
          </v-btn>
        </template>
        <template v-slot:item.exit="{ item }">
          <v-btn  small  outlined  color="primary" :key="1" @click="editUser(item)">
             <v-icon>mdi-clock</v-icon>
            Registrar Salida
          </v-btn>
        </template>
      </v-data-table>
      <v-card-text style="height: 100px; position: relative">
        <!-- <v-fab-transition>
          <v-btn
            fab
            dark
            large
            color="primary"
            fixed
            right
            bottom
            @click="createUser"
          >
            <v-icon>mdi-plus</v-icon>
          </v-btn>
        </v-fab-transition> -->
      </v-card-text>
    </base-material-card>
  </v-container>
</template>

<script>
import { getUsers } from "@/api/modules";
import i18n from "@/i18n";
import userjson from "./user.json";
const readpolicies = (data) => {
  if (data === true) {
    return "Si";
  } else return "No";
};
export default {
  name: "DashboardDataTables",

  data: () => ({
    hidden: false,
    title: "Visitas",
    headers: [
       {
        sortable: false,
        text: "Fecha de entrada",
        value: "entry",
      },
       {
        sortable: false,
        text: "Fecha de salida",
        value: "exit",
      },
      {
        text: "Departamento",
        value: "area",
      },
      {
        text: "Empresa",
        value: "edificeId",
      },

      {
        text: "Motivo",
        value: "reason",
      },
      {
        text: "Nombre del Autorizador",
        value: "reason",
      },
      {
        text: "Nombre del visitante",
        value: "name",
      },

      {
        sortable: false,
        text: "Firma",
        value: "actions",
      },
       {
        sortable: false,
        text: "Identidad",
        value: "document",
      },
       {
        sortable: false,
        text: "Foto Visita",
        value: "picture",
      },
    ],
    items: [
      {
        name: "test",
      },
    ],
    search: undefined,
    searchLabel: "undefined",
  }),
  async mounted() {
    // window.getApp.$emit("SHOW_ERROR", "34534535")
    this.loadUsersData();
  },
  methods: {
    async loadUsersData() {
      console.log("mounted");
      let serviceResponse = await getUsers();
      // if (serviceResponse.ok === 1) {
      console.log(serviceResponse);
      this.items = serviceResponse.data;
      // } else {
      //   console.log(serviceResponse)
      //   const params = { text: serviceResponse.message.text }
      //   window.getApp.$emit('SHOW_ERROR', params)
      // }
    },
    createUser() {
      console.log("create");
      this.$router.push({
        name: "UsersFrom",
        params: {
          option: 1, // option 1 to create
        },
      });
    },
    showUser(item) {
      console.log(item);
      this.$router.push({
        name: "UsersFrom",
        params: {
          option: 2, // option 2 to show
          userData: item,
        },
      });
    },
    editUser(item) {
      console.log(item);
      this.$router.push({
        name: "UsersFrom",
        params: {
          option: 3, // option 3 to edit
          userData: item,
        },
      });
    },
    deleteUser(item) {
      console.log(item);
      console.log("Delete");
    },
  },
};
</script>
