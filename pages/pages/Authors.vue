<template>
  <v-simple-table>
    <template>
      <thead>
      <tr>
        <th class="text-left">
          Name
        </th>
        <th class="text-left">
          Birth Date
        </th>
        <th class="text-left">
          Like
        </th>
      </tr>
      </thead>
      <tbody>
      <tr
        v-for="(author, index) in authors"
        :key="author.name"
      >
        <td>{{ author.name }}</td>
        <td >{{ formatDate(author.birthDate)}}</td>
        <td><v-icon :color=author.color @click='liked(index)'>{{ like }}</v-icon></td>
      </tr>
      </tbody>
    </template>
  </v-simple-table>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      like : 'mdi-star-outline',
      isLiked : false,
      authors : []
    }
  },
  created() {
    axios.get("https://localhost:44319/api/app/author", { 'withCredentials' : 'true' }).then(resp => {
      const author = resp.data.items;
      for (let i = 0; i < author.length; i++){
        this.authors.push({ ...author[i], color :'white' });
      }
    })
  },
  methods : {
    liked(index) {
      if (this.authors[index].color === 'success'){
        this.authors[index].color = 'white';
      }else {
        this.authors[index].color = 'success';
      }
    },
    formatDate(date){
      const formatted = date.substring(0, 10);
      return formatted.split("-").reverse().join("/") ;
    }
  }
}
</script>
