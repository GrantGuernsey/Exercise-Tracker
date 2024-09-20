<script>
export let muscleGroups = [];
export let selectedMuscleGroup;
export let onSelect;

const handleSelect = (muscleGroup) => {
    onSelect(muscleGroup);
};
</script>

<style>
    .muscle-group {
      cursor: pointer;
      margin: 0.5rem 0;
      padding: 0.5rem;
      border: 1px solid var(--dark-accent); /* Dark accent for border */
      border-radius: 5px;
      transition: background-color 0.3s;
      background-color: var(--light-shade); /* Light shade for background */
      color: var(--dark-shade); /* Dark shade for text color */
    }
  
    .muscle-group:hover {
      background-color: var(--light-accent); /* Light accent for hover state */
    }
</style>

<div>
<h2>Select Muscle Group</h2>
{#each muscleGroups as muscleGroup}
    <div class="muscle-group" on:click={() => handleSelect(muscleGroup)}>
    {muscleGroup}
    </div>
{/each}
</div>
