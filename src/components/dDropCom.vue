<template>
  <div class="dragble-block" @mousemove="dragMove($event)" ref="dragWrapper">
    <div
      class="dragble-block__card"
      v-for="(category, index) of categories"
      :key="category.id"
      ref="dropCards"
    >
      <div
        class="dragble-block__card-items"
        v-for="(user, i) of category.users"
        :key="user"
        @mousedown.prevent="onDragStart($event)"
        @mouseup.prevent="onDrop($event, index, i)"
      >
        {{ user }}
      </div>
    </div>
  </div>
</template>
<script setup>
import { ref, onMounted } from "Vue";
let draggingElem = ref(null);
const dragWrapper = ref(null);

let curDropArea = ref(null);
const dropCards = ref(null);

let x = 0;
let y = 0;
const categories = ref([
  { id: 1, users: ["Maga", "Ragim", "Abdulla"] },
  { id: 2, users: ["Patya", "Mesey"] },
  { id: 3, users: ["Gadzhi"] },
  { id: 4, users: ["Gusen"] },
]);

const onDragStart = (e) => {
  e.target.style.display = "none";
  curDropArea.value = document.elementFromPoint(e.x, e.y);
  e.target.style.display = "block";
  draggingElem.value = e.target;
  draggingElem.value.style.position = "absolute";
  draggingElem.value.style.zIndex = `1`;
  draggingElem.value.style.left = `${
    e.x - x - draggingElem.value.offsetWidth / 2
  }px`;
  draggingElem.value.style.top = `${
    e.y - y - draggingElem.value.offsetHeight / 2
  }px`;
};

const dragMove = (e) => {
  if (draggingElem.value == null) return;
  draggingElem.value.style.left = `${
    e.x - x - draggingElem.value.offsetWidth / 2
  }px`;
  draggingElem.value.style.top = `${
    e.y - y - draggingElem.value.offsetHeight / 2
  }px`;
};
const onDrop = (e, index, i) => {
  e.target.style.display = "none";
  let el = document.elementFromPoint(e.x, e.y);
  e.target.style.display = "block";

  if (el.className == "dragble-block__card") {
    if (
      el.getBoundingClientRect().x !=
      curDropArea.value.getBoundingClientRect().x
    ) {
      let user = categories.value[index].users[i];
      categories.value[dropCards.value.indexOf(el)].users.push(user);
      el.appendChild(e.target);

      categories.value[index].users.splice(i, 1);
    }
  }
  draggingElem.value.style.zIndex = `0`;
  draggingElem.value.style.position = `static`;
  draggingElem.value.style.left = `0px`;
  draggingElem.value = null;
  curDropArea.value = null;
};

onMounted(() => {
  x = dragWrapper.value.getBoundingClientRect().x;
  y = dragWrapper.value.getBoundingClientRect().y;
});
</script>
<style lang="scss">
.dragble-block {
  position: relative;
  display: flex;
  justify-content: center;
  gap: 30px;

  @media (max-width: 768px) {
    flex-direction: column;
    align-items: center;
  }
  .dragble-block__card {
    min-height: 200px;
    display: flex;
    flex-direction: column;
    width: 10%;
    background: #043822;
    color: #fff;
    padding: 8px 10px;
    gap: 8px;
    font-size: 18px;
    border-radius: 3px;
    @media (max-width: 768px) {
    width: 85%;
  }
    .dragble-block__card-items {
      cursor: pointer;
      user-select: none;
      padding: 3px;
      &:hover {
        background: rgba($color: #ffffff, $alpha: 0.6);
      }
    }
  }
}
</style>
