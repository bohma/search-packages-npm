<template>
  <v-dialog transition="dialog-bottom-transition" max-width="600">
    <template v-slot:activator="{ on, attrs }">
      <v-btn
        elevation="2"
        class="ml-10 mt-2 mb-3"
        v-bind="attrs"
        :loading="dialog"
        v-on="on"
        @click="getInfoPackage(item)"
        >More...</v-btn
      >
    </template>
    <template v-slot:default="dialog">
      <v-card>
        <v-toolbar dark
          ><v-avatar size="36px" class="mr-2">
            <img :src="item.owner.avatar" alt="Owner image" /> </v-avatar
          >{{ item.name }}</v-toolbar
        >
        <v-card-text>
          <div class="pa-4">{{ item.description }}</div>
          <div class="pl-4">Current version: {{ item.version }}</div>
          <div class="pl-4">Total rank: {{ totalRank }}</div>
          <a :href="item.owner.link" class="pl-4" target="_blank">
            Link to owner</a
          >
        </v-card-text>
        <v-card-actions class="justify-end">
          <v-btn text @click="dialog.value = false">Close</v-btn>
        </v-card-actions>
      </v-card>
    </template>
  </v-dialog>
</template>

<script>
import axios from "axios";
export default {
  props: {
    item: {
      type: Object,
      default() {
        return {};
      },
    },
  },
  data() {
    return {
      dialog: false,
      totalRank: 0,
    };
  },
  methods: {
    async getInfoPackage(item) {
      await axios
        .get(`https://data.jsdelivr.com/v1/package/npm/${item.name}/stats`)
        .then((res) => {
          this.totalRank = res.data.total;
        })
        .catch((e) => console.log(e));
    },
  },
};
</script>