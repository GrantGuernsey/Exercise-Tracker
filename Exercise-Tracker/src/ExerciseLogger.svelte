<script>
  export let exercises = [];
  export let onSubmit;
  export let onRemove;

  let moodValue = 3;
  // Initialize logDetails with default values
  let logDetails = exercises.map(() => [{ reps: 0, weight: 0 }]);

  // Update logDetails when exercises change
  $: logDetails = exercises.map(() => [{ reps: 0, weight: 0 }]);

  // Function to handle changes in input fields
  const handleInputChange = (exerciseIndex, setIndex, field, value) => {
    logDetails[exerciseIndex][setIndex][field] = Number(value);
  };

  const handleAddSet = (index) => {
    logDetails[index] = [...logDetails[index], { reps: 0, weight: 0 }];
  };

  const handleRemoveSet = (exerciseIndex, setIndex) => {
    logDetails[exerciseIndex] = logDetails[exerciseIndex].filter((_, i) => i !== setIndex);
  };

  const handleRemoveExercise = (index) => {
    onRemove(index);
  };

  const handleSubmit = () => {
    let formattedLog = {"mood": moodValue};
    exercises.forEach((exercise, index) => {
      formattedLog[exercise.name] = logDetails[index].reduce((acc, set, setIndex) => {
        acc[`set${setIndex + 1}`] = { reps: set.reps, weight: set.weight };
        return acc;
      }, {});
    });
    onSubmit(formattedLog);
    console.log('Logged Details (JSON):', JSON.stringify(formattedLog, null, 2));
  };
</script>

<style>
  .logger {
    margin-top: 1rem;
    padding: 1rem;
    border: 1px solid var(--dark-accent); /* Dark accent for border */
    border-radius: 5px;
    background-color: var(--light-shade); /* Light shade for background */
    color: var(--dark-shade); /* Dark shade for text color */
  }

  .logger-item {
    margin-bottom: 1rem;
  }

  .logger-item label {
    display: block;
    margin-bottom: 0.5rem;
    color: var(--dark-shade); /* Dark shade for label text */
  }

  .logger-item input {
    margin-right: 0.5rem;
    border: 1px solid var(--dark-accent); /* Dark accent for input borders */
    border-radius: 5px;
    padding: 0.25rem;
    background-color: var(--light-shade); /* Light shade for input background */
  }

  
  .add-set {
    cursor: pointer;
    margin-left: 0.5rem;
    color: var(--green-shade); /* Main brand color for add/remove actions */
  }

  .remove-set {
    cursor: pointer;
    margin-left: 0.5rem;
    color: var(--red-shade); /* Main brand color for add/remove actions */
  }

  .remove-exercise {
    cursor: pointer;
    color: var(--red-shade);
    align-items: right;
  }

  .mood-selector {
    align-items: center;
    margin-bottom: 1rem;
  }

  .emoji {
    font-size: 2rem;
    margin: 0 10px;
  }

  input[type="range"] {
    width: 200px;
    accent-color: var(--brand-color); /* Main brand color for range slider */
  }

  .rep-input, .weight-input{
    color: var(--dark-accent)
  }

  .remove-div{
    padding-bottom: 5px;
  }

</style>

<div class="logger">
  <h2>Log Your Exercises</h2>
  {#each exercises as exercise, index}
    <div class="logger-item">
      <h3>
        {exercise.name} 
        <span class="remove-exercise" on:click={() => handleRemoveExercise(index)}>Remove</span>
      </h3>
      {#each logDetails[index] as set, setIndex}
        <div>
          <label>Set {setIndex + 1}:</label>
          <span class = "input-title">Reps:</span>
          <input class = "rep-input" type="number" value={set.reps} placeholder="Reps"  
            on:input={(e) => handleInputChange(index, setIndex, 'reps', e.target.value)} />
            <span class = "input-title">Weight:</span>
          <input class = "weight-input" type="number" value={set.weight} placeholder="Weight" 
            on:input={(e) => handleInputChange(index, setIndex, 'weight', e.target.value)} />
          <div class = "remove-div">
            <span class="remove-set" on:click={() => handleRemoveSet(index, setIndex)}>Remove Set</span>
          </div>
        </div>
      {/each}
      <span class="add-set" on:click={() => handleAddSet(index)}>+ Add Set</span>
    </div>
  {/each}
  <div class="mood-selector">
    <span class="emoji">😞</span> <!-- Frowny face -->
    <input type="range" min="1" max="5" bind:value={moodValue} />
    <span class="emoji">😊</span> <!-- Happy face -->
  </div>
  <button on:click={handleSubmit}>Submit</button>
</div>
