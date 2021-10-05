<template>
  <v-data-table
    :headers="headers"
    :items="books"
    sort-by="name"
    class="elevation-1"
  >
    <template #top>
      <v-snackbar
        v-model="liked"
      >
        You liked the book {{ likedBookName }}

        <template #action="{ attrs }">
          <v-btn
            color="pink"
            text
            v-bind="attrs"
            @click="liked = false"
          >
            Close
          </v-btn>
        </template>
      </v-snackbar>
      <v-alert type="error" v-show='mistake'>
        Delete operation is not successful.
      </v-alert>
      <v-alert type="success" v-show='success'>
        I'm an error alert.
      </v-alert>
      <v-toolbar
        flat
      >
        <v-toolbar-title>Book List</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        <v-spacer></v-spacer>
        <v-dialog
          v-model="dialog"
          max-width="500px"
        >
          <template #activator="{ on, attrs }">
            <v-btn
              color="primary"
              dark
              class="mb-2"
              v-bind="attrs"
              v-on="on"
            >
              New Item
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.name"
                      label="Book name"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-select v-model='editedItem.authorName' label='Author Name' :items='authorNames' />
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-select v-model='editedItem.type' label='Type' :items='bookType'/>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.price"
                      label="Price"
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
                @click="close"
              >
                Cancel
              </v-btn>
              <v-btn
                color="blue darken-1"
                text
                @click="save"
              >
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:[`item.actions`]="{ item }">
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
      <v-icon
        small
        :color=item.iconColor
        @click="likeItem(item)"
      >
        {{ item.icon}}
      </v-icon>
    </template>
  </v-data-table>
</template>

<script>
import axios from 'axios'

export default {
  name : 'Books',
  data: () => ({
    dialog: false,
    dialogDelete: false,
    icon : "mdi-heart",
    iconColor : 'white',
    headers: [
      {
        text: 'Book Name',
        align: 'start',
        sortable: false,
        value: 'name',
      },
      { text: 'Author Name', value: 'authorName' },
      { text: 'Type', value: 'type' },
      { text: 'Price', value: 'price' },
      { text: 'Actions', value: 'actions', sortable: false },
    ],
    authors : [],
    authorNames : [],
    bookType : [
      "Undefined",
      "Adventure",
      "Biography",
      "Dystopia",
      "Fantastic",
      "Horror",
      "Science",
      "Science Fiction",
      "Poetry"
    ],
    books :[],
    favouriteBooks : [],
    editedIndex: -1,
    editedItem: {
      name: '',
      authorName: '',
      type: 0,
      price: 0,
    },
    newBook : {
      name : "",
      type : 0,
      publishDate : "2021-08-30",
      price: 0,
      authorId: "",
      isFavourite : true
    },
    defaultItem: {
      name: '',
      authorName: '',
      type: 0,
      price: 0,
    },
    ID : '',
    mistake : false,
    success : false,
    liked : false,
    likedBookName : 'Nothing'
  }),

  computed: {
    formTitle () {
      return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
    },
  },

  watch: {
    dialog (val) {
      val || this.close()
    },
    dialogDelete (val) {
      val || this.closeDelete()
    },
  },

  created () {
    axios.get("https://localhost:44319/abp/Swashbuckle/SetCsrfCookie", {withCredentials : true}).then(response => {console.log(response)});
    axios.get("https://localhost:44319/api/app/favourite-book", { 'withCredentials' : 'true' }).then(resp => {
      const book = resp.data.items;
      if(typeof(resp.data) === 'object'){
        for (let i = 0; i < book.length; i++){
          this.favouriteBooks.push(book[i].bookId);
        }
        console.log(this.favouriteBooks);
      }
    });
    axios.get("https://localhost:44319/api/app/book", { 'withCredentials' : 'true' }).then(resp => {
      const book = resp.data.items;
      if(typeof(resp.data) === 'object'){
        for (let i = 0; i < book.length; i++){
          if (this.favouriteBooks.includes(book[i].id)){
            this.books.push({ ...book[i], type : this.bookType[book[i].type], icon : this.icon, iconColor : 'red'});
          } else {
            this.books.push({ ...book[i], type : this.bookType[book[i].type], icon : this.icon, iconColor : 'white'});
          }
        }
      }else {
        console.log(resp)
        location.href = 'https://localhost:44349/';
      }
    });
    axios.get("https://localhost:44319/api/app/book/author-lookup", { 'withCredentials' : 'true' }).then( response =>{
      const author = response.data.items;
      for (let i = 0; i < author.length; i++){
        this.authors.push(author[i]);
        this.authorNames.push(author[i].name);
      }
    });
  },

  methods: {

    editItem (item) {
      this.editedIndex = this.books.indexOf(item)
      this.editedItem = Object.assign({}, item)    // For showing existing data
      this.dialog = true
      this.ID = item.id;
    },

    deleteItem (item) {
      this.editedIndex = this.books.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialogDelete = true
      this.ID = item.id;
    },

    async deleteItemConfirm () {
      await axios.delete("https://localhost:44319/api/app/book/" + this.ID, {'withCredentials' : 'true', 'xsrfHeaderName' : 'requestverificationtoken'}).then(response => {
        if (response.status === 204){
          window.location.reload();
          this.success = true;
        } else {
          this.mistake = true;
        }
      });
      this.closeDelete()
    },
    likeItem (item){
      axios.post("https://localhost:44319/api/app/book/" + item.id + "/like",
        {},
        {'withCredentials' : 'true', 'xsrfHeaderName' : 'requestverificationtoken'}).then( response => {
        this.liked = true;
        this.likedBookName = item.name;
        item.iconColor = "red"
      })
      item.icon = "mdi-heart"
    },
    close () {
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    closeDelete () {
      this.dialogDelete = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    async save () {
      const type = this.bookType.indexOf(this.editedItem.type);
      const book = this.newBook;
      this.editedItem.type = type;
      book.name = this.editedItem.name;
      book.type = type;
      book.price = this.editedItem.price;
      book.authorId = this.authors[this.authorNames.indexOf(this.editedItem.authorName)].id;
      if (this.editedIndex > -1) {
        await axios.put("https://localhost:44319/api/app/book/" + this.ID, book, {'withCredentials' : 'true', 'xsrfHeaderName' : 'requestverificationtoken'}).
        then(response => {
          if (response.status === 200) {
            window.location.reload();
          } else {
            alert("There is an error!!")
          }
        });
      } else {
        await axios.post("https://localhost:44319/api/app/book",  book , {'withCredentials' : 'true', 'xsrfHeaderName' : 'requestverificationtoken'}).then(response => {
          console.log(response);
          if (response.status === 200) {
            window.location.reload();
          } else {
            alert("There is an error!!")
          }
        });
      }
      this.close()
    },
  },
}
</script>
