<!--<template>-->
<!--  <v-simple-table>-->
<!--    <template v-slot:default>-->
<!--      <thead>-->
<!--      <tr>-->
<!--        <th class="text-left">-->
<!--          Name-->
<!--        </th>-->
<!--        <th class="text-left">-->
<!--          Type-->
<!--        </th>-->
<!--        <th class="text-left">-->
<!--          Dislike-->
<!--        </th>-->
<!--      </tr>-->
<!--      </thead>-->
<!--      <tbody>-->
<!--      <tr-->
<!--        v-for="(book) in books"-->
<!--        :key="book.name"-->
<!--      >-->
<!--        <td>{{ book.bookName }}</td>-->
<!--        <td>{{ book.type}}</td>-->
<!--        <td><v-icon :color = book.color @click = "dislike(book)">{{ star }}</v-icon></td>-->
<!--      </tr>-->
<!--      </tbody>-->
<!--    </template>-->
<!--  </v-simple-table>-->
<!--</template>-->
<template>
  <v-data-table
    :headers="headers"
    :items="books"
    sort-by="name"
    class="elevation-1">
    <template #top>
      <v-snackbar
        v-model="disliked"
      >
        You disliked the book {{ dislikedBookName }}

        <template #action="{ attrs }">
          <v-btn
            color="pink"
            text
            v-bind="attrs"
            @click="refresh"
          >
            Close
          </v-btn>
        </template>
      </v-snackbar>
      <v-toolbar
        flat
      >
        <v-toolbar-title>Favourite Books List</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        <v-spacer></v-spacer>
      </v-toolbar>
    </template>

    <template v-slot:[`item.dislike`]="{ item }">
      <v-icon
        small
        @click="dislike(item)"
      >
        mdi-thumb-down
      </v-icon>
    </template>
  </v-data-table>
</template>
<script>
import axios from "axios"
import Books from './Books'

export default {
  name: 'FavouriteBooks',
  data() {
    return {
      headers : [
        {
          text: 'Book Name',
          align: 'start',
          sortable: true,
          value: 'bookName',
        },
        { text: 'Type', value: 'type' },
        { text: 'Dislike', value: 'dislike', sortable: false},
      ],
      disliked : false,
      dislikedBookName : "Nothing",
      books : []
    }
  },
  created() {
    axios.get("https://localhost:44319/api/app/favourite-book", { 'withCredentials' : 'true' }).then(response => {
      const book = response.data.items;
      for (let i = 0; i < book.length; i++){
        this.books.push({ ...book[i], type : Books.data().bookType[book[i].type], color : 'success'});
      }
      Books.favouriteBooks = this.books;
    }).catch(e => console.log(e));
  },
  methods : {
    async dislike(book){
      await axios.delete("https://localhost:44319/api/app/favourite-book/" + book.id, {'withCredentials' : 'true', 'xsrfHeaderName' : 'requestverificationtoken'})
        .then(response => {
        if (response.status === 204){
          this.disliked = true;
          this.dislikedBookName = book.bookName;
        }
      });
    },
    refresh(){
      this.disliked = false;
      window.location.reload();
    }
  }
}
</script>
