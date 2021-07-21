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
        <template v-slot:item.delete="{ item }">
          <v-btn
            :key="3"
            color="secondary"
            fab
            class="px-1 ml-1"
            x-small
            @click="deleteUser(item)"
          >
            <v-icon small v-text="'mdi-delete'" />
          </v-btn>
        </template>
        <template v-slot:item.actions="{ item }">
          <v-btn outlined color="white" :key="1" @click="showUser(item.firm)">
            <v-img width="30px" height="30px" :src="item.firm"></v-img>
          </v-btn>
        </template>
        <template v-slot:item.document="{ item }">
          <v-btn
            outlined
            color="white"
            :key="1"
            @click="showUser(item.document)"
          >
            <v-img width="30px" height="30px" :src="item.document"></v-img>
          </v-btn>
        </template>
        <template v-slot:item.picture="{ item }">
          <v-btn outlined color="white" :key="1" @click="showUser(item.pic)">
            <v-img width="30px" height="30px" :src="item.pic"></v-img>
          </v-btn>
        </template>
        <template v-slot:item.entry="{ item }">
          <v-btn
            small
            outlined
            color="primary"
            :key="1"
            @click="setIdEntry(item)"
          >
            <v-icon>mdi-clock</v-icon>
            Registrar Entrada
          </v-btn>
        </template>
        <template v-slot:item.exit="{ item }">
          <v-btn
            small
            outlined
            color="primary"
            :key="1"
            @click="setIdEntry(item)"
          >
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
    <div class="text-center">
      <v-dialog v-model="dialog" width="500">
        <v-card>
          <v-img width="auto" :src="selImg"></v-img>

          <v-divider></v-divider>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="primary" text @click="dialog = false"> Cerrar </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </div>

    <v-dialog v-model="dialog2" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="text-h5">Registro</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-text-field
                  label="Numero de Gafete"
                  v-model="entry.batch"
                  type="number"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-text-field
                  label="Numero de Gafete Exception"
                  type="number"
                  v-model="entry.batch_Ex"
                  required
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="dialog2 = false">
            Cerrar
          </v-btn>
          <v-btn color="blue darken-1" text @click="setNewEntry()">
            Guardar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import { getUsers, setEntry, DELETEV, sentEmail } from "@/api/modules";
const API_URL_BACKEND = "https://jfalcondev.azurewebsites.net";
export default {
  name: "DashboardDataTables",

  data: () => ({
    selImg: "",
    hidden: false,
    title: "Visitas programadas",
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
      {
        sortable: false,
        text: "Cancelación",
        value: "delete",
      },
    ],
    items: [
      {
        name: "test",
      },
    ],
    search: undefined,
    searchLabel: "Buscar",
    dialog: false,
    dialog2: false,
    entry: {},
    exit: {},
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
      this.items = this.items.filter((item) => item.batch === 0);
      // } else {
      //   console.log(serviceResponse)

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
      this.dialog = true;
      this.selImg = item;
    },
    setIdEntry(items) {
      this.entry.id = items.id;
      this.dialog2 = true;
    },
    async setNewEntry() {
      this.dialog2 = false;
      let serviceResponse = await setEntry(this.entry);
      const params = { text: "Nueva entrada registrada con exito" };
      window.getApp.$emit("SHOW_MESSAGE", params);
      this.loadUsersData();
      // if (serviceResponse.ok === 1) {
      console.log(serviceResponse);
      // this.items = serviceResponse.data;
    },
    async deleteUser(item) {
      if (confirm("¿SEGURO QUE DESEA CANCELAR ESTA VISITA?")) {
        // Save it!
        let serviceResponse = await DELETEV(item.id);
        console.log(serviceResponse);
        const params = { text: "Visita cancelada con exito" };
        window.getApp.$emit("SHOW_MESSAGE", params);
        this.loadUsersData();
        //====================================
        // let body = { email: item.email };
        // let serviceResponse2 = await sentEmail(body)
        // console.log(serviceResponse2);
        // var form_data = new FormData();

        // for (var key in item) {
        //   form_data.append(key, item[key]);
        // }
        // fetch(API_URL_BACKEND + "/Visit/SendEmail", {
        //   "Content-Type": "application/json",
        //   method: "POST", // *GET, POST, PUT, DELETE, etc.
        //   body: form_data, // body data type must match "Content-Type" header
        // })
        //   .then((response) => response.json())
        //   .then((data) => {
        //     console.log(data);
        //   })
        //   .catch(function (error) {
        //     this.loading = false;
        //     console.log(
        //       "Hubo un problema con la petición Fetch:" + error.message
        //     );
        //   });
      } else {
        // Do nothing!
        console.log("Thing was not saved to the database.");
      }
      console.log(item);
      console.log("Delete");
    },
  },
};
</script>
