<template>
<div class="loader" v-if="isLoading">
    <img src="../assets/imgs/loader.gif"/>
</div>
  <div v-else class='board-app'>
    <div class="starred-boards">
      <h2><i class="far fa-star"></i> Starred boards</h2>
        <ul>
          <div v-for='board in boards' :key='board._id'>
            <li v-if="board.isStarred" class='board-preview board' @click='openBoard(board._id)' title="Open board"
            :style="board.style.includes('unsplash') ? `background: url(${board.style}); background-size: cover; ` : `background-color:${board.style}`">
              <p>{{board.title}}</p>
            </li>
          </div>
        </ul>
    </div>
     <div class="workspace-boards">
      <h2><i class="far fa-user"></i> Workspace</h2>
        <ul>
          <div v-for='board in boards' :key='board._id'>
              <li v-if="!board.isStarred" class='board-preview board' @click='openBoard(board._id)' title="Open board"
            :style="board.style.includes('unsplash') ? `background: url(${board.style}); background-size: cover; ` : `background-color:${board.style}`">
              <p>{{board.title}}</p>
            </li>
          </div>
          <li v-if="!isAdd" class='board-add board' @click='isAdd = true' title="Add board">Add new board</li>
          <li class="create-board board" v-if="isAdd">
            <form @submit.prevent="createBoard">
              <input type="text" placeholder="Enter board title" v-model="title">
              <button>Create</button>
            </form>
          </li>
        </ul>
    </div>
  </div>
</template>

<script>
import { boardService } from "@/services/board.service.js";
import {socketService} from '@/services/socket.service.js';
export default {
  name: "board-app",
  data() {
    return {
      isAdd: false,
      title: "",
    };
  },
  components: {},
  created() {
    document.body.style.backgroundColor = "#ffffff";
    document.body.style.backgroundImage = "none";
    this.$store.dispatch({ type: "loadBoards" });
    socketService.on('boards-watch', this.updateBoards)
  },
  computed: {
    boards() {
      return this.$store.getters.boards;
    },
    isLoading(){
      return this.$store.getters.isLoading
    }
  },
  methods: {
    updateBoards(boards){
      this.$store.commit({type:'setBoards', boards})
    },
    openBoard(boardId) {
      this.$router.push("/board/" + boardId);
    },
    async createBoard() {
      if (!this.title) return;
      let board = boardService.getEmptyBoard();
      board.title = this.title;
      await this.$store.dispatch({type:'addBoard',board})
      socketService.emit('boards-watch',this.boards)
      this.isAdd = false;
    },
  },
};
</script>