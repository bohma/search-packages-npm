<template>
  <div class="">
    <h1 class="header__title">Welcome to package searher</h1>
    <v-text-field
      v-model="search"
      class="input"
      :style="{ margin: '0 auto' }"
      prepend-inner-icon="mdi-magnify"
      solo
      label="search all of npm"
      clearable
    ></v-text-field>
    <div v-if="cards.length" class="">
      <v-card v-for="(item, index) in cards" :key="index" class="mx-auto card">
        <v-list-item three-line>
          <v-list-item-avatar tile size="20">
            <img :src="item.owner.avatar" alt="Image owner" />
          </v-list-item-avatar>

          <v-list-item-content>
            <div class="headline mb-2">{{ item.name }}</div>
            <v-list-item-subtitle>{{ item.description }}</v-list-item-subtitle>
          </v-list-item-content>
        </v-list-item>
        <div class="pl-10">
          <v-chip
            v-for="(tag, num) in item.keywords.slice(0, 3)"
            :key="num"
            class="ml-2"
            outlined
            small
            ><a @click="formTag(tag)">{{ tag }}</a></v-chip
          >
        </div>
        <v-card-actions>
          <v-popup :item="item"></v-popup>
        </v-card-actions>
      </v-card>
      <div class="text-center pagination">
        <v-pagination
          v-model="page"
          color="rgb(149 149 149)"
          :length="numberOfPages"
          :total-visible="7"
        ></v-pagination>
      </div>
    </div>
    <v-footer dark fixed class="social">
      <p class="social__rights">
        All rights reserved - {{ new Date().getFullYear() }}
      </p>
      <div class="social__link">
        <v-btn
          v-for="(icon, index) in icons"
          :key="index"
          class="mx-4"
          dark
          icon
        >
          <a :href="icon.link" target="_blank">
            <v-icon size="24px">
              {{ icon.icon }}
            </v-icon>
          </a>
        </v-btn>
      </div>
    </v-footer>
  </div>
</template>

<script>
import axios from "axios";
import vPopup from "@/components/popup/vPopup.vue";
import urlPost from "@/api/api"
export default {
  name: "Home",
  components: {
    vPopup,
  },
  data() {
    return {
      search: "",
      cards: [],
      page: 1,
      hitsPerPage: 10,
      urlPost,
      numberOfHits: null,
      icons: [
        { icon: "mdi-github", link: "https://github.com/bohma" },
        { icon: "mdi-telegram", link: "https://t.me/Boh_ma" },
      ],
    };
  },
  computed: {
    numberOfPages() {
      if (Math.round(this.numberOfHits / this.hitsPerPage) > this.page + 2) {
        return this.page + 2;
      }

      return Math.round(this.numberOfHits / this.hitsPerPage);
    },
  },
  watch: {
    search() {
      if (this.search) {
        this.getPackages(this.search, 0);
      }

      if (!this.search) {
        this.cards = [];
        this.page = 0;
        this.search = "";
      }
    },
    page() {
      this.getPackages(this.search, this.page);
      this.$vuetify.goTo(0);
    },
  },
  methods: {
    async getPackages(search, page) {
      
      await axios
        .post(this.urlPost, {
          // eslint-disable-next-line
          params: `query=${search}&page=${page}&hitsPerPage=${this.hitsPerPage}&attributesToHighlight=%5B%5D&attributesToRetrieve=%5B%22deprecated%22%2C%22description%22%2C%22githubRepo%22%2C%22homepage%22%2C%22keywords%22%2C%22license%22%2C%22name%22%2C%22owner%22%2C%22version%22%5D&analyticsTags=%5B%22jsdelivr%22%5D&optionalFacetFilters=%5B%22_searchInternal.alternativeNames%3Ad%22%5D`,
        })
        .then((data) => {
          this.cards = data.data.hits;
          this.numberOfHits = data.data.nbHits;
        })
        .catch((e) => console.log(e));
    },
    formTag(tag) {
      this.search = tag;
      this.$vuetify.goTo(0);
    },
  },
};
</script>

<style lang="scss" scoped>
.header {
  &__title {
    font-weight: 200;
    font-size: 30px;
    margin: 40px 0 20px;
    text-align: center;
  }
}
.card {
  margin-bottom: 30px;
  width: 450px;
}
.input {
  width: 450px;
}
.pagination {
  margin-bottom: 100px;
}
.social {
  display: flex;
  justify-content: space-between;
  align-items: center;
  &__rights {
    margin: 0;
  }
  &__link {
    display: flex;
    a {
      text-decoration: none;
      color: #fff;
    }
  }
}
@media screen and (max-width: 767px) {
  .card {
    width: 350px;
  }
  .input {
    width: 350px;
  }
  .social__rights {
    font-size: 12px;
  }
}
</style>
