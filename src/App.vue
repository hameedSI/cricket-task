<template>
  <div id="app">
    <h1>Player List</h1>
    <div v-if="loading">Loading...</div>
    <div v-else>
      <!-- Filter or Display Team -->
      <select v-model="selectedTeam" @change="filterByTeam">
        <option value="">All Teams</option>
        <option v-for="team in teams" :key="team" :value="team">{{ team }}</option>
      </select>

      <!-- Players List -->
      <div v-for="player in filteredPlayers" :key="player.name" class="player-card">
        <img :src="player.image" alt="player image" class="player-image" />
        <h2>{{ player.name }}</h2>
        <p>Matches: {{ player.matches }}</p>
        <p>Role: {{ getRole(player.role) }}</p>
        <p>Runs: {{ player.runs }}</p>
        <p>50s/100s: {{ player['50s'] }}/{{ player['100s'] }}</p>
        <p>Highest Score: {{ player.highest_score }}</p>
        <p>Best Bowling Figures: {{ player.best_bowling_figures }}</p>
        <p>Team: {{ player.team_name }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      players: [], // To store player data from the API or JSON file
      filteredPlayers: [], // To store filtered player data
      teams: [], // To store team names for filter
      selectedTeam: '', // For team filter
      loading: true, // To show loading state
    };
  },
  created() {
    this.fetchPlayers(); // Fetch data when component is created
  },
  methods: {
    async fetchPlayers() {
      try {
        // Fetching data from a local JSON file or API endpoint
        const response = await axios.get('../assets/players.json'); // Update with actual path if local
        this.players = response.data.originalPlayers;
        this.filteredPlayers = this.players;
        this.getTeams(); // Get unique team names for filtering
        this.loading = false; // Data loaded successfully
      } catch (error) {
        console.error('Error fetching player data:', error);
      }
    },
    getTeams() {
      // Get unique team names
      this.teams = [...new Set(this.players.map(player => player.team_name))];
    },
    filterByTeam() {
      // Filter players by selected team
      if (this.selectedTeam) {
        this.filteredPlayers = this.players.filter(player => player.team_name === this.selectedTeam);
      } else {
        this.filteredPlayers = this.players; // If no team is selected, show all players
      }
    },
    getRole(roleId) {
      // Map the role ID to a human-readable role name
      const roles = {
        2: 'Batsman',
        3: 'All-rounder',
        4: 'Bowler',
      };
      return roles[roleId] || 'Unknown';
    },
  },
};
</script>

<style>
/* Basic styles for the player card */
.player-card {
  border: 1px solid #ddd;
  padding: 10px;
  margin: 10px;
  display: inline-block;
  text-align: center;
  width: 200px;
  box-shadow: 2px 2px 8px rgba(0, 0, 0, 0.1);
}
.player-image {
  width: 130px;
  height: 150px;
  object-fit: cover;
}
</style>
