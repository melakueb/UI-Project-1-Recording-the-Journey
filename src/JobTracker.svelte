<script> 

    //Name
    //Current Date and Time
    //How long they have used the interface (beginning)
    //How much they have used the interface (active)

    let user = {
        name: "Elshaddai",
        startDate: new Date("2025-06-10")
    }

    let today = new Date();

    //Track entries with array
    let entries = [
        {
            date: "2025-09-14",
            mood: "happy",
            jobsApplied: 2,
            networking: "Yes",
            reflection: "Feeling good about my job search progress.",
            hours: 1
        }
    ]

    //For L1
    let daysBeginning = Math.round((today - user.startDate) / (1000*60*60*24))
    let daysActive = entries.length;

    //Today's entry, empty variables
    let newEntryDate = today.toISOString().slice(0,10)
    let mood = "";
    let jobsApplied = 0;
    let networking = "No";
    let reflection = "";
    let hours = 0;

    let savedMessage = "";

    //For L2
    let selectedDate = ""
    let selectedEntry = null;

    //For L3
    let currentTab ="goals" //can be "goals", "overview", "customization"
    let goalJobs = 0;
    let goalHours = 0;
    let darkMode = false;

    //Save or update entry
    function saveEntry() {
        // Create entry object
        let entry = {
            date: newEntryDate,
            mood,
            jobsApplied,
            networking,
            reflection,
            hours
        }

        // If entry exists for that date, replace it
        let existingIndex = entries.findIndex(e => e.date === newEntryDate)
        if (existingIndex !== -1) {
            entries[existingIndex] = entry
            savedMessage = "Entry updated for " + newEntryDate
        } 
        else {
            entries.push(entry)
            savedMessage = "Entry saved for " + newEntryDate
        }
        daysActive = entries.length
    }

    //Review entry from selected date
    function reviewEntry() {
        selectedEntry = entries.find(e => e.date === selectedDate);
        if (selectedEntry) {
            // Load into form fields too (so it can be edited)
            newEntryDate = selectedEntry.date;
            mood = selectedEntry.mood;
            jobsApplied = selectedEntry.jobsApplied;
            networking = selectedEntry.networking;
            reflection = selectedEntry.reflection;
            hours = selectedEntry.hours;
            savedMessage = "Loaded entry for " + selectedDate;
        } else {
            savedMessage = "No entry found for this date.";
        }
    }

    //Toggle dark/light mode
    function toggleTheme(){
        darkMode = !darkMode;
        document.body.style.backgroundColor = darkMode ? "#1a1a1a" : "#ffffff";
        document.body.style.color = darkMode ? "#ffffff" : "#000000";
    }

    //Load entry into form when switching dates
    function loadEntry(date) {
        let entry = entries.find(e => e.date === date)
        if (entry) {
            newEntryDate = entry.date
            mood = entry.mood
            jobsApplied = entry.jobsApplied
            networking = entry.networking
            reflection = entry.reflection
            hours = entry.hours
            savedMessage = "Loaded entry for " + date
        } 
        else {
            // Reset form if no entry found
            newEntryDate = date
            mood = ""
            jobsApplied = 0
            networking = "No"
            reflection = ""
            hours = 0
            savedMessage = "No entry yet for " + date + " (new entry)"
        }
    }
</script>

<main class = "Overview">
    <h2>Job Search Tracker</h2>

    <!--L1-->
    <h4>User: {user.name} | Date: {today.toLocaleDateString()} {today.toLocaleTimeString()} </h4>
    <h4>Days since start: {daysBeginning} | Total days active: {daysActive} </h4>

    <hr />

    <h3>Log Today's Activity</h3>
    
    <label>
        Entry Date:
        <input type="date" bind:value={newEntryDate} />
    </label>
    <br />

    <label>
        Mood:
        <select bind:value={mood}>
            <option value="" disabled selected>Select your mood</option>
            <option value="happy">Happy</option>
            <option value="neutral">Neutral</option>
            <option value="frustrated">Frustrated</option>
            <option value="tired">Tired</option>
            <option value="confident">Confident</option>
        </select>
    </label>
    <br />

    <label>
        Jobs Applied:
        <input type="number" bind:value={jobsApplied} min="0" />
    </label>
    <br />

    <label>
        Networking activity today?:
        <select bind:value={networking}>
            <option value="No">No</option>
            <option value="Yes">Yes</option>
        </select>
    </label>
    <br />

    <label>
        Reflection:
        <textarea bind:value={reflection} rows="4" cols="50" placeholder="Write your thoughts here..."></textarea>
    </label>
    <br />

    <label>
        Hours spent job searching/networking today:
        <input type="number" bind:value={hours} min="0" step="0.5" />
    </label>
    <br />

    <button on:click={saveEntry}>Save Entry</button>
    <p>{savedMessage}</p>

    <hr />

    <!--L2-->
    <h3>Review Past Entry</h3>
    <label>
        Select Date to Review:
        <input type="date" bind:value={selectedDate} />
    </label>
    <button on:click={reviewEntry}>Review Entry</button>
    
    {#if selectedEntry}
    <div class="entry-box">
        <h4>Entry for {selectedEntry.date}:</h4>
        <p>Mood: {selectedEntry.mood}</p>
        <p>Jobs Applied: {selectedEntry.jobsApplied}</p>
        <p>Networking: {selectedEntry.networking}</p>
        <p>Reflection: {selectedEntry.reflection}</p>
        <p>Hours: {selectedEntry.hours}</p>
    </div>
    {/if}

    <hr />

    <!--L3-->
    <h3>More Features</h3>
    <button on:click={() => currentTab = "goals"}>Set Goals</button>
    <button on:click={() => currentTab = "overview"}>Overview</button>
    <button on:click={() => currentTab = "customization"}>Customization</button>

    {#if currentTab === "goals"}
        <div class = "goals">
            <h4>Set Daily Goals</h4>
            <label>
                Jobs to apply:
                <input type="number" bind:value={goalJobs} min="0" />
            </label>
            <br />
            <label>
                Hours applying/networking:
                <input type="number" bind:value={goalHours} min="0" step="0.5" />
            </label>
        </div>
    {/if}

    {#if currentTab === "overview"}
        <div class = "overview">
            <h4>Progress Overview</h4>
            <p>Goal Jobs Per Day: {goalJobs}, Today's Jobs: {jobsApplied}</p>
            <p>Goal Hours Per Day: {goalHours}, Today's Hours: {hours}</p>
            <p>Total jobs applied: {entries.reduce((sum,e)=> sum+e.jobsApplied, 0)}</p>
        </div>
    {/if}

    {#if currentTab === "customization"}
        <div class = "customization">
            <h4>Customization</h4>
            <button on:click={toggleTheme}>{darkMode ? "Light Mode" : "Dark Mode"}</button>
        </div>
    {/if}
</main>

