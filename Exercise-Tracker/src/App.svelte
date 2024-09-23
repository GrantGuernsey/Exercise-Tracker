<script>
  import Body from './Body.svelte';
  import ExercisePane from './ExercisePane.svelte';
  import ExerciseLogger from './ExerciseLogger.svelte';
  import GoalPane from './GoalPane.svelte';
  import PreviousWorkoutPane from './PreviousWorkoutPane.svelte';

  let userName = "Grant Guernsey"; // Replace with the actual user's name
  let startDate = new Date("2024-09-01"); // Replace with the actual start date
  let currentDate = new Date();
  let showPrevious = false;
  let successMessage = ""; // <-- New state to store the success message

  let currentWorkoutIndex = 0;
  // Calculate the total days since start date
  let totalDaysUsed = Math.floor((currentDate - startDate) / (1000 * 60 * 60 * 24));


  let muscleGroups = ['Chest', 'Back', 'Legs', 'Arms', 'Shoulders'];
  let exercises = {
    Chest: [{ name: 'Bench Press', image: 'images/bench_press.png' }, { name: 'Chest Fly', image: 'images/chest-fly.png' }],
    Back: [{ name: 'Pull Up', image: 'images/pull_up.png' }, { name: 'Row', image: 'images/row.png' }],
    Legs: [{ name: 'Squat', image: 'images/squat.png' }, { name: 'Leg Press', image: 'images/leg-press.png' }],
    Arms: [{ name: 'Bicep Curl', image: 'images/bicep-curl.png' }, { name: 'Tricep Dip', image: 'images/dips.png' }],
    Shoulders: [{ name: 'Shoulder Press', image: 'images/shoulder-press.png' }, { name: 'Lateral Raise', image: 'images/lat-raise.png' }]
  };

  let ex = {
    Chest: ['Bench Press', 'Chest Fly'],
    Back: ['Pull Up', 'Row'],
    Legs: ['Squat', 'Leg Press'],
    Arms: ['Bicep Curl', 'Tricep Dip'],
    Shoulders: ['Shoulder Press', 'Lateral Raise']
  };

  let allExercises = Object.values(ex).flat(); // Flatten all exercises into a single array

  let dummyWorkouts = [
  {
    "mood": 3,
    "Shoulder Press": {
      "set1": { "reps": 12, "weight": 20 },
      "set2": { "reps": 10, "weight": 22 },
      "set3": { "reps": 8, "weight": 25 }
    },
    "Lateral Raise": {
      "set1": { "reps": 12, "weight": 12 },
      "set2": { "reps": 10, "weight": 14 }
    }
  },
  {
    "mood": 4,
    "Bench Press": {
      "set1": { "reps": 10, "weight": 50 },
      "set2": { "reps": 8, "weight": 55 }
    },
    "Squat": {
      "set1": { "reps": 12, "weight": 25 },
      "set2": { "reps": 10, "weight": 30 }
    }
  },
  {
    "mood": 2,
    "Squat": {
      "set1": { "reps": 15, "weight": 60 },
      "set2": { "reps": 12, "weight": 65 }
    },
    "Bench Press": {
      "set1": { "reps": 12, "weight": 120 },
      "set2": { "reps": 10, "weight": 130 }
    }
  },
  {
    "mood": 1,
    "Pull Up": {
      "set1": { "reps": 8, "weight": 0 },
      "set2": { "reps": 6, "weight": 0 }
    },
    "Row": {
      "set1": { "reps": 12, "weight": 60 },
      "set2": { "reps": 10, "weight": 65 }
    }
  },
  {
    "mood": 3,
    "Bicep Curl": {
      "set1": { "reps": 15, "weight": 15 },
      "set2": { "reps": 12, "weight": 18 }
    },
    "Tricep Dip": {
      "set1": { "reps": 12, "weight": 0 },
      "set2": { "reps": 10, "weight": 0 }
    }
  },
  {
    "mood": 4,
    "Chest Fly": {
      "set1": { "reps": 12, "weight": 25 },
      "set2": { "reps": 10, "weight": 30 }
    },
    "Leg Press": {
      "set1": { "reps": 12, "weight": 100 },
      "set2": { "reps": 10, "weight": 120 }
    }
  },
  {
    "mood": 5,
    "Pull Up": {
      "set1": { "reps": 10, "weight": 0 },
      "set2": { "reps": 8, "weight": 0 }
    },
    "Row": {
      "set1": { "reps": 10, "weight": 65 },
      "set2": { "reps": 8, "weight": 70 }
    }
  },
  {
    "mood": 2,
    "Bicep Curl": {
      "set1": { "reps": 12, "weight": 18 },
      "set2": { "reps": 10, "weight": 20 }
    },
    "Tricep Dip": {
      "set1": { "reps": 10, "weight": 0 },
      "set2": { "reps": 8, "weight": 0 }
    }
  },
  {
    "mood": 4,
    "Lateral Raise": {
      "set1": { "reps": 15, "weight": 12 },
      "set2": { "reps": 12, "weight": 14 }
    },
    "Shoulder Press": {
      "set1": { "reps": 10, "weight": 25 },
      "set2": { "reps": 8, "weight": 28 }
    }
  }
];


  // Toggle between current workout logger and previous workouts view
  const toggleView = () => {
    showPrevious = !showPrevious;
    showLogger = false;
  };

  // Navigate through the previous workouts
  const nextWorkout = () => {
    if (currentWorkoutIndex < dummyWorkouts.length - 1) {
      currentWorkoutIndex++;
    }
  };

  const prevWorkout = () => {
    if (currentWorkoutIndex > 0) {
      currentWorkoutIndex--;
    }
  };

  // Function to handle saving the edits made to previous workouts
  const saveEdits = (workoutIndex, exercise, set, reps, weight) => {
    dummyWorkouts[workoutIndex][exercise][set].reps = reps;
    dummyWorkouts[workoutIndex][exercise][set].weight = weight;
    alert("Changes saved successfully!");
  };

  let daysActive = dummyWorkouts.length;
  let selectedMuscleGroup = '';
  let selectedExercises = [];
  let showLogger = false;

  const handleMuscleGroupSelect = (muscleGroup) => {
    selectedMuscleGroup = muscleGroup;
  };

  const handleAddExercise = (exercise) => {
    selectedExercises = [...selectedExercises, exercise];
    successMessage = ""; // <-- Clear previous success message
  };

  const handleShowLogger = () => {
    showPrevious = false;
    if(showLogger){
      showLogger = false;
    }
    else{
      showLogger = true;
    }
  };

  const handleLogSubmit = (logDetails) => {
    console.log('Logged Details (JSON):', JSON.stringify(logDetails, null, 2));
    daysActive += 1;
    dummyWorkouts.unshift(logDetails);
    selectedExercises = [];
    showLogger = false;
    successMessage = "Exercise logged!"; // <-- Display success message
    alert("Exercise logged successfully!");
  };

  const handleRemoveExercise = (index) => {
    selectedExercises = selectedExercises.filter((_, i) => i !== index);
    successMessage = "";
  };

  const saveMood = (workoutIndex, newMood) => {
    dummyWorkouts[workoutIndex].mood = newMood;
    alert("Mood updated successfully!");
  };

  // State for GoalPane
  let showGoalsPane = false;
  let goals = {};
  const handleSetGoals = () => {
    showGoalsPane = !showGoalsPane;
  };

  const handleCloseGoalsPane = (goalsData) => {
    goals = goalsData; // Update the goals variable with the received data
    showGoalsPane = false; // Close the goals pane
    console.log('Received Goals:', goals);
  };

  let selectedMuscleGroupToRemove = '';

