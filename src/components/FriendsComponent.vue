<template>
  <div class="friends">
    <h4 class="friends__title">Друзья выбранных пользователей</h4>
    <!--  -->
    <div
      v-if="friendData.length >= friendsIdArr.length"
      class="friends__container"
    >
      <friend-component
        v-for="(friend, index) in friendData"
        :key="index"
        :friend="friend"
        :friendsUser="friendsUser"
        :usersArr="usersArr"
      />
    </div>
    <div v-else class="friends__loader">
      <ring-loader :color="loaderColor" :size="loaderSize"></ring-loader>
    </div>
  </div>
</template>
<script>
import RingLoader from "vue-spinner/src/RingLoader.vue";
import FriendComponent from "./FriendComponent.vue";
export default {
  name: "FriendsComponent",
  data() {
    return {
      loaderColor: "#4a76a8",
      loaderSize: "100px",
    };
  },
  components: {
    FriendComponent,
    RingLoader,
  },
  props: ["friendData", "friendsIdArr", "friendsUser", "usersArr"],
};
</script>
<style lang="sass" scoped>
.friends__loader
    display: flex
    margin: auto
    justify-content: center
    margin-top: 20px
.friends__title
  margin: 40px auto 0
.friends
  display: flex
  flex-direction: column
.friends__container
    border: solid 1px #ccc
    display: flex
    flex-direction: row
    flex-wrap: wrap
    justify-content: space-around
    width: 98%
    margin: 10px auto
    padding: 0 0 6px
</style>
