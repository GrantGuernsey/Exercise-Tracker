<script>
    export let showGoalsPane = false;
    export let onClose = () => {};
    export let exercises = []; // Array of exercises to populate the dropdowns
  
    let selectedExercise1 = '';
    let goalWeight1 = '';
    let selectedExercise2 = '';
    let goalWeight2 = '';
  
    // Function to save the goals and close the pane
    const saveGoals = () => {
      // Here you would typically process the goals and generate graphs
      // For now, just logging to the console
      console.log('Goals Set:', {
        exercise1: selectedExercise1,
        weight1: goalWeight1,
        exercise2: selectedExercise2,
        weight2: goalWeight2
      });
      
      // Close the goals pane
      onClose();
    };
  </script>
  
  <style>
    .goals-pane {
      position: fixed;
      right: 0;
      top: 0;
      width: 300px;
      height: 100%;
      background-color: var(--light-shade);
      border-left: 1px solid var(--dark-accent);
      padding: 1rem;
      transition: transform 0.3s ease-in-out;
      transform: translateX(0);
    }
  
    .goals-pane.hidden {
      transform: translateX(100%);
    }
  
    .goal-input {
      margin-bottom: 0.5rem;
    }
  
    .goal-input label {
      display: block;
      margin-bottom: 0.2rem;
    }
  
    .goal-input select, .goal-input input {
      width: 100%;
      padding: 0.2rem;
      border: 1px solid var(--dark-accent);
      border-radius: 5px;
    }
  
    .goal-input button {
      margin-top: 1rem;
      background-color: var(--brand-color);
      color: white;
    }
  </style>
  
  <div class={`goals-pane ${showGoalsPane ? '' : 'hidden'}`}>
    <h2>Set Your One Rep Max Goals</h2>
    
    <div class="goal-input">
      <label for="goalExercise1">Exercise 1:</label>
      <select id="goalExercise1" bind:value={selectedExercise1}>
        <option value="" disabled>Select an Exercise</option>
        {#each exercises as exercise}
          <option value={exercise}>{exercise}</option>
        {/each}
      </select>
      <label for="goalWeight1">Target Weight (lbs):</label>
      <input id="goalWeight1" type="number" bind:value={goalWeight1} placeholder="Enter Target Weight" />
    </div>
    
    <div class="goal-input">
      <label for="goalExercise2">Exercise 2:</label>
      <select id="goalExercise2" bind:value={selectedExercise2}>
        <option value="" disabled>Select an Exercise</option>
        {#each exercises as exercise}
          <option value={exercise}>{exercise}</option>
        {/each}
      </select>
      <label for="goalWeight2">Target Weight (lbs):</label>
      <input id="goalWeight2" type="number" bind:value={goalWeight2} placeholder="Enter Target Weight" />
    </div>
    
    <button on:click={saveGoals}>
      Save Goals
    </button>
  </div>
  