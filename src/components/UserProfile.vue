<template>
  <div class="user-profile">
    <div class="user-profile__user-panel">
      <h1 class="user-profile__username">@{{ state.user.username }}</h1>
      <div class="user-profile__admin-badge" v-if="state.user.isAdmin">
        Admin
      </div>
      <div class="user-profile__follower-count">
        <strong>Followers: </strong> {{ state.followers }}
      </div>
      <form class="user-profile__create-twoot" @submit.prevent="createNewTwoot">
        <label for="newTwoot"><strong>New Twoot</strong></label>
        <textarea
          id="newTwoot"
          rows="4"
          v-model="state.newTwootContent"
        ></textarea>
        <div class="user-profile__create-twoot-type">
          <label for="newTwootType"><strong>Type: </strong></label>
          <select id="newTwootType" v-model="state.selectedTwootType">
            <option
              :value="option.value"
              v-for="(option, index) in state.twootTypes"
              :key="index"
            >
              {{ option.name }}
            </option>
          </select>
        </div>
        <button>Twoot!</button>
      </form>
    </div>
    <div class="user-profile__twoots-wrapper">
      <TwootItem
        v-for="twoot in state.user.twoots"
        :key="twoot.id"
        :username="state.user.username"
        :twoot="twoot"
        @favourite="toggleFavourite"
      />
    </div>
  </div>
</template>

<script>
import TwootItem from "./TwootItem.vue";
import { reactive, computed } from "vue";

export default {
  name: "UserProfile",
  components: { TwootItem },
  setup() {
    const state = reactive({
      newTwootContent: "",
      selectedTwootType: "instant",
      twootTypes: [
        { value: "draft", name: "Draft" },
        { value: "instant", name: "Instant Twoot" },
      ],
      followers: 0,
      user: {
        id: 1,
        username: "Harry_Preston",
        firstName: "Harry",
        lastName: "Preston",
        email: "harry@gmail.com",
        isAdmin: true,
        twoots: [
          { id: 1, content: "This is a twoot." },
          { id: 2, content: "This is a different twood." },
        ],
      },
    });

    const newTwootCharacterCount = computed(() => state.newTwootContent.length);

    function createNewTwoot() {
      if (state.newTwootContent && state.selectedTwootType !== "draft") {
        state.user.twoots.unshift({
          id: state.user.twoots.length + 1,
          content: state.newTwootContent,
        });
      }
    }

    return {
      state,
      newTwootCharacterCount,
      createNewTwoot,
    };
  },
  watch: {
    followers(newFollowerCount, oldFollowerCount) {
      if (oldFollowerCount < newFollowerCount) {
        console.log(`${this.state.user.username} has gained a follower!`);
      }
    },
  },
  methods: {
    followUser() {
      this.state.followers++;
    },
    toggleFavourite(id) {
      console.log(`Favourited Tweet ${id}`);
    },
  },
  mounted() {
    this.followUser();
  },
};
</script>
