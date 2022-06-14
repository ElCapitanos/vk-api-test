<template>
    <button class="builder main__btn" @click="buildList">Построить</button>
</template>
<script>
export default {
    name: 'BuildButtonComponent',
    methods: {
        buildList() {
            this.$emit('buildFriendsList')
        },
    },
}
</script>
<style lang="sass" scoped>
.builder
    margin: 20px auto
    width: 160px
</style>
