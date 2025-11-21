<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Detailed Team Viewer</title>
    <style>
        :root {
            --bg-color: #1a1a1a;
            --card-bg: #2d2d2d;
            --text-color: #ffffff;
            --accent-color: #3388ff;
            --gold: #ffd700;
            --silver: #c0c0c0;
            --bronze: #cd7f32;
        }

        body {
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            margin: 0;
            padding: 20px;
        }

        header {
            text-align: center;
            margin-bottom: 20px;
            border-bottom: 2px solid var(--accent-color);
            padding-bottom: 10px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        
        .header-top {
            width: 100%;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        h1 { margin: 0; font-size: 2.5em; text-transform: uppercase; letter-spacing: 2px; }
        .last-updated { font-size: 0.9em; color: #888; }
        
        #error-message { 
            color: #ff4d4d; 
            font-size: 1.1em; 
            font-weight: bold; 
            margin-top: 15px;
            padding: 10px;
            background: #440000;
            border-radius: 5px;
            width: 90%;
            text-align: center;
        }

        /* Selection Containers */
        #selection-controls {
            display: flex;
            flex-direction: column;
            gap: 20px;
            margin-bottom: 30px;
        }

        .selector-container {
            background-color: #3a3a3a;
            padding: 15px;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.4);
        }

        .selector-options {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            justify-content: center;
        }

        .selector-option {
            background: #4a4a4a;
            color: var(--text-color);
            padding: 8px 12px;
            border-radius: 5px;
            cursor: pointer;
            transition: background 0.2s, transform 0.1s;
            user-select: none;
            font-size: 0.9em;
            display: flex;
            align-items: center;
            white-space: nowrap;
        }

        .selector-option input[type="radio"],
        .selector-option input[type="checkbox"] {
            margin-right: 5px;
            accent-color: var(--accent-color);
        }

        .selector-option.selected {
            background: var(--accent-color);
            font-weight: bold;
        }
        
        .selector-option:hover {
            background: #5a5a5a;
        }

        /* Grid Layout - Now single column for detail view */
        .grid-container {
            display: flex;
            justify-content: center;
        }
        
        /* Single Team Card */
        .team-card {
            background-color: var(--card-bg);
            border-radius: 10px;
            overflow: hidden;
            box-shadow: 0 4px 15px rgba(0,0,0,0.3);
            width: 100%;
            max-width: 800px; /* Limit width for detailed view */
        }
        
        .team-header {
            background-color: var(--accent-color);
            padding: 15px;
            text-align: center;
            font-size: 2em;
            font-weight: bold;
        }

        .card-body { padding: 20px; display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
        
        @media (max-width: 768px) {
            .card-body { grid-template-columns: 1fr; }
        }

        .week-section {
            padding: 15px;
            border: 1px solid #444;
            border-radius: 6px;
            margin-bottom: 20px;
            background-color: #333;
        }
        .week-title { 
            font-size: 1.5em; 
            font-weight: bold; 
            margin-bottom: 10px;
            color: var(--gold);
            border-bottom: 2px solid var(--gold);
            padding-bottom: 5px;
        }

        .type-section { margin-bottom: 15px; }
        .type-title { 
            font-size: 1.1em; 
            text-transform: uppercase; 
            color: #aaa; 
            border-bottom: 1px solid #666; 
            margin-bottom: 8px;
            padding-top: 10px;
        }

        .player-row {
            display: flex;
            justify-content: space-between;
            padding: 6px 0;
            font-size: 1em;
        }
        
        .player-row:nth-child(even) { background-color: #333333; }

        /* Medals */
        .medal { margin-right: 8px; font-weight: bold; }
        .rank-1 .medal { color: var(--gold); }
        .rank-2 .medal { color: var(--silver); }
        .rank-3 .medal { color: var(--bronze); }
        
        .score { font-weight: bold; color: var(--accent-color); }
        .empty-msg { font-style: italic; color: #999; font-size: 0.9em; padding: 10px 0; }
        .no-selection-msg { 
            text-align: center; 
            padding: 80px; 
            font-size: 1.4em; 
            color: #aaa; 
            background: #252525; 
            border-radius: 10px;
        }

    </style>
</head>
<body>

    <header>
        <div class="header-top">
            <div class="last-updated" id="last-update">Updating...</div>
            <h1 id="main-title">🔎 Team Performance Details</h1>
            <div><!-- Spacer --></div>
        </div>
        <div id="error-message"></div>
    </header>
    
    <div id="selection-controls">
        <!-- Team Selector (Single Select - Radio Buttons) -->
        <div class="selector-container">
            <h3 style="margin-top:0; font-size:1.2em; border-bottom: 1px solid #4a4a4a; padding-bottom: 5px;">Select One Team:</h3>
            <div class="selector-options" id="team-selector">
                <!-- Team radio buttons inserted here -->
            </div>
        </div>

        <!-- Week Selector (Multi-Select - Checkboxes) -->
        <div class="selector-container">
            <h3 style="margin-top:0; font-size:1.2em; border-bottom: 1px solid #4a4a4a; padding-bottom: 5px;">Select Weeks to View:</h3>
            <div class="selector-options" id="week-selector">
                <!-- Week checkboxes inserted here -->
            </div>
        </div>
    </div>

    <div class="grid-container" id="leaderboard-grid">
        <div class="no-selection-msg">Please select a team and one or more weeks above.</div>
    </div>

    <script>
        // Set up variables for global access in the script
        let allData = {};
        let weekHeaders = []; // [{ name: "Week 1", index: 2 }, ...]

        // State Variables
        let selectedTeam = ""; // Holds the name of the single selected team
        let selectedWeeks = []; // Holds the indices of the selected weeks (e.g., [0, 1, 3])

        // ==========================================
        // ⚙️ CONFIGURATION
        // ==========================================
        
        // 🚨 CRITICAL STEP: REPLACE THIS PLACEHOLDER 🚨
        // This MUST be the published CSV link from your Google Sheet.
        // E.g., https://docs.google.com/spreadsheets/d/e/LONG_ID_STRING/pub?output=csv
        const CSV_URL = "https://docs.google.com/spreadsheets/d/e/2PACX-1vTrdPsAo1CJyqCRdqVlzl9_I-dVaTiViT-HhxG03JCwxGY2KaGIWpMwcQIlw_S-6jIBybp7c07_XHFW/pub?gid=735304327&single=true&output=csv"; 

        // Interval for checking the sheet for updates (in milliseconds).
        const REFRESH_INTERVAL = 30000; // 30 seconds

        // The list of teams present in your sheet's Column A
        const KNOWN_TEAMS = ["11U", "12U", "13U", "14U White", "14U Blue", "14U Softball", "15U White", "15U Blue", "16U", "17U"];

        // ==========================================
        
        // --- LOCAL STORAGE FUNCTIONS ---
        function loadSelections() {
            const savedTeam = localStorage.getItem('detailedViewSelectedTeam');
            const savedWeeks = localStorage.getItem('detailedViewSelectedWeeks');

            if (savedTeam && KNOWN_TEAMS.includes(savedTeam)) {
                selectedTeam = savedTeam;
            } else {
                selectedTeam = KNOWN_TEAMS[0] || ""; // Default to first team
            }

            if (savedWeeks) {
                try {
                    // Load saved indices, ensuring they are valid numbers
                    const parsedWeeks = JSON.parse(savedWeeks);
                    if (Array.isArray(parsedWeeks)) {
                        selectedWeeks = parsedWeeks.filter(idx => typeof idx === 'number');
                    }
                } catch (e) {
                    console.error("Could not parse saved weeks:", e);
                    selectedWeeks = [];
                }
            } else {
                selectedWeeks = [];
            }
        }

        function saveSelections() {
            localStorage.setItem('detailedViewSelectedTeam', selectedTeam);
            localStorage.setItem('detailedViewSelectedWeeks', JSON.stringify(selectedWeeks));
        }
        
        // --- UI FUNCTIONS ---
        function displayError(msg) {
            document.getElementById('error-message').innerText = msg;
        }

        function updateLastRefresh() {
            const now = new Date();
            document.getElementById('last-update').innerText = "Last updated: " + now.toLocaleTimeString();
        }

        // --- TEAM SELECTION (RADIO BUTTONS) ---
        function handleTeamSelection(teamName) {
            selectedTeam = teamName;
            saveSelections();
            renderTeamSelector(); // Update radio button state
            renderGrid();
        }

        function renderTeamSelector() {
            const container = document.getElementById('team-selector');
            container.innerHTML = '';
            
            // If no team is selected, default to the first one available
            if (!selectedTeam && KNOWN_TEAMS.length > 0) {
                selectedTeam = KNOWN_TEAMS[0];
            }

            KNOWN_TEAMS.forEach(teamName => {
                const isSelected = selectedTeam === teamName;
                const label = document.createElement('label');
                label.className = `selector-option ${isSelected ? 'selected' : ''}`;
                
                const radio = document.createElement('input');
                radio.type = 'radio';
                radio.name = 'teamSelect'; // Ensures only one is selectable
                radio.checked = isSelected;
                radio.value = teamName;
                radio.onchange = () => handleTeamSelection(teamName);

                label.appendChild(radio);
                label.appendChild(document.createTextNode(teamName));
                
                label.onclick = () => {
                    // Handle label click to select the radio button
                    if (!isSelected) {
                        handleTeamSelection(teamName);
                    }
                };

                container.appendChild(label);
            });
            renderGrid();
        }
        
        // --- WEEK SELECTION (CHECKBOXES) ---
        function handleWeekSelection(weekIndex) {
            const indexInArray = selectedWeeks.indexOf(weekIndex);
            if (indexInArray > -1) {
                selectedWeeks.splice(indexInArray, 1); // Deselect
            } else {
                selectedWeeks.push(weekIndex); // Select
            }
            saveSelections();
            renderWeekCheckboxes(); // Update checkbox state
            renderGrid();
        }

        function renderWeekCheckboxes() {
            const container = document.getElementById('week-selector');
            container.innerHTML = '';
            
            if(weekHeaders.length === 0) {
                container.innerHTML = '<div class="empty-msg">No week data available.</div>';
                return;
            }

            weekHeaders.forEach((w, idx) => {
                const isSelected = selectedWeeks.includes(idx);
                const label = document.createElement('label');
                label.className = `selector-option ${isSelected ? 'selected' : ''}`;
                
                const checkbox = document.createElement('input');
                checkbox.type = 'checkbox';
                checkbox.checked = isSelected;
                checkbox.value = idx;
                checkbox.onchange = () => handleWeekSelection(idx);

                label.appendChild(checkbox);
                label.appendChild(document.createTextNode(w.name));
                
                label.onclick = () => {
                    // Toggle selection manually
                    handleWeekSelection(idx);
                };

                container.appendChild(label);
            });
            // Ensure the grid is rendered to reflect the new selection
            renderGrid(); 
        }

        // --- GRID RENDERING (SINGLE CARD, MULTIPLE WEEKS) ---
        function renderGrid() {
            const container = document.getElementById('leaderboard-grid');
            container.innerHTML = '';
            
            if (!selectedTeam || selectedWeeks.length === 0) {
                 container.innerHTML = '<div class="no-selection-msg">Please select a team and one or more weeks above to see the detailed performance report.</div>';
                 return;
            }
            
            const teamData = allData[selectedTeam];
            if (!teamData) {
                container.innerHTML = `<div class="no-selection-msg">Data for team "${selectedTeam}" is currently unavailable.</div>`;
                return;
            }

            const card = document.createElement('div');
            card.className = 'team-card';
            
            let html = `<div class="team-header">${selectedTeam} Performance Report</div><div class="card-body">`;
            
            // Render a section for each selected week
            selectedWeeks.sort((a, b) => a - b).forEach(weekIndex => {
                const weekName = weekHeaders[weekIndex].name;
                
                html += `<div class="week-section">
                            <div class="week-title">${weekName}</div>
                            ${buildTypeSection("Offense", teamData.Offense[weekIndex])}
                            ${buildTypeSection("Defense", teamData.Defense[weekIndex])}
                        </div>`;
            });
            
            html += `</div>`;
            card.innerHTML = html;
            container.appendChild(card);
        }

        function buildTypeSection(title, players) {
            let html = `<div class="type-section"><div class="type-title">${title} Leaders</div>`;
            
            if(!players || players.length === 0) {
                html += `<div class="empty-msg">No scores recorded for this week.</div>`;
            } else {
                players.forEach((p, i) => {
                    // Parse "Name - Score"
                    let name = p; 
                    let score = "";
                    if(p.includes('-')) {
                        const parts = p.split('-');
                        score = parts.pop().trim();
                        name = parts.join('-').trim();
                    }
                    
                    let medal = "";
                    if(i === 0) medal = "🥇";
                    if(i === 1) medal = "🥈";
                    if(i === 2) medal = "🥉";

                    html += `
                    <div class="player-row rank-${i+1}">
                        <div><span class="medal">${medal}</span> ${name}</div>
                        <div class="score">${score}</div>
                    </div>`;
                });
            }
            html += `</div>`;
            return html;
        }
        
        // --- DATA FETCHING & PARSING ---

        async function fetchData() {
            if (CSV_URL === "PASTE_YOUR_PUBLISHED_CSV_URL_HERE") {
                displayError("❌ Configuration Error: Please replace the placeholder 'PASTE_YOUR_PUBLISHED_CSV_URL_HERE' for CSV_URL in the code with your actual Google Sheets published CSV link.");
                return;
            }
            
            try {
                const response = await fetch(CSV_URL);
                if (!response.ok) {
                    throw new Error(`HTTP error! status: ${response.status}`);
                }
                const text = await response.text();
                parseCSV(text);
                updateLastRefresh();
                displayError(""); // Clear error on success
            } catch (error) {
                console.error("Error fetching data:", error);
                
                // --- ADDED A MORE SPECIFIC ERROR MESSAGE FOR LOCAL FILE ISSUES ---
                if (window.location.protocol === 'file:') {
                    displayError("❌ Local File Error (CORS): Browsers often block data loading from Google Sheets when running the file locally (file://). Please serve this file using a simple local web server (e.g., Python's `http.server`) and try again.");
                } else {
                    displayError(`❌ Fetch Error: Could not load data. Ensure the CSV link is correct and publicly accessible. (Details: ${error.message})`);
                }
                // ------------------------------------------------------------------
            }
        }

        function parseCSV(csvText) {
            const rows = csvText.split(/\r?\n/).map(row => row.split(','));
            
            // Row 2 (Index 1) contains weeks
            const rawWeeks = rows[1];
            weekHeaders = [];
            
            // Find columns that look like weeks (starting from col 2 / C)
            for(let i=2; i<rawWeeks.length; i++) {
                let w = rawWeeks[i].replace(/"/g, '').trim(); // Remove quotes
                if(w.length > 2) {
                    weekHeaders.push({ name: w, index: i });
                }
            }
            
            // If no weeks were previously selected, default to selecting all of them now that we have the list.
            if (selectedWeeks.length === 0 && weekHeaders.length > 0) {
                 selectedWeeks = weekHeaders.map((_, idx) => idx);
                 saveSelections();
            }

            // Parse content
            const teams = {};
            let currentTeam = null;
            let currentType = null;

            for(let r=2; r<rows.length; r++) {
                let cellA = rows[r][0].replace(/"/g, '').trim();
                if(!cellA) continue;

                if(KNOWN_TEAMS.includes(cellA)) {
                    currentTeam = cellA;
                    // Initialize team structure for all detected weeks
                    teams[currentTeam] = { Offense: new Array(weekHeaders.length).fill().map(() => []), Defense: new Array(weekHeaders.length).fill().map(() => []) };
                    currentType = null;
                    continue;
                }

                if(cellA === "Offense" || cellA === "Defense") {
                    currentType = cellA;
                    continue;
                }

                if(cellA.startsWith("Player") && currentTeam && currentType) {
                    // For each identified week column
                    weekHeaders.forEach((weekObj, wIdx) => {
                        let val = rows[r][weekObj.index];
                        if(val) val = val.replace(/"/g, '').trim();
                        
                        if(val) {
                             teams[currentTeam][currentType][wIdx].push(val);
                        }
                    });
                }
            }
            allData = teams;
            
            // Now that data and week headers are loaded, render controls and grid
            renderTeamSelector(); 
            renderWeekCheckboxes();
            renderGrid();
        }

        // Init
        loadSelections(); // Load selections from last session
        fetchData();
        setInterval(fetchData, REFRESH_INTERVAL);

    </script>
</body>
</html>
