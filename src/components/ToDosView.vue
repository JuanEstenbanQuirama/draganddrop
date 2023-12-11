<script setup>
import { reactive } from "vue";
import InputNew from "./InputNew.vue";


let boards = reactive([
  {
    id: crypto.randomUUID(),
    name: "Board ToDo´s",
    items: [
      // {
      //   id: crypto.randomUUID(),
      //   title: "probando ando",
      // },
      // {
      //   id: crypto.randomUUID(),
      //   title: "probando ando 2",
      // },
    ],
  },
  // {
  //   id: crypto.randomUUID(),
  //   name: "tablero 2",
  //   items: [
  //     {
  //       id: crypto.randomUUID(),
  //       title: "probando xd",
  //     },
  //     {
  //       id: crypto.randomUUID(),
  //       title: "probando xd 2",
  //     },
  //   ],
  // },
]);

function handleNewItem(text, board) {
  board.items.push({
    id: crypto.randomUUID(),
    title: text.value,
  });
}

function handleNewBoard() {
  const name = prompt("Name of the board"); // Buscar como funcionan el promt y si se puede cambiar por una notificiacion mas chimba
  if (!!name) {
    boards.push({
      id: crypto.randomUUID(),
      name: name,
      items: [],
    });
  }
}

function startDrag(evt, board, item) {
  evt.dataTransfer.setData(
    "text/plain",
    JSON.stringify({ boardId: board.id, itemId: item.id })
  );
}

function onDrop(evt, dest) {
  const { boardId, itemId } = JSON.parse(
    evt.dataTransfer.getData("text/plain")
  );
  const originBoard = boards.find((item) => item.id == boardId);
  const originItem = originBoard.items.find((item) => item.id == itemId);
  dest.items.push({ ...originItem });
  originBoard.items = originBoard.items.filter((item) => item != originItem);
}

function deleteItem(board, item) {
  board.items = board.items.filter((i) => i !== item);
}

// PENDIENTE CREAR FUNCIONAIDAD DE ENVIDAR LOS ELIMINADOS A UNA LISTA  - QUE SE VEA COMO UN HISTORIAL
</script>

<template lang="pug">
div.general-container
  nav.nav
    p 
      strong Drag & Drop
    a(href="#" @click.prevent="handleNewBoard") Create board
  .boards-container 
      .boards 
          div.board(@drop="onDrop($event, board)" @dragover.prevent @dragenter.prevent v-for="board in boards" :key="board.id") 
              div {{ board.name }}
              InputNew(@on-new-item="(text) => handleNewItem(text, board)")
              .items 
                  .item(draggable="true" @dragstart="startDrag($event, board, item)" v-for="item in board.items" :key="item.id")
                    p {{ item.title }}
                    div.botton-container
                      button.boton(@click="deleteItem(board, item)") delete
                    
</template>
