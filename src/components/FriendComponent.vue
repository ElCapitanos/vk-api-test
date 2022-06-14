<template>
  <div class="friend" v-if="friend.first_name != 'DELETED'" ref="card">
    <div
      class="friend__container"
      v-if="friend.photo_100"
      @click="openFriendCard"
    >
      <span class="friend__text friend__title"
        >{{ friend.first_name }} {{ friend.last_name }}</span
      >
      <img
        :src="friend.photo_100"
        :alt="friend.last_name"
        class="friend__pict"
      />
      <p class="friend__text" v-if="friend.sex">Пол: {{ sexIs(friend.sex) }}</p>
      <p class="friend__text" v-if="friend.bdate">
        Дата рождения: {{ friend.bdate }}
      </p>
      <p class="friend__text" v-if="friend.counters">
        Кол-во друзей: {{ friend.counters.friends }}
      </p>
      <hr style="color: white" />
      <div class="friend__text friend__amount">
        {{ searchUsersForFriendCard() }} в друзьях
      </div>
    </div>
    <friend-card-component
      v-if="friendCardPopup.flag && wallPost"
      :friend="friend"
      :friendCardPopup="friendCardPopup"
      :wallPost="wallPost"
    />
  </div>
</template>
<script>
import FriendCardComponent from "./FriendCardComponent.vue";
import { _TOKEN } from "../data";
import { _BASE_URL } from "../data";
import { _VERSION } from "../data";
import { _WALL_GET } from "../data";
export default {
  components: { FriendCardComponent },
  name: "FriendComponent",
  props: ["friend", "friendsUser", "usersArr"],
  data() {
    return {
      friendCardPopup: {
        flag: false,
      },
      wallPost: {},
    };
  },
  updated() {
    this.opacityCard();
  },
  methods: {
    opacityCard() {
      let cards = document.querySelectorAll(".friend");
      for (let card of cards) {
        let amount = card.querySelector(".friend__amount");
        let length = amount.textContent.split("").filter(function (value) {
          return value == ",";
        }).length;
        if (length > 0) {
          let factor = 0.4 / length;
          card.style.backgroundColor =
            "rgba(74, 118, 168, " + (+factor + 0.6) + ")";
        }
      }
    },
    searchUsersForFriendCard() {
      let friendsUserName = [];
      let trueName = "";
      for (let cardId in this.friendsUser) {
        for (let nameId of this.friendsUser[cardId]) {
          if (nameId == this.friend.id) {
            for (let name of this.usersArr) {
              if (name.id == cardId) {
                trueName = name.first_name + " " + name.last_name;
                friendsUserName.push(trueName);
              }
            }
          }
        }
      }
      friendsUserName = friendsUserName.join(", ");
      return friendsUserName;
    },
    sexIs(number) {
      if (number == 1) {
        return "женский";
      }
      if (number == 2) {
        return "мужской";
      }
      if (number == 0) {
        return "не указан";
      }
    },
    openFriendCard() {
      this.friendCardPopup.flag = true;
      this.fetchWallPost(this.friend.id);
    },
    fetchWallPost(friendId) {
      fetch(
        _BASE_URL +
          _WALL_GET +
          _VERSION +
          _TOKEN +
          "&owner_id=" +
          friendId +
          "&count=1"
      )
        .then((res) => res.json())
        .then((data) => {
          this.wallPost = data.response.items[0];
          if (this.wallPost == undefined) {
            alert("У пользователя на стене нет ни одной записи");
          }
        })
        .catch((e) => {
          alert("Произошла ошибка запроса.");
        });
    },
  },
};
</script>
<style lang="sass" scoped>
.friend__container
    cursor: pointer
.friend__title
    text-transform: uppercase
    font-weight: 500
.friend__pict
    display: flex
    border-radius: 50%
    margin: 0 auto 10px
    box-shadow: 0px 0px 12px #fff
.friend__text
    display: flex
    margin: 10px auto
    justify-content: center
    color: #fff
    font-size: 14px
    font-weight: 500
.friend
    background-color: rgba(74, 118, 168, .6)
    padding: 6px
    border: solid 1px transparent
    box-sizing: border-box
    width: 300px
    margin-top: 6px
</style>
