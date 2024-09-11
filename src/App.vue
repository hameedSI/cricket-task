<template>
  <div>
    <!-- Navigation and Team Filter -->
    <nav class="navbar navbar-expand-lg bg-light">
      <div class="container-fluid">
        <a class="navbar-brand" href="#">Sportz Interactive</a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <ul class="navbar-nav me-auto mb-2 mb-lg-0">
            <!-- Dropdown to Filter Players by Team -->
            <select v-model="selectedTeam" class="form-select" @change="filterPlayers">
              <option value="ALL">ALL Teams</option>
              <option v-for="team in teams" :key="team" :value="team">{{ team }}</option>
            </select>
          </ul>
          <!-- Search Form for Player Names -->
          <form class="d-flex" role="search">
            <input v-model="searchQuery" class="form-control me-2" type="search" placeholder="Search">
            <button class="btn btn-outline-success" type="button" @click="searchPlayers">Search</button>
          </form>
        </div>
      </div>
    </nav>

    <!-- Displaying Player Cards -->
    <div class="container text-center">
      <div v-for="role in ['Batsman', 'Bowler', 'All rounder']" :key="role">
        <h1>{{ role }}</h1>
        <div class="row">
          <div v-for="player in filteredPlayersByRole(role)" :key="player.name" class="col-md-4 mb-4">
            <div class="card">
              <div class="card-body">
                <h5 class="card-title">{{ player.name }}</h5>
                <p class="card-text">Matches: {{ player.matches }}</p>
                <p class="card-text">Runs: {{ player.runs }}</p>
                <p class="card-text">50/100s: {{ player['50s'] }}/{{ player['100s'] }}</p>
                <p class="card-text">Highest Score: {{ player.highest_score }}</p>
                <p class="card-text">Team Name: {{ player.team_name }}</p>
                <p class="card-text">Best Bowling Figures: {{ player.best_bowling_figures }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      players: [],
      selectedTeam: 'ALL',
      searchQuery: '',
      teams: [],
    };
  },
  mounted() {
    // Fetch the players data from the public folder
    fetch('/players.json')
      .then(response => response.json())
      .then(data => {
        this.players = data.originalPlayers;
        this.teams = [...new Set(data.originalPlayers.map(player => player.team_name))]; // Dynamically get all team names
      })
      .catch(error => console.error('Error fetching player data:', error));
  },
  methods: {
    // Filter players based on the selected team
    filterPlayers() {
      return this.players.filter(player => 
        this.selectedTeam === 'ALL' || player.team_name === this.selectedTeam
      );
    },
    // Filter players by name based on search query
    searchPlayers() {
      return this.filterPlayers().filter(player =>
        player.name.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    },
    // Filter players by role (Batsman, Bowler, All rounder)
    filteredPlayersByRole(role) {
      let roleValue = 2; // Batsman by default
      if (role === 'Bowler') roleValue = 4;
      if (role === 'All rounder') roleValue = 3;
      return this.searchPlayers().filter(player => player.role === roleValue);
    },
  },
};
</script>

<style scoped>
.card {
  margin: 20px;
  width: 18rem;
}
</style>
