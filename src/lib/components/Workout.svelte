<script>

    const REFRESHES_PER_SECOND = 10;

    const workout1 = [
        {"time": "0:00", "intensity": "Easy", "rpm": "60-100", "duration": 90},
        {"time": "1:30", "intensity": "Moderate", "rpm": "60-100", "duration": 180},
        {"time": "4:30", "intensity": "Easy", "rpm": "55-65", "duration": 45},
        {"time": "5:15", "intensity": "Hard", "rpm": "55-65", "duration": 120},
        {"time": "7:15", "intensity": "Easy", "rpm": "80-90", "duration": 55},
        {"time": "8:10", "intensity": "All Out", "rpm": "80+", "duration": 20},
        {"time": "8:30", "intensity": "Easy", "rpm": "80-90", "duration": 30},
        {"time": "9:00", "intensity": "All Out", "rpm": "80+", "duration": 20},
        {"time": "9:20", "intensity": "Easy", "rpm": "80-90", "duration": 30},
        {"time": "9:50", "intensity": "All Out", "rpm": "80+", "duration": 20},
        {"time": "10:10", "intensity": "Easy", "rpm": "55-65", "duration": 40},
        {"time": "10:50", "intensity": "Hard", "rpm": "55-65", "duration": 180},
        {"time": "13:50", "intensity": "Easy", "rpm": "60-70", "duration": 30},
        {"time": "14:20", "intensity": "All Out", "rpm": "60+", "duration": 20},
        {"time": "14:40", "intensity": "Easy", "rpm": "60-70", "duration": 80},
        {"time": "16:00", "intensity": "All Out", "rpm": "60+", "duration": 20},
        {"time": "16:20", "intensity": "Easy", "rpm": "60-70", "duration": 30},
        {"time": "16:50", "intensity": "All Out", "rpm": "60+", "duration": 20},
        {"time": "17:10", "intensity": "Easy", "rpm": "55-65", "duration": 70},
        {"time": "18:20", "intensity": "Hard", "rpm": "55-65", "duration": 120},
        {"time": "20:20", "intensity": "Easy", "rpm": "60+", "duration": 40}
    ]

    const workout2 = [
        {"time": "0:00", "intensity": "Easy", "rpm": "60-100", "duration": 5},
        {"time": "1:30", "intensity": "Moderate", "rpm": "60-100", "duration": 10},
        {"time": "4:30", "intensity": "Easy", "rpm": "55-65", "duration": 5},
        {"time": "5:15", "intensity": "Hard", "rpm": "55-65", "duration": 12}
    ]

    // set default prop value
    export const workoutData = workout2;

    function convertTime(secs) {
        const minutes = Math.floor(secs / 60);
        const seconds = secs % 60;

        if (minutes === 0) {
            return "" + seconds;
        }
        else if (seconds < 10) {
            return minutes + ":0" + seconds;
        }
        else {
            return minutes + ":" + seconds;
        }

    }

    let progressVal = 0;

    let progressIncrement = (100 / workoutData[0].duration) / REFRESHES_PER_SECOND;

    let totalSecs = 0;

    let counter = 0;

    let currIndex = 0;

    let currEventTimeLeft = workoutData[currIndex].duration;

    let isPaused = false;

    let intervalID = setInterval(render, 1000 / REFRESHES_PER_SECOND);

    let workoutFinished = false;

    function nextBlock() {
        if (currIndex+1 >= workoutData.length) {
            clearInterval(intervalID);
            workoutFinished = true;
        }
        else {
            currIndex += 1;
            progressIncrement = (100 / workoutData[currIndex].duration) / REFRESHES_PER_SECOND;
            currEventTimeLeft = workoutData[currIndex].duration;
            progressVal = 0;
            
        }
    }

    function render() {
        progressVal = progressVal > 100 ? 0 : progressVal + progressIncrement;
        counter = (counter == REFRESHES_PER_SECOND - 1 ? 0 : counter + 1);
        if (counter == REFRESHES_PER_SECOND - 1) {
            if (currEventTimeLeft <= 1) {
                nextBlock();
            }
            else {
                currEventTimeLeft -= 1;
            }
            totalSecs += 1;
        }
    }

    function handlePauseButton() {
        isPaused = !isPaused;
        if (isPaused) {
            clearInterval(intervalID)
        }
        else {
            intervalID = setInterval(render, 1000 / REFRESHES_PER_SECOND);
        }
    }

    function handleNext() {
        totalSecs += currEventTimeLeft;
        nextBlock();
    }

</script>

{#if (workoutFinished)}
    <div class="w-3/4 max-w-md mx-auto border-2 border-white rounded-lg p-3 text-white text-xl bg-white bg-opacity-5 border-opacity-20 text-opacity-50">
        <p>Finished!</p>
    </div>
{:else}
    <div class="w-3/4 max-w-md mx-auto border-2 border-white rounded-lg p-3 text-white text-xl bg-white bg-opacity-5 border-opacity-20 text-opacity-50">
        <p>Intensity: {workoutData[currIndex].intensity}</p>
        <p>RPM: {workoutData[currIndex].rpm}</p>
        <p>Time Remaining in Block: {convertTime(currEventTimeLeft)}</p>
        

        <div class="flex w-full h-1.5 bg-gray-500 rounded-full overflow-hidde mt-8" role="progressbar" aria-valuenow="{progressVal}" aria-valuemin="0" aria-valuemax="100">
            <div class=" opacity-50 flex flex-col justify-center rounded-full overflow-hidden bg-white text-xs text-white text-center whitespace-nowrap" style="width: {progressVal}%"></div>
        </div>

        
    </div>

    <div class="w-3/4 max-w-md mx-auto border-2 border-white rounded-lg p-3 text-white text-xl bg-white bg-opacity-5 border-opacity-20 text-opacity-50 m-8">
        <p>Time Elapsed: {convertTime(totalSecs)}</p>
    </div>
    <div class="w-3/4 max-w-md mx-auto text-center">
        <button on:click={handlePauseButton} class="bg-white bg-opacity-5 border-opacity-20 text-white text-opacity-50 py-2 px-4 border-2 border-white rounded hover:border-opacity-50 hover:text-opacity-80">
            {isPaused ? "Resume" : "Pause"}
        </button>
        <button on:click={handleNext} class="bg-white bg-opacity-5 border-opacity-20 text-white text-opacity-50 py-2 px-4 border-2 border-white rounded hover:border-opacity-50 hover:text-opacity-80">
            Next Block
        </button>
    </div>
{/if}

    


