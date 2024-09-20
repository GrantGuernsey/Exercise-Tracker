<script>
    import ExerciseCard from './ExerciseCard.svelte';
  
    export let exercises = [];
    export let onAddExercise;
  
    const handleAddExercise = (exercise) => {
      onAddExercise(exercise);
    };
  </script>
  
  <style>
    .exercise-pane {
      margin-top: 1rem;
    }
  </style>
  
  <div class="exercise-pane">
    <h2>Exercises</h2>
    {#each exercises as exercise}
      <ExerciseCard 
        exercise={exercise} 
        onAdd={handleAddExercise} 
      />
    {/each}
  </div>
  