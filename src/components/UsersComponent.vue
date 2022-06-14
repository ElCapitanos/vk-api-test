<template>
  <div v-if="users.length" class="users">
    <h4>Выбранные пользователи</h4>
    <div class="users__container">
      <user-component v-for="user of users" :key="user.id" :user="user" />
    </div>
  </div>
</template>
<script>
import UserComponent from "./UserComponent.vue";
export default {
  components: { UserComponent },
  name: "UsersComponent",
  props: ["users"],
};
</script>
<style lang="sass" scoped>
.users
    display: flex
    flex-direction: column
    width: 96%
    margin: 20px auto
    border: solid 1px #ccc
    padding: 10px
    box-sizing: border-box
.users__container
    display: flex
    flex-direction: row
    flex-wrap: wrap
    width: 100%
    margin: 20px auto
</style>
