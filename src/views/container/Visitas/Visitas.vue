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
     <div class="row">

        <v-col cols="12"  md="3">
                <v-menu
                  v-model="menu2"
                  :close-on-content-click="false"
                  :nudge-right="40"
                  transition="scale-transition"
                  offset-y
                  min-width="auto"
                >
                  <template v-slot:activator="{ on, attrs }">
                    <v-text-field
                  
                      label="Fecha inicio"
                      prepend-icon="mdi-calendar"
                      readonly
                      v-bind="attrs"
                      v-on="on"
                      
                    ></v-text-field>
                  </template>
                  <v-date-picker
                
                  
                  ></v-date-picker>
                </v-menu>
              </v-col>
  <v-col cols="12"  md="3">
                <v-menu
                  v-model="menu2"
                  :close-on-content-click="false"
                  :nudge-right="40"
                  transition="scale-transition"
                  offset-y
                  min-width="auto"
                >
                  <template v-slot:activator="{ on, attrs }">
                    <v-text-field
                  
                      label="Fecha fin"
                      prepend-icon="mdi-calendar"
                      readonly
                      v-bind="attrs"
                      v-on="on"
                      
                    ></v-text-field>
                  </template>
                  <v-date-picker
                
                  
                  ></v-date-picker>
                </v-menu>
              </v-col>


       <v-btn color="success"
       style="margin-top:20px"
       
                      small>Exportar Excel</v-btn>
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        class="ml-auto"
        :label="searchLabel"
        hide-details
        single-line
        style="max-width: 250px"
      />
</div>
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
            @click="editUser(item)"
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
            @click="editUser(item)"
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


        <v-dialog
      v-model="dialog2"
      persistent
      max-width="600px"
    >
    
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
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-text-field
                  label="Numero de Gafete Exception"
                  type="number"
                  required
                ></v-text-field>
              </v-col>
            
             
            </v-row>
          </v-container>
        
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="blue darken-1"
            text
            @click="dialog2 = false"
          >
            Cerrar
          </v-btn>
          <v-btn
            color="blue darken-1"
            text
            @click="dialog2 = false"
          >
            Guardar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import { getUsers } from "@/api/modules";



const readpolicies = (data) => {
  if (data === true) {
    return "Si";
  } else return "No";
};
export default {
  name: "DashboardDataTables",

  data: () => ({
    selImg: "",
    hidden: false,
    title: "Visitas en Curso",
    headers: [
     
      {
        text: "Departamento",
        value: "area",
      },
      {
        text: "Empresa",
        value: "edificeId",
      },
 {
        text: "Fecha de entrada",
        value: "date",
      },
       {
        text: "Fecha de salida",
        value: "date",
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
    searchLabel: "Buscar",
    dialog: false,
     dialog2: false,
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
       this.items= this.items.filter(item=>item.batch!==0)
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
      this.dialog = true;
      this.selImg = item;
      // console.log(item);
      // this.$router.push({
      //   name: "UsersFrom",
      //   params: {
      //     option: 2, // option 2 to show
      //     userData: item,
      //   },
      // });
    },
    editUser(item) {
        this.dialog2 = true;
      // console.log(item);
      // this.$router.push({
      //   name: "UsersFrom",
      //   params: {
      //     option: 3, // option 3 to edit
      //     userData: item,
      //   },
      // });
    },
    deleteUser(item) {
      console.log(item);
      console.log("Delete");
    },
   async export(){

   }
  },
};
</script>
