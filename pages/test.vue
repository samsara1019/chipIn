<template>
  <div>
    <div v-for="(menu, index) in menus" :key="index">
      메뉴<input type="text" v-model="menu.name" placeholder="메뉴 이름을 적어주세요" />
      <!-- 갯수<input type="number" v-model="menu.number" /> -->
      가격<input type="number" v-model="menu.amount" /> n분의 1<input type="radio" v-model="menu.isN" :value="true" /> 따로 계산<input
        type="radio"
        v-model="menu.isN"
        :value="false"
      />
      <div v-if="!menu.isN">
        먹은인원
        <span v-for="member in members" :key="member.name">
          <input type="checkbox" v-model="menu.members" :value="member.name" :id="`${menu.name}${member.name}`" />
          <label :for="`${menu.name}${member.name}`">{{ member.name }}</label>
          (<input type="number" v-model="menu.counts">)
        </span>
      </div>
      <button @click="deleteMenu(index)">메뉴삭제-</button>
    </div>
    <button @click="plusMenu">메뉴추가+</button>
    <br />

    총 {{ totalAmount }}원
    <br />
    <br />
    <br />

    <div v-for="(member, index) in members" :key="index">
      <input type="text" v-model="member.name" placeholder="이름을 써주세요" />
      <!-- <div v-for="(menu, index) in menus" :key="index">
        {{ menu.name }}
        <div v-if="!menu.isN">몇인분?<input type="number" @input="changeNumberOfMember($event, member.name, menu.name)" /></div>
      </div> -->
      총 {{ member.getTotalAmount() }}원 <br>
      <!-- <span v-for="(menu, index) in member.getMenus()" :key="index">
        {{ menu.name }} ({{ menu.count }}) /
      </span> -->
      <!-- <button @click="deleteMember">인원삭제+</button> -->
    </div>
    <button @click="plusMember">인원추가+</button>
  </div>
</template>

<script setup>
class Menu {
  constructor() {
    this.name = "";
    this.amount = 0;
    this.isN = true;
    this.members = [];
    this.counts = [];
  }
}
class Member {
  constructor(name) {
    this.name = name;
  }
  getTotalAmount() {
    return menus.value.reduce((sum, menu) => {
      if (menu.isN) {
        return (sum += menu.amount / members.value.length);
      } else {
        const isEat = menu.members.find((member) => member === this.name);
        if (isEat) {
          return (sum += menu.amount / menu.members.length);
        } else {
          return (sum += 0);
        }
      }
    }, 0);
  }
  getMenus() {
    return menus.value.map((menu) => {
        const isEat = menu.members.find((member) => member === this.name);
        if (isEat) {
          return {
            name: menu.name,
            count: 1 / menu.members.length,
          };
        } else {
          return {
            name: menu.name,
            count: 1 / members.value.length,
          };
        }
      })
      .filter((m) => m);
  }
}
const menus = ref([new Menu()]);
const members = ref([new Member()]);
const totalAmount = computed(() => {
  return menus.value.reduce((sum, menu) => {
    return sum + menu.amount;
  }, 0);
});

const plusMenu = () => {
  menus.value.push(new Menu());
};
const plusMember = () => {
  members.value.push(new Member());
};
const deleteMenu = (index) => {
  menus.value.splice(index, 1);
};
const deleteMember = (index) => {
  members.value.splice(index, 1);
};
</script>

<style></style>
