<template>

  <section v-if="board" class="dashboard">
    <button @click="closeDashboard()">✖</button>
    <main class="data-container">
      <div class="data-topic">
        <div class="stats tasks">
          <div class="title">
            <i class="fas fa-tasks"></i>
            <h1>Cards</h1>
          </div>
          <div>
            <span>{{ tasksCount }}</span>
          </div>
        </div>
        <div class="stats done">
          <div class="title">
            <i class="fas fa-check"></i>
            <h1>Done</h1>
          </div>
          <div>
            <span>{{ doneCount }}</span>
          </div>
        </div>
        <div class="stats lists">
          <div class="title">
            <i class="fas fa-clipboard-list"></i>
            <h1>Lists</h1>
          </div>
          <div>
            <span>{{ listsCount }}</span>
          </div>
        </div>
        <div class="stats members">
          <div class="title">
            <i class="fas fa-user"></i>
            <h1>Members</h1>
          </div>
          <div>
            <span>{{ membersCount }}</span>
          </div>
        </div>
      </div>
      <section class="charts-container">
        <div class="chart-container">
          <h2>Labels count in board</h2>
          <chart class="chart" :data="this.labels" />
        </div>
        <div class="pie-chart-container">
          <h2>Task distribution</h2>
          <pie-chart class="pie-chart" :data="members" />
        </div>
      </section>
    </main>
  </section>
</template>

<script>
import { boardService } from '@/services/board.service.js';
import { dashboardService } from '@/services/dashboard.service.js';
import chart from '@/cmps/board/charts/chart.vue';
import pieChart from '@/cmps/board/charts/pie-chart.vue';
export default {
  data() {
    return {
      board: null,
      labels: null,
      members: null
    };
  },
  async created() {
    const { boardId } = this.$route.params;
    const board = await boardService.getById(boardId);
    this.board = board;
    const labelsMap = dashboardService.createLabelsMap(board);
    const labels = dashboardService.setLabelsNames(labelsMap, board);
    this.labels = labels; 
    const members = dashboardService.createMembersMap(board);
    this.members = members
  },
  methods: {
    closeDashboard() {
      this.$router.back();
    },
  },
  computed: {
    listsCount() {
      return this.board.groups.length;
    },
    doneCount() {
      const group = this.board.groups.find(group => group.title.toLowerCase() === 'done')
      return group.cards.length || 0;
    },
    tasksCount() {
      return this.board.groups.reduce((acc, group) => acc + group.cards.length, 0);
    },
    membersCount() {
      return this.board.members.length;
    },
  },
  components: {
    chart,
    pieChart,
  },
};
</script>