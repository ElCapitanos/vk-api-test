<template>
  <div>
    <h2 class="main__title">Выбрать пользователя ВК</h2>
    <form class="main__form" @submit.prevent="validUser">
      <input
        class="main__input"
        type="text"
        placeholder="Введите id-номер пользователя"
        ref="userId"
        autofocus
        @input="isClearInput"
      />
      <button type="submit" class="main__btn" ref="btn" disabled>Найти</button>
    </form>
    <div v-if="popupFlag" class="overlay"></div>
    <div v-if="popupFlag" class="main__popup">
      <div v-if="userFirstName && userLastName">
        <h5>
          По данному ID найден пользователь с именем "{{ userFirstName }}
          {{ userLastName }}". Добавляем на страницу?
        </h5>
        <div class="main__btns">
          <button class="main__btn" @click="addUser">Да</button>
          <button class="main__btn" @click="toggleFlag">Нет</button>
        </div>
      </div>
      <div v-else>
        <h5>Пользователь не найден (возможно, аккаунт был удалён)</h5>
        <div class="main__btns">
          <button class="main__btn" @click="toggleFlag">Ok</button>
        </div>
      </div>
    </div>
    <users-component :users="usersArr" />
    <build-button-component
      v-if="usersArr.length"
      @buildFriendsList="buildFriendsList"
    />
    <div>
      <friends-component
        v-if="friendsIdArr.length"
        :friendData="friendData"
        :friendsIdArr="friendsIdArr"
        :friendsUser="friendsUser"
        :usersArr="usersArr"
      />
    </div>
  </div>
</template>
<script>
import UsersComponent from "./UsersComponent.vue";
import { _TOKEN } from "../data"; // лучше, конечно, экспортировать/ импортировать объект, а всякие токены-методы-версии и пр. устанавливать методами этого объекта. Но для наглядности можно и так )
import { _BASE_URL } from "../data";
import { _VERSION } from "../data";
import { _USERS_GET } from "../data";
import { _FRIENDS_GET } from "../data";
import BuildButtonComponent from "./BuildButtonComponent.vue";
import FriendsComponent from "./FriendsComponent.vue";

export default {
  name: "MainComponent",
  components: { UsersComponent, BuildButtonComponent, FriendsComponent },
  data() {
    return {
      popupFlag: false,
      currentUser: [],
      usersArr: [],
      friendsIdArr: [],
      friendData: [],
      userFirstName: "",
      userLastName: "",
      friendsUser: {},
    };
  },
  methods: {
    buildFriendsList() {
      if (this.usersArr.length) {
        this.friendData = [];
        for (let user of this.usersArr) {
          this.fetchFriendList(user.id, _FRIENDS_GET);
        }
      }
    },
    isClearInput() {
      this.$refs.userId.value
        ? (this.$refs.btn.disabled = false)
        : (this.$refs.btn.disabled = true);
    },
    clearInput() {
      this.$refs.userId.value ? (this.$refs.userId.value = "") : "";
    },
    validUser() {
      !isNaN(this.$refs.userId.value) && this.$refs.userId.value > 0
        ? this.fetchData(this.$refs.userId.value, _USERS_GET)
        : alert("ID пользователя должен представлять собой число больше нуля");
    },
    checkUser(userFirstName, userLastName) {
      this.userFirstName = userFirstName;
      this.userLastName = userLastName;
    },
    newUserToArray(user, array) {
      array.push(user);
      user = [];
    },
    checkForUnique(array, user) {
      if (array.length) {
        let counter = 0;
        for (let item of array) {
          if (item.id === user.id) {
            counter++;
          }
        }
        if (counter != 0) {
          alert("Пользователь уже был ранее добавлен в список");
        } else {
          this.newUserToArray(user, array);
        }
      } else {
        this.newUserToArray(user, array);
      }
    },
    addUser() {
      this.checkForUnique(this.usersArr, this.currentUser);
      this.toggleFlag();
    },
    toggleFlag() {
      this.popupFlag = !this.popupFlag;
      this.clearInput();
    },
    sortArrayByLastName(x, y) {
      return x.last_name.localeCompare(y.last_name);
    },
    fetchFriendList(userId, method) {
      fetch(_BASE_URL + method + _VERSION + _TOKEN + "&user_id=" + userId)
        .then((res) => res.json())
        .then((data) => {
          this.friendsIdArr = data.response.items;
          if (this.friendsUser.userId) {
            this.friendsUser[userId] = this.friendsIdArr;
          } else {
            this.friendsUser[userId] = this.friendsIdArr;
          }
          if (!this.friendsIdArr.length) {
            alert("У выбранных пользователей нет открытого списка друзей");
          } else {
            for (let item of this.friendsIdArr) {
              this.fetchFriend(item);
            }
          }
        })
        .catch((e) => {
          alert(
            "Произошла ошибка запроса. Возможно, пользователь " +
              userId +
              " скрыл свой список друзей"
          );
        });
    },
    uniqueFriendList(array) {
      if (array.length > 1) {
        for (let i = 1; i < array.length; i++) {
          for (let j = 0; j < i; j++) {
            if (array[j].id == array[i].id) {
              array.splice(i, 1);
            }
          }
        }
      }
    },
    fetchFriend(friend) {
      fetch(
        _BASE_URL +
          _USERS_GET +
          _VERSION +
          _TOKEN +
          "&user_id=" +
          friend +
          "&fields=photo_100,sex,bdate,counters,is_friend"
      )
        .then((res) => res.json())
        .then((data) => {
          this.friendData.push(data.response[0]);
          this.friendData = this.friendData.sort(this.sortArrayByLastName);
          this.uniqueFriendList(this.friendData);
        })
        .catch((e) => {
          this.fetchFriend(friend);
        });
    },
    fetchData(userId, method) {
      fetch(_BASE_URL + method + _VERSION + _TOKEN + "&user_id=" + userId)
        .then((res) => res.json())
        .then((data) => {
          this.checkUser(
            data.response[0].first_name,
            data.response[0].last_name
          );
          this.currentUser = data.response[0];
          this.toggleFlag();
        })
        .catch((e) => {
          alert("Произошла ошибка запроса. Возможно, введён несуществующий ID");
        });
    },
  },
};
</script>
<style lang="sass">
$blue-color: #4a76a8
.main__popup
  display: flex
  width: 300px
  flex-direction: column
  padding: 8px
  box-sizing: border-box
  border: solid 1px $blue-color
  z-index: 3
  background-color: #fff
  position: fixed
  left: 50%
  top: 50%
  transform: translate(-50%, -50%)
.main__btns
  display: flex
  flex-direction: row
  width: 80%
  justify-content: space-around
  margin: 10px auto 30px
.overlay
  display: flex
  top: 0
  left: 0
  position: fixed
  width: 100%
  height: 100%
  z-index: 2
  background-color: rgba(0,0,0, .6)
.main__form
  display: flex
  flex-direction: row
  justify-content: center
  width: 100%
.main__btn
  display: flex
  justify-content: center
  background-color: $blue-color
  color: #fff
  font-weight: 600
  font-size: 10px
  padding: 12px 0
  box-sizing: border-box
  border: solid 1px $blue-color
  transition: .4s linear
  text-transform: uppercase
  cursor: pointer
  width: 70px
  user-select: none
  &:hover
    color: $blue-color
    background-color: #fff
  &:disabled
    opacity: .6
    cursor: not-allowed
    background-color: $blue-color
    color: #fff
.main__input
  padding: 10px
  box-sizing: border-box
  display: flex
  width: 260px
  outline: none
  margin-right: 10px
</style>