const removeSelectedMuscleGroup = () => {
  if (selectedMuscleGroupToRemove && confirm(`Are you sure you want to remove the ${selectedMuscleGroupToRemove} muscle group? This action cannot be undone.`)) {
    // Remove the muscle group from the exercises object
    delete exercises[selectedMuscleGroupToRemove];

    // Also update allExercises to remove the exercises associated with the muscle group
    //allExercises = allExercises.filter(exercise => !exercises[selectedMuscleGroupToRemove].map(e => e.name).includes(exercise));

    muscleGroups = muscleGroups.filter(group => group !== selectedMuscleGroupToRemove);

    // Clear the selected muscle group to remove
    selectedMuscleGroupToRemove = '';

    successMessage = `${selectedMuscleGroupToRemove} muscle group removed successfully.`;
  }
};

let currentTheme = 'light'; // Default theme

const changeTheme = () => {
  if (currentTheme === 'light') {
    document.documentElement.style.setProperty('--light-shade', '#f3f4ee');
    document.documentElement.style.setProperty('--light-accent', '#9795A4');
    document.documentElement.style.setProperty('--brand-color', '#7E86BD');
    document.documentElement.style.setProperty('--dark-accent', '#756E7D');
    document.documentElement.style.setProperty('--dark-shade', '#294066');
    currentTheme = 'dark';
  } else {
    document.documentElement.style.setProperty('--light-shade', '#ffffff'); // Change as needed
    document.documentElement.style.setProperty('--light-accent', '#cccccc'); // Change as needed
    document.documentElement.style.setProperty('--brand-color', '#000000'); // Change as needed
    document.documentElement.style.setProperty('--dark-accent', '#444444'); // Change as needed
    document.documentElement.style.setProperty('--dark-shade', '#222222'); // Change as needed
    currentTheme = 'light';
  }
};
</script>

