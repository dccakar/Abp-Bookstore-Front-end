<template>
  <v-simple-table>
    <template v-slot:default>
      <thead>
      <tr>
        <th class="text-left">
          Name
        </th>
        <th class="text-left">
          DisLike
        </th>
      </tr>
      </thead>
      <tbody>
      <tr
        v-for="(author, index) in authors"
        :key="author.name"
      >
        <td>{{ author.authorName }}</td>
        <td><v-icon :color = author.color @click = "dislike(index)">{{ star }}</v-icon></td>
      </tr>
      </tbody>
    </template>
  </v-simple-table>
</template>

<script>
import axios from "axios"
export default {
  name: 'FavouriteAuthors',
  data() {
    return {
      star : 'mdi-star-outline',
      disliked : false,
      authors : []
    }
  },
  created() {
    axios.get("https://localhost:44319/api/app/favourite-author", { 'withCredentials' : 'true' }).then(response => {
      const author = response.data.items;
      for (let i = 0; i < author.length; i++){
        this.authors.push({ ...author[i], color : 'success'});
      }
    }).catch(e => console.log(e));
  },
  methods : {
    dislike(index){
      if (this.authors[index].color === 'success'){
        this.authors[index].color = 'white';
      }else {
        this.authors[index].color = 'success';
      }
    }
  }
}
</script>
