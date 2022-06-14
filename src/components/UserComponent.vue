<template>
  <div v-if="user.id" class="user">
    {{ user.first_name }} {{ user.last_name }}
  </div>
</template>
<script>
export default {
  name: "UserComponent",
  props: ["user"],
};
</script>
<style lang="sass" scoped>
.user
    border: solid 1px #ccc
    padding: 10px
    box-sizing: border-box
    margin: 0 10px 10px 0
</style>
