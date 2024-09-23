<script>
  // ... other imports
  import { onMount, tick, onDestroy } from 'svelte';
  import Chart from 'chart.js/auto';
  
  export let exercises = [];
  export let workouts = [];
  export let goalsData = {};
  
  let selectedExercise = '';
  let chartData = [];
  
  let chart;

  async function fetchWorkoutData() {
    if (selectedExercise) {
      chartData = [];

      workouts.forEach((workout, index) => {
        const exerciseSets = workout[selectedExercise];

        if (exerciseSets) {
          for (const setKey in exerciseSets) {
            const { reps, weight } = exerciseSets[setKey];
            chartData.push({ x: index + 1, y: weight });
          }
        }
      });

      await tick();
      createOrUpdateChart();
    }
  }

  function createOrUpdateChart() {
    const ctx = document.getElementById('progressChart')?.getContext('2d');

    if (ctx) {
      const reversedChartData = [...chartData].reverse();

      if (chart) {
        // Update existing chart
        chart.data.labels = reversedChartData.map(d => d.x);
        chart.data.datasets[0].data = reversedChartData.map(d => d.y);

        // Clear previous goal lines
        chart.data.datasets = chart.data.datasets.filter(ds => !ds.label.includes('Goal'));

        // Add goal lines if applicable
        addGoalLines(chart);

        chart.update();
      } else {
        // Create new chart
        chart = new Chart(ctx, {
          type: 'line',
          data: {
            labels: reversedChartData.map(d => d.x),
            datasets: [{
              label: 'Max Weight',
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
                  text: 'Max Weight'
                }
              }
            }
          }
        });

        // Add goal lines if applicable
        addGoalLines(chart);
      }
    }
  }

  function addGoalLines(chart) {
    // Check if goals exist for the selected exercise and add them
    if (selectedExercise === goalsData.exercise1 && goalsData.exercise1) {
      chart.data.datasets.push({
        label: `${goalsData.exercise1} Goal`,
        data: new Array(chart.data.labels.length).fill(goalsData.weight1),
        borderColor: 'rgba(255, 99, 132, 1)',
        borderWidth: 1,
        borderDash: [5, 5],
        fill: false
      });
    }

    if (selectedExercise === goalsData.exercise2 && goalsData.exercise2) {
      chart.data.datasets.push({
        label: `${goalsData.exercise2} Goal`,
        data: new Array(chart.data.labels.length).fill(goalsData.weight2),
        borderColor: 'rgba(54, 162, 235, 1)',
        borderWidth: 1,
        borderDash: [5, 5],
        fill: false
      });
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
  