<style>
  .app {
    font-family: Arial, sans-serif;
    margin: 1rem;
    color: var(--dark-shade); /* Use dark shade for text color */
    display: flex;
    flex-direction: column;
  }
  .theme-button {
    position: absolute; /* Position it at the top left */
    top: 20px;
    left: 20px;
    padding: 10px;
    background-color: var(--brand-color);
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }
  .pane-container {
    display: flex;
    justify-content: center; /* Center items when no right pane */
    flex-wrap: wrap; /* Allow wrapping if needed */
  }

  .left-pane {
    flex: 1; /* Allow left pane to take available space */
    max-width: 600px; /* Optional: limit max width */
  }

  .right-pane {
    margin-left: 20px; /* Space between left and right panes */
    flex: 0 0 300px; /* Fixed width for the right pane */
  }

  .button {
    margin-top: 1rem;
    background-color: var(--dark-shade); /* Dark shade for button background */
    color: white; /* White text color for contrast */
    border: none;
    border-radius: 5px;
    cursor: pointer;
    padding: 0.6em 1.2em; /* Adjusted padding for consistency */
    transition: background-color 0.3s, border-color 0.3s;
  }
  .button:hover {
    background-color: var(--dark-accent); /* Brand color for button hover state */
  }
  .prev-btn {
    margin-top: 1rem;
    background-color: var(--brand-color); /* Brand color for previous button */
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    padding: 0.6em 1.2em; /* Adjusted padding for consistency */
    transition: background-color 0.3s;
  }
  .prev-btn:hover {
    background-color: var(--dark-accent); /* Dark accent for hover state */
  }
  .exercise-pane {
    margin-top: 2rem; /* Space between the controls and exercise pane */
  }
  .workout-view {
    margin-top: 1rem;
    padding: 1rem;
    border: 1px solid var(--dark-accent); /* Dark accent for border */
    border-radius: 5px;
    background-color: var(--light-shade); /* Light shade for background */
    color: var(--dark-shade); /* Dark shade for text color */
  }

  .workout-view h2 {
    color: var(--dark-shade); /* Dark shade for headings */
  }

  .workout-view h3 {
    color: var(--dark-accent); /* Dark accent for subheadings */
  }

  .workout-view label {
    display: block;
    margin-bottom: 0.5rem;
    color: var(--dark-shade); /* Dark shade for label text */
  }

  .workout-view input[type="number"],
  .workout-view input[type="range"] {
    margin-right: 0.5rem;
    border: 1px solid var(--dark-accent); /* Dark accent for input borders */
    border-radius: 5px;
    padding: 0.25rem;
    background-color: var(--light-shade); /* Light shade for input background */
  }

  .workout-view button {
    border-radius: 8px;
    border: 1px solid transparent;
    padding: 0.6em 1.2em;
    font-size: 1em;
    font-weight: 500;
    background-color: var(--brand-color); /* Main brand color for buttons */
    color: white;
    cursor: pointer;
    transition: background-color 0.3s, border-color 0.25s;
  }

  .rep-input, .weight-input{
    color: var(--dark-accent)
  }

  .workout-view button:hover {
    background-color: var(--dark-accent); /* Dark accent on hover */
    border-color: var(--dark-accent); /* Dark accent for border on hover */
  }

  .workout-view button:focus,
  .workout-view button:focus-visible {
    outline: 4px auto var(--brand-color); /* Main brand color for focus outline */
  }

  .navigation {
    margin-top: 20px;
  }

  .navigation button {
    margin: 0 10px;
    background-color: var(--brand-color); /* Main brand color for navigation buttons */
    color: white;
  }

  .navigation button:disabled {
    background-color: var(--dark-accent); /* Dark accent for disabled navigation buttons */
    cursor: not-allowed;
  }
  .success-message {
    color: green; /* Consider using a green from your palette for consistency */
    font-weight: bold;
    margin: 0 10px;
  }

  .controls {
    display: flex;
    flex-direction: column; /* Stack buttons vertically */
    margin-bottom: 1rem;
  }
  select {
        color: var(--dark-accents);
        background-color: transparent; /* Optional: To keep it consistent */
    }
    option {
        color: var(--dark-accents);
    }
</style>


