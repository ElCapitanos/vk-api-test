<template>
  <div>
    <div class="overlay" @click="closeFriendCard"></div>
    <div class="friendcard">
      <div class="cross" @click="closeFriendCard">&times;</div>
      <div style="display: flex; flex-direction: column">
        <span class="friendcard__name"
          >{{ friend.first_name }} {{ friend.last_name }}</span
        >
        <img
          :src="friend.photo_100"
          :alt="friend.last_name"
          class="friendcard__userpict"
        />
        <span class="friendcard__subtitle">Последнее сообщение на стене:</span>

        <div v-if="wallPost" class="friendcard__walltext">
          <span v-if="wallPost.text">{{ wallPost.text }}</span>
          <div v-if="wallPost.attachments">
            <img
              v-if="wallPost.attachments[0].photo"
              :src="
                wallPost.attachments[0].photo.sizes[
                  wallPost.attachments[0].photo.sizes.length - 1
                ].url
              "
              title="фото со стены пользователя"
              class="friendcard__wallpict"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: "FriendCardComponent",
  props: ["friendCardPopup", "friend", "wallPost"],
  methods: {
    closeFriendCard() {
      this.friendCardPopup.flag = false;
    },
  },
};
</script>
<style lang="sass" scoped>
.friendcard__userpict
    display: flex
    width: 100px
    border-radius: 50%
    margin: 10px auto
    box-shadow: #ccc 0 0 10px
.friendcard__name
    display: flex
    font-size: 12px
    margin: 10px auto 0
.friendcard__subtitle
    font-size: 12px
    margin: 10px auto
.friendcard__walltext
    display: flex
    flex-direction: column
    width: 280px
    font-size: 12px
    border: solid 1px #ccc
    box-sizing: border-box
    margin: 0 10px 10px
    padding: 5px
    overflow-y: auto
    max-height: 300px
    text-align: left
.friendcard__wallpict
    max-width: 230px
    margin: 20px 10px
    display: flex
.cross
    cursor: pointer
    position: absolute
    display: flex
    top: 10px
    right: 10px
    border: solid 2px rgb(74, 118, 168)
    width: 24px
    height: 24px
    line-height: 21px
    background-color: #fff
    color: rgb(74, 118, 168)
    font-size: 18px
    box-sizing: border-box
    justify-content: center
    transition: .4s linear
    border-radius: 50%
    &:hover
        transform: rotate(360deg)
.overlay
    display: flex
    position: fixed
    top: 0
    left: 0
    width: 100%
    height: 100%
    background-color: rgba(0,0,0, .6)
    z-index: 2
.friendcard
    display: flex
    flex-direction: column
    width: 300px
    position: fixed
    top: 50%
    left: 50%
    transform: translate(-50%, -50%)
    background-color: #fff
    z-index: 3
    height: 500px
</style>
