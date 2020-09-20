<template>
  <v-app>
    <v-data-table
    :headers="headers"
    :items="tasks"
    :search="search"
    :sort-by.sync="sortBy"
    :sort-desc.sync="sortDesc"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar flat color="white">
        <v-toolbar-title>Список задач</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Найдите задачу"
          single-line
          hide-details
        ></v-text-field>
        <v-spacer></v-spacer>
        <v-dialog v-model="dialog" max-width="600px">
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              color="primary"
              dark
              class="mb-2"
              v-bind="attrs"
              v-on="on"
              v-on:click="generate()"
            >Добавить задачу</v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="headline">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <!-- Id -->
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.id" label="Id" clearable></v-text-field>
                  </v-col>
                  <!-- Задача -->
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.name" label="Задача" clearable></v-text-field>
                  </v-col>

                  <!-- Выбор даты -->
                  <v-col cols="12" sm="6" md="4">
                    <v-dialog
                      ref="dialog"
                      v-model="modal"
                      :return-value.sync="editedItem.date"
                      persistent
                      width="290px"
                    >
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field
                          v-model="editedItem.date"
                          label="Выберите дату"
                          readonly
                          v-bind="attrs"
                          v-on="on"
                          clearable
                        ></v-text-field>
                      </template>
                      <v-date-picker v-model="date" scrollable>
                        <v-spacer></v-spacer>
                        <v-btn text color="primary" @click="modal = false">Закрыть</v-btn>
                        <v-btn text color="primary" @click="$refs.dialog.save(date)">Oк</v-btn>
                      </v-date-picker>
                    </v-dialog>
                  </v-col>

                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="close">Закрыть</v-btn>
              <v-btn color="blue darken-1" text @click="save">Сохранить</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }">
      <v-icon
        small
        class="mr-2"
        @click="editItem(item)"
      >
        mdi-pencil
      </v-icon>
      <v-icon
        small
        @click="deleteItem(item)"
      >
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="initialize">Reset</v-btn>
    </template>
  </v-data-table>
  <div class="text-center pt-2">
    <v-btn color="primary" class="mr-2" @click="toggleOrder">Переключить сортировку</v-btn>
    <v-btn color="primary" @click="nextSort">Сортировать следующий столбец</v-btn>
  </div>
  </v-app>
</template>

<script>

export default {
  name: 'App',
data: () => ({
      dialog: false,
      search: '',
      sortBy: 'Calories',
      sortDesc: false,
      date: new Date().toISOString().substr(0, 10),
      modal : false,
      headers: [
        {
          text: 'Id',
          align: 'start',
          value: 'id',
        },
        { text: 'Name', value: 'name' },
        { text: 'Date', value: 'date' },
        { text: 'Actions', value: 'actions', sortable: false },
      ],
      tasks: [],
      editedIndex: -1,
      editedItem: {
        id: '',
        name: '',
        date: '',
      },
      defaultItem: {
        id: '',
        name: '',
        date: '',
      },
    }),

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'Новая задача' : 'Изменить задачу'
      },
    },


    watch: {
      dialog (val) {
        val || this.close()
      },
    },

    created () {
      this.initialize()
    },

    methods: {
      initialize () {
        this.tasks = [
          {
            id: 'id1600551530215',
            name: 'Add text',
            date: '2020-09-01',
          },
          {
            id: 'id1600551530211',
            name: 'delete text',
            date: '2020-09-02',
          },
          {
            id: 'id1600551530212',
            name: 'reverse list',
            date: '2020-09-03',
          },
          {
            id: 'id1600551530213',
            name: 'create app',
            date: '2020-09-04',
          },
          {
            id: 'id1600551530214',
            name: 'delete dict',
            date: '2020-09-05',
          },
          {
            id: 'id1600551530216',
            name: 'input text',
            date: '2020-09-06',
          },
          {
            id: 'id1600551530217',
            name: 'read test',
            date: '2020-09-07',
          },
          {
            id: 'id1600551530218',
            name: 'push git',
            date: '2020-09-08',
          },
          {
            id: 'id1600551530219',
            name: 'translate',
            date: '2020-09-09',
          },
          {
            id: 'id1600551530210',
            name: 'go back',
            date: '2020-09-11',
          },
        ]
      },

      editItem (item) {
        this.editedIndex = this.tasks.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },

      deleteItem (item) {
        const index = this.tasks.indexOf(item)
        confirm('Вы действительно хотите удалить задачу?') && this.tasks.splice(index, 1)
      },

      close () {
        this.dialog = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },

      save () {
        if (this.editedIndex > -1) {
          Object.assign(this.tasks[this.editedIndex], this.editedItem)
        } else {
          this.tasks.push(this.editedItem)
        }
        this.close()
      },

      toggleOrder () {
        this.sortDesc = !this.sortDesc
      },
      nextSort () {
        let index = this.headers.findIndex(h => h.value === this.sortBy)
        index = (index + 1) % this.headers.length 
        this.sortBy = this.headers[index].value
      },


      generate: function() {
        var rand = 'id' + new Date().getTime();
            this.editedItem.id = rand;
            this.defaultItem.id = rand;
        },      
    },
};
</script>
