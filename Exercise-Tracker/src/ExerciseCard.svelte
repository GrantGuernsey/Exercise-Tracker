<script>
export let exercise = {};
export let onAdd;

const handleAdd = () => {
    onAdd(exercise);
};
</script>

<style>
    .exercise-card {
      border: 1px solid var(--dark-accent); /* Dark accent for border */
      border-radius: 5px;
      padding: 1rem;
      margin-bottom: 1rem;
      display: flex;
      align-items: center;
      background-color: var(--light-shade); /* Light shade for background */
      color: var(--dark-shade); /* Dark shade for text color */
    }
  
    .exercise-card img {
      width: 50px;
      height: 50px;
      margin-right: 1rem;
    }
  
    .exercise-card button {
      margin-left: auto;
      background-color: var(--brand-color); /* Main brand color for button */
      color: white;
      border: 1px solid var(--brand-color);
      border-radius: 5px;
      padding: 0.5rem 1rem;
      cursor: pointer;
      transition: background-color 0.3s, border-color 0.3s;
    }
  
    .exercise-card button:hover {
      background-color: var(--dark-accent); /* Dark accent for button hover state */
      border-color: var(--dark-accent);
    }
  
    .exercise-card button:focus,
    .exercise-card button:focus-visible {
      outline: 4px auto var(--brand-color); /* Brand color for button focus state */
    }
  </style>

<div class="exercise-card">
<img src={exercise.image} alt={exercise.name} />
<div>
    <h3>{exercise.name}</h3>
</div>
<button on:click={handleAdd}>+</button>
</div>
