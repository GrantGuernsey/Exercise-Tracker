<script>
    import { onMount, tick, onDestroy } from 'svelte';
    import Chart from 'chart.js/auto';
    export let exercises = [];
    export let workouts = [];
    let selectedExercise = '';
    let chartData = [];
    
    let chart;
  
    async function fetchWorkoutData() {
      if (selectedExercise) {
        // Reset chartData
        chartData = [];
  
        // Loop through each workout to find sets for the selected exercise
        workouts.forEach((workout, index) => {
          const exerciseSets = workout[selectedExercise];  // Access the exercise sets
    
          if (exerciseSets) {
            // Calculate max volume for each set
            for (const setKey in exerciseSets) {
              const { reps, weight } = exerciseSets[setKey];
              chartData.push({ x: index + 1, y: reps * weight });
            }
          }
        });
  
        // Wait for DOM updates to complete
        await tick();
  
        // Update or create the chart
        createOrUpdateChart();
      }
    }
  
    // Function to create or update the chart
    function createOrUpdateChart() {
      const ctx = document.getElementById('progressChart')?.getContext('2d');
      
      if (ctx) {
        const reversedChartData = [...chartData].reverse(); // Reverse the chartData array

        if (chart) {
          // Update the existing chart
          chart.data.labels = reversedChartData.map(d => d.x);
          chart.data.datasets[0].data = reversedChartData.map(d => d.y);
          chart.update();
        } else {
          // Create a new chart
          chart = new Chart(ctx, {
            type: 'line',
            data: {
              labels: reversedChartData.map(d => d.x), // Workout numbers
              datasets: [{
                label: 'Max Volume',
                data: reversedChartData.map(d => d.y),
                borderColor: 'rgba(75, 192, 192, 1)',
                borderWidth: 2,
                fill: false
              }]
            },
            options: {
              scales: {
                x: {
                  title: {
                    display: true,
                    text: 'Workout Number'
                  }
                },
                y: {
                  title: {
                    display: true,
                    text: 'Max Volume (reps * weight)'
                  }
                }
              }
            }
          });
        }
      }
    }
    
    onDestroy(() => {
    if (chart) {
      chart.destroy();
    }
    });

  </script>
  
  <style>
    .graph-container {
      width: 100%;
      height: 400px;
    }
    select {
        color: var(--dark-accents);
        background-color: transparent; /* Optional: To keep it consistent */
    }
    option {
        color: var(--dark-accents);
    }
  </style>
  
  <div>
    <label for="exercise-dropdown">Select Exercise:</label>
    <select id="exercise-dropdown" bind:value={selectedExercise} on:change={fetchWorkoutData}>
      <option class = "ex-dr" value="" disabled selected>Select an exercise</option>
      {#each exercises as exercise}
        <option value={exercise}>{exercise}</option>
      {/each}
    </select>
  
    {#if chartData.length > 0}
      <div class="graph-container">
        <canvas id="progressChart"></canvas>
      </div>
    {/if}
  </div>
  