<div class="app">
  <button class="theme-button" on:click={changeTheme}>
    Switch Theme
  </button>

  <div class = "header">
    <h1>Hello, {userName}!</h1>
    
    <!-- Display current date and time -->
    <p>Current date and time: {currentDate}</p>
    
    <!-- Display usage statistics -->
    <p>You have used the interface for {totalDaysUsed} days.</p>
    <p>You have been active for {daysActive} days.</p>
  </div>
  
  <div class = "controls">
    <!-- Button to toggle between current workout logger and previous workouts -->
    <button class="prev-btn" on:click={toggleView}>
      {showPrevious ? "Return to Current Logger" : "View Previous Workouts"}
    </button>
    
    <button class="prev-btn" on:click={handleSetGoals}>
      Set Goals
    </button>


  <div>
    <label for="muscle-group-dropdown">Select Muscle Group to Remove:</label>
    <select id="muscle-group-dropdown" bind:value={selectedMuscleGroupToRemove}>
      <option value="" disabled selected>Select muscle group</option>
      {#each muscleGroups as muscleGroup}
        <option value={muscleGroup}>{muscleGroup}</option>
      {/each}
    </select>
    <button class="button" on:click={removeSelectedMuscleGroup} disabled={!selectedMuscleGroupToRemove}>
      Remove Muscle Group
    </button>
  </div>

    <PreviousWorkoutPane exercises={allExercises} workouts={dummyWorkouts} goalsData = {goals} />
  </div>

  <div class = "pane-container">
    <div class = "left-pane">

      <Body 
        muscleGroups={muscleGroups} 
        selectedMuscleGroup={selectedMuscleGroup}
        onSelect={handleMuscleGroupSelect}
      />
    </div>

    {#if selectedMuscleGroup}
      <div class = "right-pane">
        <ExercisePane 
          exercises={exercises[selectedMuscleGroup]} 
          onAddExercise={handleAddExercise}
        />

        {#if selectedExercises.length > 0}
        <button class="button" on:click={handleShowLogger}>
          View {selectedExercises.length} Exercises Added
        </button>
      {/if}
      {#if successMessage}
      <p class="success-message">{successMessage}</p>
      {/if}
      </div>
    {/if}

    {#if selectedExercises.length > 0 || showLogger || showPrevious}
      <div class = "right-pane">


        {#if showLogger}
          <ExerciseLogger 
            exercises={selectedExercises} 
            onSubmit={handleLogSubmit}
            onRemove={handleRemoveExercise}
          />
        {/if}

        {#if showPrevious}
        <div class="workout-view">
          <h2>Previous Workout {currentWorkoutIndex + 1}</h2>

          <!-- Check if the workout contains a mood entry -->
          {#if dummyWorkouts[currentWorkoutIndex].mood !== undefined}
            <div>
              <h3>Mood</h3>
              <label>Mood: {dummyWorkouts[currentWorkoutIndex].mood}</label>
              <input type="range" min="1" max="5" step="1" value={dummyWorkouts[currentWorkoutIndex].mood} 
                on:input={(e) => dummyWorkouts[currentWorkoutIndex].mood = +e.target.value} />
              <button on:click={() => saveMood(currentWorkoutIndex, dummyWorkouts[currentWorkoutIndex].mood)}>
                Save Mood
              </button>
            </div>
          {/if}

          <!-- Iterate over the exercises -->
          {#each Object.entries(dummyWorkouts[currentWorkoutIndex]) as [exerciseName, sets]}
            {#if exerciseName !== 'mood'} <!-- Skip mood from being treated as an exercise -->
            <div>
              <h3>{exerciseName}</h3>
              {#each Object.entries(sets) as [setName, set]}
                <div>
                  <p>{setName}</p>
                  <div>
                    <p>Reps:</p>
                    <input class="rep-input" type="number" value={set.reps} min="0" on:input={(e) => set.reps = +e.target.value} />
                  </div>
                  <div>
                    <p>Weight:</p>
                    <input class="weight-input" type="number" value={set.weight} min="0" on:input={(e) => set.weight = +e.target.value} />
                  </div>
                  <button on:click={() => saveEdits(currentWorkoutIndex, exerciseName, setName, set.reps, set.weight)}>
                    Save
                  </button>
                </div>
              {/each}
            </div>
            
            {/if}
          {/each}

          <!-- Navigation buttons for previous/next workouts -->
          <div class="navigation">
            <button on:click={prevWorkout} disabled={currentWorkoutIndex === 0}>Previous Workout</button>
            <button on:click={nextWorkout} disabled={currentWorkoutIndex === dummyWorkouts.length - 1}>Next Workout</button>
          </div>
        </div>
        {/if}
      </div>
    {/if}
  </div>
  <GoalPane showGoalsPane={showGoalsPane} onClose={handleCloseGoalsPane} exercises={allExercises} />

</div>
