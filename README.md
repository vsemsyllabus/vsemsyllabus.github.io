
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no" />
<title>Syllabrix — Focus. Plan. Achieve.</title>
<meta name="theme-color" content="#121212" />
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<style>
  :root{
    /* LIGHT */
    --bg-light:#f0f2f5; --card-light:#ffffff; --text-light:#050505; --muted-light:#65676b; --border-light:#ced0d4;
    --brand-light:#0a84ff; --brand-bg-light:#e7f3ff; --accent:#fa383e; --danger:#e02c34; --success:#31a24c;
    
    /* DARK (User Specified) */
    --bg-dark:#121212; --card-dark:#1b1b1b; --text-dark:#eceef1; --muted-dark:#a1a6ad; --border-dark:#2a2a2a;
    --brand-dark:#0a84ff; --brand-bg-dark:#1b2b44;

    /* GRADIENTS */
    --grad-title: linear-gradient(45deg, #0a84ff, #64d2ff);
    --grad-brand: linear-gradient(45deg, #0a84ff, #4599ff);
    --grad-today: linear-gradient(45deg, #0a84ff, #af52de, #ff3b30);

    /* SUBJECT COLORS */
    --sub-c1:#0a84ff; --sub-c2:#34c759; --sub-c3:#ff9500; --sub-c4:#ff3b30; --sub-c5:#af52de;

    /* ACTIVE */
    --bg:var(--bg-light); --card:var(--card-light); --text:var(--text-light); --muted:var(--muted-light); --border:var(--border-light);
    --brand:var(--brand-light); --brand-bg:var(--brand-bg-light);

    --radius:12px; --radius-lg:20px; --shadow-sm:0 1px 2px rgba(0,0,0,.08); --shadow-md:0 8px 24px rgba(0,0,0,.1);
  }
  body[data-theme="dark"]{ --bg:var(--bg-dark); --card:var(--card-dark); --text:var(--text-dark); --muted:var(--muted-dark); --border:var(--border-dark); --brand:var(--brand-dark); --brand-bg:var(--brand-bg-dark); --shadow-sm:0 2px 4px rgba(0,0,0,.3); --shadow-md:0 10px 32px rgba(0,0,0,.45); }

  *{box-sizing:border-box; -webkit-tap-highlight-color:transparent}
  html{scroll-behavior:smooth; scroll-padding-top:120px}
  body{margin:0; min-height:100vh; font:16px -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji"; background:var(--bg); color:var(--text); transition: background .2s, color .2s; -webkit-font-smoothing: antialiased; -moz-osx-font-smoothing: grayscale;}

  .wrap{max-width:960px; margin:0 auto; padding:0 16px}
  header{padding:16px 0 8px}
  .header-top {display: flex; justify-content: space-between; align-items: center; margin-bottom: 4px;}
  .header-placeholder { width: 40px; }
  h1{margin:0; font-size:32px; font-weight:800; text-align:center; letter-spacing:-.5px;
     background:var(--grad-title); -webkit-background-clip:text; -webkit-text-fill-color:transparent}
  .tagline{margin:0 0 16px; text-align:center; color:var(--muted); font-weight:500; font-size: 15px;}

  /* Main app shell */
  .app-card{background:var(--card); border:1px solid var(--border); border-radius:var(--radius-lg); box-shadow:var(--shadow-sm); padding:16px; transition: background .2s, border-color .2s;}
  .view-title { text-align: center; margin: 6px 0 16px; font-weight: 700; font-size: 1.2em; }
  
  .seg-tabs{display:flex; gap:6px; background:var(--bg); padding:4px; border-radius:999px; border:1px solid var(--border); width:max-content;}
  .seg-btn{padding:8px 16px; border-radius:999px; border:0; background:transparent; color:var(--muted); font-weight:600; font-size:14px; cursor: pointer; transition: all .25s ease;}
  .seg-btn.active{background-image:var(--grad-brand); color:#fff; box-shadow: 0 2px 8px color-mix(in srgb, var(--brand) 30%, transparent)}

  /* Theme Toggle */
  .theme-toggle { font-size: 22px; background: var(--bg); border: 1px solid var(--border); width: 40px; height: 40px; border-radius: var(--radius); cursor: pointer; display: flex; align-items: center; justify-content: center; transition: all .2s; padding: 0;}
  .theme-toggle:hover { border-color: var(--brand); }
  .theme-icon { transition: transform 0.3s ease-out; }
  body[data-theme="dark"] #theme-icon-sun { display: block; }
  body[data-theme="dark"] #theme-icon-moon { display: none; }
  body[data-theme="light"] #theme-icon-sun { display: none; }
  body[data-theme="light"] #theme-icon-moon { display: block; }
  
  /* Floating Search */
  #search-container{position:sticky; top:0; z-index:20; padding:12px 0; background:linear-gradient(to bottom, var(--bg) 85%, transparent)}
  #search{display:block; width:100%; max-width:520px; margin:0 auto; height:48px; padding:0 20px; border-radius:999px; border:1px solid var(--border); background:var(--card); color:var(--text); font-size:16px; box-shadow:var(--shadow-md)}
  #search:focus{outline:none; border-color:var(--brand); box-shadow:0 0 0 3px color-mix(in srgb, var(--brand) 20%, transparent)}
  #search-results{position:absolute; left:50%; transform:translateX(-50%); top:calc(48px + 18px); width:100%; max-width:520px; background:var(--card); border:1px solid var(--border); border-radius:14px; box-shadow:var(--shadow-md); max-height:50vh; overflow:auto}
  .sr-item{padding:12px 16px; border-bottom:1px solid var(--border); cursor:pointer}
  .sr-item:last-child{border-bottom:0} .sr-item:hover { background: var(--bg); }
  .sr-title{font-weight:600} .sr-ctx{font-size:12px; color:var(--muted)}
  .hidden{display:none!important}

  /* Subject tabs */
  #subject-tabs{display:flex; gap:8px; overflow:auto; padding:8px 2px 12px; -ms-overflow-style:none; scrollbar-width:none}
  #subject-tabs::-webkit-scrollbar{display:none}
  .subject-chip{white-space:nowrap; border:1px solid var(--border); background:var(--bg); color:var(--muted); padding:8px 14px; border-radius:999px; font-weight:600; font-size:14px; cursor: pointer; transition: all .2s;}
  .subject-chip.active{background-image:var(--grad-brand); color:#fff; border-color:transparent; transform: translateY(-2px); box-shadow:var(--shadow-sm)}

  .unit{background:var(--bg); border:1px solid var(--border); border-radius:14px; padding:14px; margin:16px 0}
  .unit-top{display:flex; justify-content:space-between; gap:8px; align-items:center; flex-wrap:wrap}
  .topic{border:1px solid var(--border); border-radius:12px; margin-top:12px; overflow:hidden}
  .topic summary{list-style:none; cursor:pointer; padding:12px 14px; font-weight:600; background:var(--bg)}
  .topic summary::-webkit-details-marker{display:none}
  .checks{list-style:none; padding:12px; margin:0; display:grid; gap:8px}
  .item{display:flex; gap:10px; align-items:flex-start; padding:12px; border:1px solid var(--border); border-radius:var(--radius); background:var(--card)}
  .item label{flex:1; line-height:1.5}
  .icons{display:flex; gap:6px; align-items: center;}
  .i-btn{border:0; background:var(--bg); border: 1px solid var(--border); color:var(--muted); border-radius:8px; cursor: pointer; padding: 0; width: 32px; height: 32px; display: flex; align-items: center; justify-content: center; transition: all .2s;}
  .i-btn svg { width: 18px; height: 18px; stroke-width: 2.5px; }
  .i-btn:hover{color:var(--text); border-color:var(--text); transform: scale(1.1);}
  .i-btn.active{color:#fff; border-color:transparent; background-image:var(--grad-brand);}
  .i-btn[data-act="important"].active{color:#fff; background:var(--accent);}
  
  /* Planner */
  .planner{display:grid; gap:16px}
  .p-card{background:var(--bg); border:1px solid var(--border); border-radius:14px; padding:14px}
  .p-card-label {font-weight:700; display:block; margin-bottom:10px}
  .row{display:flex; gap:8px} .row.stack{flex-wrap:wrap}
  .btn{border:1px solid var(--border); background:var(--card); color:var(--text); padding:10px 14px; border-radius:var(--radius); font-weight:600; cursor:pointer; transition: all .2s;}
  .btn:hover { border-color: var(--brand); transform: translateY(-2px); box-shadow: var(--shadow-sm); }
  .btn.brand{background-image:var(--grad-brand); color:#fff; border-color:transparent}
  .btn.danger{background:color-mix(in srgb, var(--danger) 15%, transparent); border-color:color-mix(in srgb, var(--danger) 40%, transparent); color:var(--danger)}
  .btn.small{padding:6px 10px; font-size:13px; border-radius: 8px;}
  input, select, textarea {width:100%; background:var(--card); border:1px solid var(--border); border-radius:var(--radius); padding:10px 14px; color:var(--text); font-family: inherit; font-size: inherit; transition: all .2s;}
  input:focus, select:focus, textarea:focus { outline: none; border-color: var(--brand); box-shadow:0 0 0 3px color-mix(in srgb, var(--brand) 20%, transparent); }
  input[type="date"] { color-scheme: dark; } body[data-theme="light"] input[type="date"] { color-scheme: light; }
  input[type="checkbox"] { width: auto; }

  .chip{display:inline-flex; align-items:center; gap:6px; border:1px solid var(--border); background:var(--card); padding:6px 12px; border-radius:999px; font-weight:600; font-size:13px}

  .plans-by-date{display:grid; gap:12px}
  .day-card{background:var(--card); border:1px solid var(--border); border-radius:14px; padding:12px}
  .day-head{display:flex; justify-content:space-between; align-items:center}
  .day-list{margin:10px 0 0; padding-left:20px; display: grid; gap: 8px;} .day-list li{margin:0} .x{opacity:.65}

  /* Modal */
  .overlay{position:fixed; inset:0; background:rgba(0,0,0,.6); backdrop-filter: blur(4px); display:flex; align-items:center; justify-content:center; padding:14px; z-index:60}
  .modal{width:100%; max-width:640px; background:var(--card); border:1px solid:var(--border); border-radius:18px; box-shadow:var(--shadow-md); display:flex; flex-direction:column; max-height:90vh}
  .m-head,.m-foot{padding:12px 16px; border-bottom:1px solid var(--border); display:flex; justify-content:space-between; align-items:center; flex-shrink: 0;}
  .m-foot{border-bottom:0; border-top:1px solid var(--border)} .m-body{padding:16px; overflow:auto} .hint{font-size:12px; color:var(--muted)}

  /* Dashboard */
  #dash-content-title { text-align: center; margin: 0 0 12px; font-size: 1.1em; }
  .progress{height:8px; background:var(--bg); border:1px solid var(--border); border-radius:999px; overflow:hidden; cursor: pointer;}
  .progress i{display:block; height:100%; width:0; background-image:var(--grad-brand); transition: width 0.3s ease-out;}
  .muted{color:var(--muted)}
  .progress-circle-container{position:relative; width:120px; height:120px; margin: 16px auto 0;}
  .progress-circle-bg, .progress-circle-fg{fill:none; stroke-width:10;}
  .progress-circle-bg{stroke:var(--border);}
  .progress-circle-fg{stroke:url(#progress-gradient); stroke-linecap:round; transform: rotate(-90deg); transform-origin:50% 50%; transition: stroke-dashoffset 0.5s ease;}
  .progress-circle-text{font-size:24px; font-weight:700; fill:var(--brand); text-anchor:middle;}
  .progress-subtitle { display: flex; align-items: center; justify-content: center; gap: 8px; margin-top: 8px; flex-wrap: wrap; }
  .info-list { display: grid; gap: 8px; }
  .info-list-item{display:flex; gap:10px; align-items:flex-start; padding:12px; border:1px solid var(--border); border-radius:var(--radius); background:var(--card); }
  .info-list-item input[type="checkbox"] { margin-top: 2px; }
  .info-list-item-content{flex:1; line-height:1.5}
  
  .countdown-item { text-align: center; }
  .countdown-timer { font-size: 24px; font-weight: 700; background: var(--grad-brand); -webkit-background-clip: text; -webkit-text-fill-color: transparent; letter-spacing: -0.5px;}
  .countdown-label { font-size: 14px; font-weight: 600; color: var(--muted); }

  /* Date Scroller */
  #date-scroller-container { overflow-x: auto; padding: 4px 0 10px; margin: 0 -16px; padding-left: 16px; padding-right: 16px; scrollbar-width: none; -ms-overflow-style: none; scroll-snap-type: x mandatory; -webkit-overflow-scrolling: touch; }
  #date-scroller-container::-webkit-scrollbar { display: none; }
  #date-scroller { display: flex; gap: 8px; }
  .date-item { flex: 0 0 68px; text-align: center; padding: 10px 0; border-radius: 18px; cursor: pointer; scroll-snap-align: center; border: 1px solid var(--border); background: var(--bg); transition: all .2s ease-out; }
  .date-item .day-name { font-size: 12px; font-weight: 600; color: var(--muted); }
  .date-item .day-num { font-size: 18px; font-weight: 700; margin-top: 4px; }
  .date-item .month-indicator { font-size: 10px; color: var(--accent); font-weight: 700; text-transform: uppercase; margin-top: 2px; }
  .date-item:hover { transform: translateY(-2px); box-shadow: var(--shadow-sm); }
  .date-item.active { background-image: var(--grad-brand); color: #fff; border-color: transparent; }
  .date-item.active .day-name { color: rgba(255,255,255,0.8); }
  .date-item.today { background-image: var(--grad-today); color: #fff; border-color: transparent; }
  .date-item.today .day-name { color: rgba(255,255,255,0.8); }
  .date-item.has-plan .day-num::after { content: '•'; color: var(--brand); font-size: 20px; position: absolute; transform: translate(2px, 8px); }
  .date-item.active.has-plan .day-num::after, .date-item.today.has-plan .day-num::after { color: #fff; }
  
  /* Planner Tabs */
  #plan-tabs-container { display: flex; flex-wrap: wrap; gap: 6px; }
  .plan-tab { white-space: nowrap; padding: 8px 12px; border-radius: 99px; background: var(--bg); border: 1px solid var(--border); font-weight: 600; cursor: pointer; font-size: 14px; }
  .plan-tab.active { background-image:var(--grad-brand); color: #fff; border-color: transparent; }
  .plan-tab-btn { padding: 8px; background: transparent; border: 0; cursor: pointer; font-weight: 600; color: var(--brand); }

  /* --- Notes Modal & Editor --- */
  .notes-modal-layout { display: flex; gap: 14px; }
  #notes-list-panel { width: 180px; flex-shrink: 0; display: flex; flex-direction: column; gap: 6px; }
  #notes-content-panel { flex-grow: 1; min-width: 0; }
  .note-list-item { padding: 8px 10px; border-radius: 8px; background: var(--bg); cursor: pointer; font-weight: 600; border: 1px solid var(--border); overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
  .note-list-item.active { background: var(--brand-bg); color: var(--brand); border-color: var(--brand); }

  .note-save-status {
    font-size: 13px;
    font-weight: 600;
    color: var(--muted);
    opacity: 0;
    transition: opacity 0.3s ease;
  }

  .editor-toolbar { display: flex; flex-wrap: wrap; gap: 6px; margin-bottom: 8px; padding-bottom: 8px; border-bottom: 1px solid var(--border); }
  .editor-toolbar .i-btn { width: 36px; height: 36px; }
  #noteContent:focus { outline: none; }
  #noteContent img { max-width: 100%; border-radius: 8px; cursor: pointer; }
  #noteContent .resizable-image-wrapper { position: relative; display: inline-block; resize: both; overflow: hidden; border: 2px dashed var(--brand); min-width: 50px; min-height: 50px; }
  #noteContent .resizable-image-wrapper img { width: 100%; height: 100%; display: block; pointer-events: none; }

  /* Drawing Modal */
  #drawing-toolbar { display: flex; align-items: center; gap: 12px; flex-wrap: wrap; margin-bottom: 8px; }
  #drawing-toolbar input[type="color"] { padding: 0; height: 24px; width: 24px; border: none; background: none; border-radius: 4px; }
  
  /* --- TTS Player --- */
  .audio-player-controls { display: flex; align-items: center; gap: 10px; background: var(--bg); padding: 8px; border-radius: var(--radius); border: 1px solid var(--border); }
  .audio-player-controls #ttsVoice { flex-grow: 1; }
  .audio-player-controls #ttsRate { max-width: 120px; }
  #noteContent .tts-highlight { background-color: color-mix(in srgb, var(--brand) 25%, transparent); }

  /* Toast Notification */
  .toast { position: fixed; bottom: -100px; left: 50%; transform: translateX(-50%); background: #222; color: white; padding: 12px 20px; border-radius: 99px; font-weight: 600; z-index: 100; box-shadow: 0 4px 12px rgba(0,0,0,.2); opacity: 0; transition: all .4s cubic-bezier(0.25, 1, 0.5, 1); }
  .toast.show { opacity: 1; bottom: 30px; }

  /* Mobile-first tweaks */
  @media (min-width:680px){ .row > *{flex:1} }
</style>
</head>
<body data-theme="dark">
  <div id="toast" class="toast hidden"></div>
  
  <header class="wrap">
    <div class="header-top">
      <div class="header-placeholder"></div>
      <h1>Syllabrix</h1>
      <button id="themeToggle" class="theme-toggle" aria-label="Toggle theme">
        <svg id="theme-icon-sun" class="theme-icon" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="5"></circle><line x1="12" y1="1" x2="12" y2="3"></line><line x1="12" y1="21" x2="12" y2="23"></line><line x1="4.22" y1="4.22" x2="5.64" y2="5.64"></line><line x1="18.36" y1="18.36" x2="19.78" y2="19.78"></line><line x1="1" y1="12" x2="3" y2="12"></line><line x1="21" y1="12" x2="23" y2="12"></line><line x1="4.22" y1="19.78" x2="5.64" y2="18.36"></line><line x1="18.36" y1="5.64" x2="19.78" y2="4.22"></line></svg>
        <svg id="theme-icon-moon" class="theme-icon" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"></path></svg>
      </button>
    </div>
    <div class="tagline">Focus. Plan. Achieve.</div>
    <div class="seg-tabs" id="mainTabs">
      <button class="seg-btn active" data-tab="dashboard">Dashboard</button>
      <button class="seg-btn" data-tab="syllabus">Syllabus</button>
      <button class="seg-btn" data-tab="tools">Study Tools</button>
    </div>
  </header>

  <!-- Floating Search -->
  <div id="search-container" class="wrap">
    <input id="search" type="search" placeholder="Search entire syllabus…"/>
    <div id="search-results" class="hidden"></div>
  </div>

  <main class="wrap" style="padding-bottom:24px">
    <!-- DASHBOARD -->
    <section id="view-dashboard">
        <div id="countdown-container" class="app-card"></div>
        
        <div class="app-card" style="margin-top:16px;">
            <div class="seg-tabs" id="dashboard-tabs" style="margin: 0 auto 16px;">
                <button class="seg-btn active" data-d-tab="today">Today</button>
                <button class="seg-btn" data-d-tab="pinned">Pinned</button>
                <button class="seg-btn" data-d-tab="nextup">Next Up</button>
                <button class="seg-btn" data-d-tab="progress">Progress</button>
            </div>
            
            <div id="dashboard-tab-content">
                <div id="dash-today">
                    <div id="date-scroller-container">
                        <div id="date-scroller"></div>
                    </div>
                    <h3 class="dash-content-title" id="daily-plan-title">Today's Plan</h3>
                    <div id="daily-plan-list"></div>
                </div>
                <div id="dash-pinned" class="hidden">
                    <h3 class="dash-content-title">Pinned Topics</h3>
                    <div id="pinned-topics-list"></div>
                </div>
                <div id="dash-nextup" class="hidden">
                    <h3 class="dash-content-title">Next Up</h3>
                    <div id="next-up-list"></div>
                </div>
                <div id="dash-progress" class="hidden">
                    <div style="text-align: center;">
                        <div class="muted" style="font-weight:700">Overall Progress</div>
                        <div id="overall-progress-circle" class="progress-circle-container"></div>
                        <div id="overall-progress-subtitle" class="progress-subtitle"></div>
                    </div>
                    <div style="margin-top: 24px;">
                        <div class="muted" style="margin-bottom: 12px; text-align:center; font-weight:700;">Progress by Subject</div>
                        <div id="subject-progress-list" style="display:grid; gap:12px;"></div>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- SYLLABUS -->
    <section id="view-syllabus" class="app-card hidden">
      <div id="subject-tabs"></div>
      <div id="subjects"></div>
    </section>

    <!-- STUDY TOOLS -->
    <section id="view-tools" class="app-card hidden">
      <div class="seg-tabs" id="toolsTabs" style="margin-bottom:16px">
        <button class="seg-btn active" data-t="planner">Planner</button>
        <button class="seg-btn" data-t="notes">Notes</button>
        <button class="seg-btn" data-t="important">Important</button>
      </div>

      <!-- Notes View -->
      <div id="tools-notes" class="hidden">
        <h3 class="view-title">My Notes</h3>
        <div id="notesList" class="info-list"></div>
      </div>

      <!-- Important View -->
      <div id="tools-important" class="hidden">
        <h3 class="view-title">Important Topics</h3>
        <div id="impList" class="info-list"></div>
      </div>

      <!-- Planner View -->
      <div id="tools-planner">
        <h3 class="view-title">My Study Planner</h3>
        <div class="planner">
            <div class="p-card">
                <label class="p-card-label">Plan Settings</label>
                <div class="row stack">
                    <input type="text" id="planName" placeholder="e.g., Sessional Exam Plan" />
                    <input type="date" id="targetDate" />
                </div>
                <div class="row" style="margin-top: 8px; align-items: center; justify-content: space-between;">
                    <label class="chip">
                        <input id="planCountdown" type="checkbox"> Enable Countdown on Dashboard
                    </label>
                    <button id="savePlanSettings" class="btn brand">Save Settings</button>
                </div>
            </div>

            <div class="p-card">
                <label class="p-card-label">Smart Plan Generator</label>
                <p class="hint" style="margin-bottom: 10px; font-size: 13px;">This will clear your current plan and generate a new one.</p>
                <div class="row stack">
                    <button class="btn brand small" data-auto="7">7 Days</button>
                    <button class="btn brand small" data-auto="30">30 Days</button>
                    <button class="btn brand small" data-auto="90">90 Days</button>
                    <input type="number" id="customDays" min="1" placeholder="Custom days" />
                    <button id="genCustom" class="btn small">Generate</button>
                </div>
                <label class="chip" style="margin-top: 12px;">
                    <input id="preferImportant" type="checkbox"> Prioritize important topics
                </label>
            </div>

            <div class="p-card">
                <label class="p-card-label">Add Topic to Plan</label>
                <div class="row stack" style="align-items: flex-end;">
                    <div style="flex:2">
                        <label for="topicSearch" class="hint" style="padding-left:4px; margin-bottom: 4px; display:inline-block">Search for a topic</label>
                        <input type="search" id="topicSearch" placeholder="Type to search..." />
                    </div>
                    <div style="flex:1">
                        <label for="addDate" class="hint" style="padding-left:4px; margin-bottom: 4px; display:inline-block">Select a date</label>
                        <input type="date" id="addDate">
                    </div>
                </div>
                <select id="topicSelect" size="5" class="hidden" style="margin-top: 8px; width: 100%; border-radius: var(--radius);"></select>
                <button id="addToPlan" class="btn brand" style="margin-top: 8px; width: 100%;">Add Selected Topic</button>
                <div class="hint" style="margin-top: 6px;">Tip: You can also use the ➕ button on any topic in the Syllabus tab.</div>
            </div>

            <div class="p-card">
                <label class="p-card-label">Your Plan</label>
                <div id="plansByDate" class="plans-by-date"></div>
            </div>
        </div>
      </div>

    </section>
  </main>

  <!-- Add-to-Plan Modal (from Syllabus) -->
  <div id="addModal" class="overlay hidden">
    <div class="modal">
      <div class="m-head"><strong>Add to Plan</strong><button id="addClose" class="btn small">✕</button></div>
      <div class="m-body">
        <label for="modalDate" style="font-weight: 600; display: block; margin-bottom: 8px;">Select Date</label>
        <input type="date" id="modalDate">
        <div id="modalTopicSummary" class="hint" style="margin-top:8px"></div>
      </div>
      <div class="m-foot">
        <button id="modalAddBtn" class="btn brand">Add</button>
      </div>
    </div>
  </div>

  <!-- NOTES MODAL -->
  <div id="notesModal" class="overlay hidden">
    <div class="modal" style="max-width: 800px;">
      <div class="m-head"><strong id="nmTitle">Topic Notes</strong><button id="nmClose" class="btn small">✕</button></div>
      <div class="m-body">
          <div class="notes-modal-layout">
              <div id="notes-list-panel"></div>
              <div id="notes-content-panel">
                  <label>Note Title</label>
                  <input id="noteTitle" type="text" placeholder="e.g., IMNCI — Key concepts">
                  <label style="margin-top:8px">Subtitle</label>
                  <input id="noteSubtitle" type="text" placeholder="e.g., Assessment and classification">
                  <label style="margin-top:8px">Content</label>
                  <div id="editorToolbar" class="editor-toolbar"></div>
                  <div id="noteContent" contenteditable="true" style="min-height:200px; border:1px solid var(--border); border-radius:12px; padding:10px; overflow-y: auto;"></div>
                  <label style="margin-top:8px">Key points</label>
                  <textarea id="noteKeys" style="width:100%; min-height:80px;" placeholder="• Point 1&#10;• Point 2"></textarea>
              </div>
          </div>
      </div>
      <div class="m-foot" style="flex-direction: column; align-items: stretch; gap: 12px;">
          <div class="audio-player-controls">
              <button id="ttsPlay" class="i-btn" title="Play/Pause">
                  <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polygon points="5 3 19 12 5 21 5 3"></polygon></svg>
              </button>
              <button id="ttsStop" class="i-btn" title="Stop">
                  <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect x="3" y="3" width="18" height="18" rx="2" ry="2"></rect></svg>
              </button>
              <select id="ttsVoice" class="btn small"></select>
              <input type="range" id="ttsRate" min="0.5" max="2" step="0.1" value="1" title="Playback Speed">
              <span id="ttsRateLabel" class="chip" style="min-width: 50px; justify-content: center;">1.0x</span>
          </div>
          <div style="display: flex; justify-content: space-between; align-items:center;">
             <div>
                <button id="noteNew" class="btn small">New Note</button>
                <button id="noteDelete" class="btn danger small">Delete Note</button>
                <span id="noteSaveStatus" class="note-save-status"></span>
             </div>
             <div>
               <button id="noteExport" class="btn small">Export PDF</button>
               <button id="noteSave" class="btn brand">Save & Close</button>
             </div>
          </div>
      </div>
    </div>
  </div>

  <!-- DRAWING MODAL -->
  <div id="drawingModal" class="overlay hidden">
    <div class="modal" style="max-width: 800px;">
      <div class="m-head"><strong>Create Drawing</strong></div>
      <div class="m-body" style="background:var(--bg);">
          <div id="drawing-toolbar">
              <label class="chip">Color: <input type="color" id="drawColor" value="#0a84ff"></label>
              <label class="chip">Size: <input type="range" id="drawSize" min="1" max="50" value="5" style="width: 80px;"></label>
              <button id="drawPen" class="btn small brand">Pen</button>
              <button id="drawEraser" class="btn small">Eraser</button>
              <button id="drawClear" class="btn small danger">Clear</button>
          </div>
          <canvas id="drawingCanvas" style="border: 1px solid var(--border); border-radius: 12px; margin-top: 8px; background: var(--card); touch-action: none;"></canvas>
      </div>
      <div class="m-foot">
          <button id="drawingCancel" class="btn">Cancel</button>
          <button id="drawingSave" class="btn brand">Insert Drawing</button>
      </div>
    </div>
  </div>

  <!-- Subject Progress Detail Modal -->
  <div id="subjectModal" class="overlay hidden">
    <div class="modal">
      <div class="m-head"><strong id="smTitle">Subject Progress</strong><button id="smClose" class="btn small">✕</button></div>
      <div class="m-body" id="smBody"></div>
    </div>
  </div>

<input type="file" id="imageInput" accept="image/*" class="hidden">
<script type="module">
/* ================== DATA ================== */
const DATA = [
  {
    code: "CHN-I",
    subject: "CHILD HEALTH NURSING - I",
    units: [
      { no: "I", title: "Introduction: Modern concept of child-care", topics: [
        "Historical development of child health and child-care",
        "Philosophy and modern concept of child-care",
        "Cultural and religious considerations in child-care",
        "National policy, programs and legislation related to child health & welfare",
        "Internationally accepted rights of the child",
        "Changing trends in hospital care: preventive, promotive and curative aspects",
        "Preventive pediatrics: concept and role",
        "Immunization programs and cold chain",
        "Care of under-five and well-baby clinics",
        "Preventive measures towards accidents",
        "Difference between adult and child in terms of response to illness: physiological, psychological, social, immunological differences",
        "Hospital environment for the sick child; impact of hospitalization on child and family",
        "Role of pediatric nurse in hospital: communication techniques, grief and bereavement",
        "Principles of pre and postoperative nursing care for infants and children",
        "Child health nursing procedures: administration of meds (oral, IM, IV), calculation of fluid requirements, application of restraints, pain assessment scales (FACES, FLACC, numerical)"
      ]},
      { no: "II", title: "Growth and Development", topics: [
        "Definition and principles of growth and development",
        "Factors affecting growth & development",
        "Growth and development from birth to adolescence",
        "Growth and developmental theories: Freud, Erickson, Jean Piaget, Kohlberg",
        "Needs of normal children through developmental stages and parental guidance",
        "Nutritional needs of children and infants: breastfeeding (exclusive), supplementary/artificial feeding and weaning",
        "Baby friendly hospital concept",
        "Role of play for normal and sick children: types and selection of play materials",
        "Demonstration and observation of developmental milestones; developmental study reports"
      ]},
      { no: "III", title: "Care of normal and high-risk neonates", topics: [
        "Newborn/essential newborn care: ENBC module",
        "Neonatal resuscitation — NRP / PLS (AHA) principles and practice",
        "Nursing management of low birth weight baby: thermoregulation, feeding, kangaroo mother care",
        "Nursing management of common neonatal disorders: hyperbilirubinemia (jaundice), hypothermia, hyperthermia, metabolic disorders, neonatal infections, neonatal seizures, respiratory distress syndrome",
        "Neonatal equipment and organization of neonatal care unit",
        "Retinopathy of prematurity awareness",
        "Practical sessions, demonstration and clinical practice"
      ]},
      { no: "IV", title: "Integrated Management of Neonatal and Childhood Illness (IMNCI)", topics: [
        "Principles and strategies of IMNCI",
        "Assessment and classification of sick neonate and child",
        "Integrated approach to common childhood illnesses",
        "Health education for parents and health workers",
        "Clinical practice and modular-based teaching"
      ]},
      { no: "V", title: "Disorders of respiratory and endocrine systems", topics: [
        "Assessment and nursing management of pediatric respiratory disorders: acute nasopharyngitis, tonsillitis, croup, bronchiolitis, bronchitis, pneumonia, asthma",
        "Congenital respiratory malformations: tracheoesophageal fistula, diaphragmatic hernia — identification and nursing care",
        "Endocrine disorders: juvenile diabetes mellitus, hypothyroidism — pathophysiology, clinical features, management and nursing care"
      ]},
      { no: "VI", title: "Childhood emergencies and accidents", topics: [
        "Childhood emergencies: accidents — causes and prevention",
        "Poisoning: common poisons, first aid and referral",
        "Foreign bodies, hemorrhage and acute airway obstruction",
        "Perform child CPR and pediatric life support workshop"
      ]},
    ]
  },
  {
    code: "MHN-I",
    subject: "MENTAL HEALTH NURSING - I",
    units: [
      { no: "I", title: "Historical development & scope", topics: [
        "Evolution of mental health services and psychiatric nursing",
        "Scope and settings for mental health nursing practice",
        "Concepts of normal and abnormal behavior",
        "Values, attitudes and stigma related to mental illness",
        "Roles and functions of mental health nurse"
      ]},
      { no: "II", title: "Terminology, classification & psychopathology", topics: [
        "Classification of mental disorders: ICD-11, DSM overview",
        "Terminology used in mental health nursing",
        "Basic psychopathology: etiology, bio-psycho-social factors",
        "Defense mechanisms and personality development"
      ]},
      { no: "III", title: "Mental health assessment", topics: [
        "Purpose and process of mental health assessment",
        "History taking and Mental Status Examination (MSE)",
        "Neurological and physical assessment related to psychiatric care",
        "Psychological testing (overview)",
        "Investigations: blood chemistry, EEG, CT, MRI (basic)"
      ]},
      { no: "IV", title: "Therapeutic communication & nurse-patient relationship", topics: [
        "Principles of therapeutic communication",
        "Nurse–patient relationship: stages, boundaries, transference/counter-transference",
        "Therapeutic impasses and interventions",
        "Process recording and role play for communication skills"
      ]},
      { no: "V", title: "Treatment modalities & therapies", topics: [
        "Psychopharmacology: classes of drugs, principles, indications, side effects and nursing responsibilities",
        "Electroconvulsive therapy (ECT): indications, preparation, nursing care",
        "Psychological therapies: psychotherapy, behavior therapy, cognitive behavior therapy (CBT)",
        "Psychosocial therapies: group therapy, family therapy, occupational therapy, recreational therapy",
        "Alternative & complementary therapies: yoga, relaxation techniques"
      ]},
      { no: "VI", title: "Schizophrenia and psychotic disorders", topics: [
        "Etiology, clinical manifestations and diagnostic criteria",
        "Nursing assessment and management",
        "Rehabilitation and follow-up care",
        "Special considerations: geriatric, community follow-up"
      ]},
      { no: "VII", title: "Mood disorders", topics: [
        "Depressive disorders: clinical features, assessment and nursing care",
        "Bipolar affective disorders: mania, hypomania, mood stabilization strategies",
        "Treatment modalities: pharmacological and psychosocial",
        "Relapse prevention and family education"
      ]},
      { no: "VIII", title: "Neurotic, stress-related & somatoform disorders", topics: [
        "Anxiety disorders, OCD, phobias, PTSD",
        "Somatoform and dissociative disorders — presentation and nursing management",
        "Stress management and counseling techniques"
      ]}
    ]
  },
  {
    code: "CHN-COM",
    subject: "COMMUNITY HEALTH NURSING - I (incl. Environmental Science & Epidemiology)",
    units: [
      { no: "I", title: "Concepts of community & public health", topics: [
        "Definition: public health, community health and community health nursing",
        "Evolution of public health in India",
        "Concepts of health & disease, determinants of health",
        "Levels of prevention: primary, secondary, tertiary",
        "Health problems profile of India"
      ]},
      { no: "II", title: "Health care planning & delivery system", topics: [
        "Health care planning steps and committees",
        "Organization of health care at various levels: sub-center, PHC, CHC, district, state and national",
        "National Health Policy and National Health Mission programs (NHM, Ayushman Bharat)",
        "Primary Health Care & Comprehensive Primary Health Care (CPHC)"
      ]},
      { no: "III", title: "Environmental science & sanitation", topics: [
        "Ecosystem: concept, structure and functions",
        "Natural resources: renewable/non-renewable and conservation",
        "Pollution: air, water, soil, noise; causes, health effects and control measures",
        "Waste management: sewage, solid waste, biomedical waste management",
        "Water purification, rain water harvesting and water quality standards"
      ]},
      { no: "IV", title: "Nutrition & food safety", topics: [
        "Nutrition assessment for different age groups",
        "Types of diets, meal planning and therapeutic diets",
        "National nutrition programs; nutritional deficiency disorders",
        "Food borne diseases: causes, prevention and public health response"
      ]},
      { no: "V", title: "Communication, BCC and health education", topics: [
        "Behaviour Change Communication (BCC) principles and steps",
        "Health education methods and media: audio-visual aids, IEC materials",
        "Counseling skills and motivational interviewing",
        "Planning and evaluation of health education sessions"
      ]},
      { no: "VI", title: "Community health approaches & home visiting", topics: [
        "Community health nursing approaches: nursing process, epidemiological approach, problem solving",
        "Home visits: purpose, principles, bag technique and documentation",
        "Community participation and utilization of community resources"
      ]},
      { no: "VII", title: "School health & adolescent health services", topics: [
        "School health program components, role of school health nurse",
        "Screening and referrals, school health records",
        "Adolescent health: issues, counseling and preventive strategies"
      ]},
      { no: "VIII", title: "Epidemiology & communicable disease control", topics: [
        "Basic epidemiology: distribution, determinants and patterns of disease",
        "Modes of transmission; chain of infection",
        "Epidemiological methods: descriptive, analytical and experimental",
        "Investigation of epidemic outbreaks and control measures"
      ]},
      { no: "IX", title: "National Health Programs", topics: [
        "Overview of national programs relevant to communicable and non-communicable diseases: UIP, NLEP, RNTCP, NVBDCP, NACO, NPCDCS, TB & Leprosy programs",
        "Role of nurses in implementation and screening, referral & follow-up"
      ]},
      { no: "X", title: "Records, reports & surveillance", topics: [
        "Registers and reporting systems in community health (vital statistics)",
        "IDSP and surveillance systems; reporting of Notifiable diseases",
        "Maintenance of health center records and family records"
      ]}
    ]
  },
  {
    code: "EDTECH",
    subject: "EDUCATIONAL TECHNOLOGY / NURSING EDUCATION",
    units: [
      { no: "I", title: "Foundations of educational technology", topics: [
        "Definitions: education, educational technology; aims and scope",
        "Latest approaches: transformational education, relationship-based, competency-based education",
        "Philosophy of education and nursing education",
        "Teaching–learning process: nature, principles and barriers",
        "Learning theories: behaviorism, cognitivism, constructivism, experiential & reflective learning"
      ]},
      { no: "II", title: "Assessment, curriculum and planning", topics: [
        "Essential qualities of a teacher and teaching styles",
        "Assessment of learners: domains of learning, test construction, MCQs and advanced item types",
        "Curriculum design and development: course plan, unit and lesson planning",
        "Writing learning outcomes and behavioral objectives"
      ]},
      { no: "III", title: "Teaching strategies & active learning", topics: [
        "Teaching methods: lecture, demonstration, group discussion, seminar, symposium, panel discussion, workshops",
        "Microteaching and skill lab teaching; ICT and computer-assisted learning",
        "Active strategies: team-based, problem-based learning, case study, simulation, peer teaching, gamification"
      ]},
      { no: "IV", title: "Clinical teaching & assessment", topics: [
        "Clinical learning environment and selection of clinical experiences",
        "Clinical teaching strategies: bedside teaching, case presentation, OSCE, process recording",
        "Assessment of clinical competence: checklists, rating scales, portfolios"
      ]},
      { no: "V", title: "Teaching media & technology", topics: [
        "Types of media: still visuals, projected, moving visuals, audio, realia and models",
        "Digital resources: e-learning, videoconferencing, telehealth and mobile learning",
        "Principles of media use and preparation of teaching aids"
      ]},
      { no: "VI", title: "Evaluation & guidance", topics: [
        "Assessment & evaluation methods: purposes, principles, barriers",
        "Guidance and counseling: objectives, processes and student support",
        "Ethics in education and evidence-based teaching practice"
      ]}
    ]
  },
  {
    code: "FORENSIC",
    subject: "INTRODUCTION TO FORENSIC NURSING AND INDIAN LAWS",
    units: [
      { no: "I", title: "Forensic science & violence", topics: [
        "Definition and scope of forensic science and forensic nursing",
        "History and importance of forensic science in medical practice",
        "Violence: definitions, epidemiology, sources of data",
        "Sexual abuse (children and women) — detection and basic nursing care",
        "Visit to regional forensic science laboratory (practical exposure)"
      ]},
      { no: "II", title: "Forensic nursing practice & roles", topics: [
        "Concept and scope of forensic nursing: settings and sub-specialties",
        "Roles and responsibilities of nurse in care of victims and evidence preservation",
        "Documentation and chain of custody for biological and medico-legal evidence",
        "Ethical issues in forensic nursing and interaction with legal teams"
      ]},
      { no: "III", title: "Overview of Indian laws", topics: [
        "Introduction to Indian Constitution: fundamental rights and human rights commissions",
        "Overview of judicial system: magistrate, district, state and apex courts",
        "Basic outlines of IPC, Indian Evidence Act, Criminal Procedure Code (overview)",
        "POSCO Act (Protection of Children from Sexual Offences) — basics",
        "Role of a nurse as witness and legal responsibilities"
      ]}
    ]
  }
];

/* =============== STATE =============== */
const storeKey = "syllabrix_planner_v7";
let state = JSON.parse(localStorage.getItem(storeKey) || "{}");
if(!state.important) state.important = [];
if(!state.nextUp) state.nextUp = [];
if(!state.pinned) state.pinned = [];
if(!state.topic) state.topic = {};
if (!state.planName) state.planName = "My Study Plan";
if (!state.targetDate) state.targetDate = "";
if (typeof state.preferImportant !== 'boolean') state.preferImportant = false;
if (typeof state.countdownEnabled !== 'boolean') state.countdownEnabled = false;
if (!state.schedule) state.schedule = {};

function save(){ localStorage.setItem(storeKey, JSON.stringify(state)); }
const idFor = (type, code, unitNo, idx) => [type, code, String(unitNo).replace(/\s+/g,''), idx].join("::");

/* =============== DOM =============== */
const $ = (s) => document.querySelector(s);
const $$ = (s) => Array.from(document.querySelectorAll(s));
const views = { dash: $('#view-dashboard'), syl: $('#view-syllabus'), tools: $('#view-tools') };
const subjectTabs = $('#subject-tabs'); const subjectsEl = $('#subjects');
const plansByDate = $('#plansByDate');
const subjectColors = ['var(--sub-c1)', 'var(--sub-c2)', 'var(--sub-c3)', 'var(--sub-c4)', 'var(--sub-c5)'];

/* =============== ICONS =============== */
const ICONS = {
    pin: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 10c0 7-9 13-9 13s-9-6-9-13a9 9 0 0 1 18 0z"></path><circle cx="12" cy="10" r="3"></circle></svg>`,
    star: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polygon points="12 2 15.09 8.26 22 9.27 17 14.14 18.18 21.02 12 17.77 5.82 21.02 7 14.14 2 9.27 8.91 8.26 12 2"></polygon></svg>`,
    next: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="13 17 18 12 13 7"></polyline><polyline points="6 17 11 12 6 7"></polyline></svg>`,
    add: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="5" x2="12" y2="19"></line><line x1="5" y1="12" x2="19" y2="12"></line></svg>`,
    notes: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M13.5 2H6.5A2.5 2.5 0 0 0 4 4.5v15A2.5 2.5 0 0 0 6.5 22h11a2.5 2.5 0 0 0 2.5-2.5v-11L13.5 2z"></path><polyline points="13.5 2 13.5 9.5 21 9.5"></polyline></svg>`,
    bold: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M6 4h8a4 4 0 0 1 4 4 4 4 0 0 1-4 4H6z"></path><path d="M6 12h9a4 4 0 0 1 4 4 4 4 0 0 1-4 4H6z"></path></svg>`,
    italic: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="19" y1="4" x2="10" y2="4"></line><line x1="14" y1="20" x2="5" y2="20"></line><line x1="15" y1="4" x2="9" y2="20"></line></svg>`,
    underline: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M6 3v7a6 6 0 0 0 6 6 6 6 0 0 0 6-6V3"></path><line x1="4" y1="21" x2="20" y2="21"></line></svg>`,
    list: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="8" y1="6" x2="21" y2="6"></line><line x1="8" y1="12" x2="21" y2="12"></line><line x1="8" y1="18" x2="21" y2="18"></line><line x1="3" y1="6" x2="3.01" y2="6"></line><line x1="3" y1="12" x2="3.01" y2="12"></line><line x1="3" y1="18" x2="3.01" y2="18"></line></svg>`,
    alignLeft: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="17" y1="10" x2="3" y2="10"></line><line x1="21" y1="6" x2="3" y2="6"></line><line x1="21" y1="14" x2="3" y2="14"></line><line x1="17" y1="18" x2="3" y2="18"></line></svg>`,
    alignCenter: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="10" x2="6" y2="10"></line><line x1="21" y1="6" x2="3" y2="6"></line><line x1="21" y1="14" x2="3" y2="14"></line><line x1="18" y1="18" x2="6" y2="18"></line></svg>`,
    alignRight: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="21" y1="10" x2="7" y2="10"></line><line x1="21" y1="6" x2="3" y2="6"></line><line x1="21" y1="14" x2="3" y2="14"></line><line x1="21" y1="18" x2="7" y2="18"></line></svg>`,
    image: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect x="3" y="3" width="18" height="18" rx="2" ry="2"></rect><circle cx="8.5" cy="8.5" r="1.5"></circle><polyline points="21 15 16 10 5 21"></polyline></svg>`,
    draw: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M12 20h9"></path><path d="M16.5 3.5a2.121 2.121 0 0 1 3 3L7 19l-4 1 1-4L16.5 3.5z"></path></svg>`,
    play: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polygon points="5 3 19 12 5 21 5 3"></polygon></svg>`,
    pause: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect x="6" y="4" width="4" height="16"></rect><rect x="14" y="4" width="4" height="16"></rect></svg>`,
}

/* =============== SEARCH INDEX =============== */
let searchIndex = [];
function buildSearch(){
  searchIndex = [];
  DATA.forEach(sub=>sub.units.forEach(u=>u.topics.forEach((t,i)=>{
    searchIndex.push({ id:idFor('topic', sub.code, u.no, i), text:`${sub.code} ${u.title} ${t}`.toLowerCase(), ctx:`${sub.code} · Unit ${u.no}`, title:t })
  })));
}

/* =============== UI HELPERS =============== */
function showToast(message) {
    const toast = $('#toast');
    toast.textContent = message;
    toast.classList.remove('hidden');
    toast.classList.add('show');
    setTimeout(() => {
        toast.classList.remove('show');
    }, 3000);
}

function setTheme(t, fromClick = false){ 
    document.body.setAttribute('data-theme', t); 
    localStorage.setItem('theme', t);
    if(fromClick) showToast(`Switched to ${t.charAt(0).toUpperCase() + t.slice(1)} Mode`);
}
const savedTheme = localStorage.getItem('theme'); 
setTheme(savedTheme || 'dark');
$('#themeToggle')?.addEventListener('click', ()=> setTheme(document.body.getAttribute('data-theme')==='dark'?'light':'dark', true));

function formatTopicText(text) {
    if (!text.includes(':')) return text;
    const parts = text.split(':');
    const title = parts[0].trim() + ':';
    const itemsText = parts.slice(1).join(':').replace(/, and /g, ', ');
    const items = itemsText.split(/, | and /).map(s => s.trim().replace(/\.$/, ''));

    if (items.length <= 1) return text;

    const listItems = items.map(item => {
        if (!item) return '';
        const subItemsMatch = item.match(/\(([^)]+)\)/);
        const mainItem = item.replace(/\(([^)]+)\)/, '').trim();
        return `<li>${mainItem}${subItemsMatch ? ` <span class="muted">(${subItemsMatch[1]})</span>` : ''}</li>`;
    }).join('');

    return `<div>${title}</div><ul style="margin: 4px 0 0 0; padding-left: 18px; list-style-type: '•  ';">${listItems}</ul>`;
}

function computeProgress(){
  let totalTopics=0, doneTopics=0, totalUnits=0, doneUnits=0;
  const bySubject = {};
  DATA.forEach(s=>{
    let sTotalT=0, sDoneT=0, sTotalU=0, sDoneU=0;
    s.units.forEach(u=>{
      sTotalU++;
      let allTopicsInUnitDone = u.topics.length > 0;
      u.topics.forEach((t,i)=>{
        sTotalT++;
        const id = idFor("topic", s.code, u.no, i);
        if(state.topic[id]?.done) {
          sDoneT++;
        } else {
          allTopicsInUnitDone = false;
        }
      });
      if(allTopicsInUnitDone) sDoneU++;
    });
    bySubject[s.code] = {
      doneTopics: sDoneT, totalTopics: sTotalT, pct: sTotalT ? Math.round(sDoneT*100/sTotalT) : 0,
      doneUnits: sDoneU, totalUnits: sTotalU
    };
    totalTopics += sTotalT; doneTopics += sDoneT;
    totalUnits += sTotalU; doneUnits += sDoneU;
  });
  return {
    totalTopics, doneTopics, pct: totalTopics ? Math.round(doneTopics*100/totalTopics) : 0,
    totalUnits, doneUnits, bySubject
  };
}

function renderCircularProgress(pct) {
    const r = 45;
    const circumference = 2 * Math.PI * r;
    const offset = circumference - (pct / 100) * circumference;
    return `
        <svg width="120" height="120" viewBox="0 0 100 100">
            <defs>
                <linearGradient id="progress-gradient" x1="0%" y1="0%" x2="0%" y2="100%">
                    <stop offset="0%" style="stop-color:var(--brand-dark);" />
                    <stop offset="100%" style="stop-color:${subjectColors[1]};" />
                </linearGradient>
            </defs>
            <circle class="progress-circle-bg" cx="50" cy="50" r="${r}"></circle>
            <circle class="progress-circle-fg" cx="50" cy="50" r="${r}"
                stroke-dasharray="${circumference}"
                stroke-dashoffset="${offset}"
            ></circle>
            <text x="50" y="55" class="progress-circle-text">${pct}%</text>
        </svg>`;
}

let countdownInterval = null;
function renderDashboard(){
  const p = computeProgress();
  
  // Countdown
  const countdownContainer = $('#countdown-container');
  const { countdownEnabled, targetDate, planName } = state;
  if(countdownInterval) window.clearInterval(countdownInterval);

  if (countdownEnabled && targetDate) {
      countdownContainer.classList.remove('hidden');
      const updateCountdown = () => {
          const now = new Date().getTime();
          const target = new Date(targetDate).getTime() + (24*60*60*1000); // End of day
          const diff = target - now;

          if (diff < 0) {
              countdownContainer.innerHTML = `<div class="countdown-item"><div class="countdown-label">For ${planName}</div><div class="countdown-timer">Target passed!</div></div>`;
              if(countdownInterval) window.clearInterval(countdownInterval);
          } else {
              const d = Math.floor(diff / (1000 * 60 * 60 * 24));
              const h = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
              const m = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));
              const s = Math.floor((diff % (1000 * 60)) / 1000);
              countdownContainer.innerHTML = `<div class="countdown-item"><div class="countdown-label">Time until ${planName}</div><div class="countdown-timer">${d}d : ${h}h : ${m}m : ${s}s</div></div>`;
          }
      };
      updateCountdown();
      countdownInterval = window.setInterval(updateCountdown, 1000);
  } else { 
      countdownContainer.classList.add('hidden');
  }

  // Progress Circle & Subtitle
  $('#overall-progress-circle').innerHTML = renderCircularProgress(p.pct || 0);
  $('#overall-progress-subtitle').innerHTML = `
      <span class="chip">${p.doneTopics} / ${p.totalTopics} Topics</span>
      <span class="chip">${p.doneUnits} / ${p.totalUnits} Units</span>
  `;

  // Subject Progress Bars
  const by = p.bySubject;
  const subjectHTML = Object.keys(by).map((code, index)=>{
    const item = by[code];
    const color = subjectColors[index % subjectColors.length];
    return `<div class="item" data-act="subject-details" data-code="${code}" style="cursor:pointer; align-items:center">
      <div style="flex:1">
        <div style="display:flex; justify-content:space-between; font-size:14px; margin-bottom: 6px;">
          <strong>${code}</strong>
          <span class="muted">${item.doneTopics}/${item.totalTopics} topics</span>
        </div>
        <div class="progress"><i style="width:${item.pct}%; background:${color};"></i></div>
      </div>
      <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="muted"><polyline points="9 18 15 12 9 6"></polyline></svg>
    </div>`;
  }).join('');
  $('#subject-progress-list').innerHTML = subjectHTML;

  renderDailyPlan();
  renderNextUpList();
  renderPinnedTopics();
}

function renderDailyPlan(date = new Date()) {
    const $list = $('#daily-plan-list');
    const $title = $('#daily-plan-title');
    const dateKey = date.toISOString().split('T')[0];
    const topics = state.schedule[dateKey] || [];

    const today = new Date();
    today.setHours(0,0,0,0);
    date.setHours(0,0,0,0);

    if (date.getTime() === today.getTime()) {
      $title.textContent = "Today's Plan";
    } else {
      $title.textContent = `Plan for ${date.toLocaleDateString(undefined, {month: 'long', day: 'numeric'})}`;
    }
    
    if (topics.length === 0) {
        $list.innerHTML = `<div class="muted" style="text-align:center; padding: 20px 0;">No topics planned for this day.</div>`;
    } else {
        $list.innerHTML = `<div class="info-list">` + topics.map(id => {
            const details = parseTopicId(id);
            if (!details) return '';
            const isDone = state.topic[id]?.done;
            return `
                <div class="info-list-item" style="${isDone ? 'opacity:0.6;' : ''}">
                    <input type="checkbox" id="plan-check-${id}" data-id="${id}" ${isDone ? 'checked' : ''} />
                    <label for="plan-check-${id}" class="info-list-item-content">
                      <div>${details.topic}</div>
                      <div class="muted" style="font-size:12px">${details.code} &middot; Unit ${details.unitNo}</div>
                    </label>
                </div>`;
        }).join('') + `</div>`;
    }
}

function renderNextUpList() {
    const $list = $('#next-up-list');
    if (state.nextUp.length === 0) {
        $list.innerHTML = `<div class="muted" style="text-align:center; padding: 20px 0;">Add topics to your "Next Up" list from the Syllabus tab.</div>`;
    } else {
        $list.innerHTML = `<div class="info-list">` + state.nextUp.map(id => {
            const details = parseTopicId(id); if (!details) return '';
            return `<div class="item">
                <div style="flex:1">
                  <div style="font-weight:600">${details.topic}</div>
                  <div class="muted" style="font-size:12px">${details.code} &middot; Unit ${details.unitNo}</div>
                </div>
                <button class="i-btn active" data-act="nextup" data-id="${id}" title="Remove from Next Up">${ICONS.next}</button>
            </div>`;
        }).join('') + `</div>`;
    }
}

function renderPinnedTopics() {
    const $list = $('#pinned-topics-list');
    if (state.pinned.length === 0) {
        $list.innerHTML = `<div class="muted" style="text-align:center; padding: 20px 0;">Pin your most important topics for quick access.</div>`;
    } else {
        $list.innerHTML = `<div class="info-list">` + state.pinned.map(id => {
            const details = parseTopicId(id); if (!details) return '';
            return `<div class="item">
                <div style="flex:1">
                  <div style="font-weight:600">${details.topic}</div>
                  <div class="muted" style="font-size:12px">${details.code} &middot; Unit ${details.unitNo}</div>
                </div>
                <button class="i-btn active" data-act="pin" data-id="${id}" title="Unpin Topic">${ICONS.pin}</button>
            </div>`;
        }).join('') + `</div>`;
    }
}

function renderSubjectTabs(){
  subjectTabs.innerHTML = DATA.map((s,i)=>`<button class="subject-chip ${i===0?'active':''}" data-code="${s.code}">${s.code}</button>`).join('');
}

function renderSubjects(activeCode){
  subjectsEl.innerHTML = '';
  const sub = DATA.find(s=>s.code===activeCode) || DATA[0];
  sub.units.forEach(u=>{
    const unit = document.createElement('div'); unit.className='unit';
    const unitId = `${sub.code}::${String(u.no).replace(/\s+/g,'')}`;
    unit.innerHTML = `<div class="unit-top"><div>
      <div style="font-weight:800">Unit ${u.no}: ${u.title}</div>
      <div class="muted" style="font-size:12px">${u.topics.length} topics</div></div>
      <div class="row" style="gap: 4px;">
        <button class="btn small brand" data-act="add-unit-plan" data-unitid="${unitId}">+ Plan</button>
        <button class="btn small danger" data-act="reset-unit" data-unitid="${unitId}">Reset</button>
      </div>
    </div>`;
    const det = document.createElement('details'); det.className='topic'; det.innerHTML = `<summary>View Topics</summary>`;
    const list = document.createElement('ul'); list.className='checks';
    u.topics.forEach((t,i)=>{
      const tid = idFor('topic', sub.code, u.no, i); 
      const done = !!state.topic[tid]?.done;
      const isImp = state.important.includes(tid);
      const isNextUp = state.nextUp.includes(tid);
      const isPinned = state.pinned.includes(tid);
      const hasNotes = (state.topic[tid]?.notes?.length || 0) > 0;
      const li = document.createElement('li'); li.className='item';
      li.innerHTML = `
        <input type="checkbox" id="${tid}" ${done?'checked':''}>
        <label for="${tid}">${formatTopicText(t)}</label>
        <div class="icons">
          <button class="i-btn ${isPinned ?'active':''}" data-act="pin" data-id="${tid}" title="Pin topic">${ICONS.pin}</button>
          <button class="i-btn ${isImp?'active':''}" data-act="important" data-id="${tid}" title="Mark important">${ICONS.star}</button>
          <button class="i-btn ${isNextUp?'active':''}" data-act="nextup" data-id="${tid}" title="Add to Next Up">${ICONS.next}</button>
          <button class="i-btn" data-act="add-plan" data-id="${tid}" title="Add to Plan">${ICONS.add}</button>
          <button class="i-btn ${hasNotes?'active':''}" data-act="notes" data-id="${tid}" title="Notes">${ICONS.notes}</button>
        </div>`;
      list.appendChild(li);
    });
    det.appendChild(list); unit.appendChild(det); subjectsEl.appendChild(unit);
  });
}

function renderNotes(){
  const allNotes = [];
  Object.entries(state.topic).forEach(([topicId, topicData]) => {
      if (topicData.notes && topicData.notes.length > 0) {
          topicData.notes.forEach((note, index) => {
              if (Object.values(note).some(Boolean)) {
                  allNotes.push({ topicId, noteIndex: index, ...note });
              }
          });
      }
  });

  const list = allNotes.map(note => {
    const meta = parseTopicId(note.topicId);
    return `<div class="item">
      <div style="flex:1">
        <div style="font-weight:800">${note.title || 'Untitled Note'}</div>
        ${note.subtitle ? `<div class="muted">${note.subtitle}</div>`:''}
        <div class="muted" style="font-size:12px">${meta?.code} · Unit ${meta?.unitNo}</div>
      </div>
      <button class="btn small" data-act="notes" data-id="${note.topicId}" data-note-index="${note.noteIndex}">View</button>
    </div>`;
  }).join('');
  $('#notesList').innerHTML = list || `<div class="muted" style="text-align:center; padding: 20px;">No notes yet. Add notes from the Syllabus tab.</div>`;
}

function renderImportant(){
  const list = state.important.map(id=>{
    const m = parseTopicId(id); 
    if (!m) return '';
    return `<div class="item"><div style="flex:1">
      <div style="font-weight:800">${m.topic}</div>
      <div class="muted" style="font-size:12px">${m.code} · Unit ${m.unitNo}</div>
    </div><button class="i-btn active" data-act="important" data-id="${id}">${ICONS.star}</button></div>`;
  }).join('');
  $('#impList').innerHTML = list || `<div class="muted" style="text-align:center; padding: 20px;">No important topics yet. Mark topics as important from the Syllabus tab.</div>`;
}

function renderDateScroller() {
    const container = $('#date-scroller');
    const today = new Date();
    today.setHours(0,0,0,0);
    let html = '';
    
    for (let i = -90; i <= 90; i++) {
        const d = new Date();
        d.setDate(today.getDate() + i);
        d.setHours(0,0,0,0);

        const dayName = d.toLocaleDateString(undefined, { weekday: 'short' });
        const dayNum = d.getDate();
        const dateKey = d.toISOString().split('T')[0];
        const isToday = i === 0;
        const hasPlan = state.schedule[dateKey]?.length > 0;
        
        let monthIndicator = '';
        if (dayNum === 1 || i === -90) {
            monthIndicator = `<div class="month-indicator">${d.toLocaleDateString(undefined, { month: 'short' })}</div>`;
        }
        
        const classes = ['date-item'];
        if (isToday) classes.push('today');
        if (hasPlan) classes.push('has-plan');

        html += `
            <div class="${classes.join(' ')}" data-date="${dateKey}">
                <div class="day-name">${dayName}</div>
                <div class="day-num">${dayNum}</div>
                ${monthIndicator}
            </div>
        `;
    }
    container.innerHTML = html;
    const todayEl = $('.date-item.today');
    if (todayEl) {
        todayEl.classList.add('active');
        todayEl.scrollIntoView({ behavior: 'auto', inline: 'center', block: 'nearest' });
    }
}

function renderPlannerTool() {
    $('#planName').value = state.planName || '';
    $('#targetDate').value = state.targetDate || '';
    $('#planCountdown').checked = !!state.countdownEnabled;
    $('#preferImportant').checked = !!state.preferImportant;
    renderPlansByDate();
}

function renderTopicPicker(){
  const q = $('#topicSearch').value?.toLowerCase() || '';
  const topicSelect = $('#topicSelect');
  if (q.length < 2) {
      topicSelect.classList.add('hidden');
      return;
  }
  const results = searchIndex.filter(r => r.text.includes(q)).slice(0, 100);
  if (results.length === 0) {
      topicSelect.classList.add('hidden');
      return;
  }
  topicSelect.innerHTML = results.map(r => `<option value="${r.id}">${r.title} — ${r.ctx}</option>`).join('');
  topicSelect.classList.remove('hidden');
}

function renderPlansByDate(){
  const sched = state.schedule;
  const dates = Object.keys(sched).sort();
  if (dates.length === 0) {
      plansByDate.innerHTML = `<div class="muted" style="text-align:center; padding: 20px;">Your plan is empty. Use the generator or add topics manually.</div>`;
      return;
  }
  plansByDate.innerHTML = dates.map(d=>{
    const items = sched[d].map(id=>{ const m = parseTopicId(id); return `<li>${m?.topic} <span class="muted">(${m?.code})</span> <button class="btn small danger" data-act="rm-item" data-date="${d}" data-id="${id}">✕</button></li>`; }).join('');
    const pretty = new Date(d+'T00:00:00').toLocaleDateString(undefined,{weekday:'long', year:'numeric', month:'long', day:'numeric'});
    return `<div class="day-card"><div class="day-head"><strong>${pretty}</strong>
      <span class="x"><button class="btn small danger" data-act="rm-date" data-date="${d}">Clear Day</button></span></div>
      <ul class="day-list">${items}</ul></div>`;
  }).join('');
}

/* =============== UTIL =============== */
function parseTopicId(id){
  if (!id) return null;
  const parts = id.split('::');
  if (parts.length < 4) return null;
  const [, code, unitNo, idx] = parts;
  const sub = DATA.find(s=>s.code===code); 
  if (!sub) return null;
  const unit = sub.units.find(u=>String(u.no).replace(/\s+/g,'')===unitNo);
  if (!unit) return null;
  const topic = unit.topics[Number(idx)];
  if (topic === undefined) return null;
  return { code, unitNo: unit.no, topic, subject: sub.subject, unitTitle: unit.title };
}

function getTopicsForUnit(unitId) {
    const [code, unitNo] = unitId.split('::');
    const sub = DATA.find(s => s.code === code);
    const unit = sub?.units.find(u => String(u.no).replace(/\s+/g, '') === unitNo);
    if (!unit) return [];
    return unit.topics.map((t, i) => idFor('topic', code, unit.no, i));
}

function addTopicToPlan(date, topicId){
  if(!state.schedule[date]) state.schedule[date]=[];
  if(!state.schedule[date].includes(topicId)) state.schedule[date].push(topicId);
}

function removeTopicFromPlan(date, topicId){
  if(!state.schedule[date]) return;
  state.schedule[date] = state.schedule[date].filter(x=>x!==topicId);
  if(state.schedule[date].length===0) delete state.schedule[date];
}

function generateSmart(days){
  if (!confirm("This will clear your current plan and generate a new one. Are you sure?")) return;

  state.schedule = {}; // Clear existing schedule
  state.preferImportant = $('#preferImportant').checked;

  const all = [];
  DATA.forEach(sub => sub.units.forEach(u => u.topics.forEach((t, i) => all.push(idFor('topic', sub.code, u.no, i)))));
  const imps = new Set(state.preferImportant ? state.important : []);
  const sorted = all.filter(tid => !(state.topic[tid]?.done)).sort((x, y) => (imps.has(y) ? 1 : 0) - (imps.has(x) ? 1 : 0));
  const perDay = Math.ceil(sorted.length / Math.max(1, days));
  const start = new Date(); start.setHours(0, 0, 0, 0);
  sorted.forEach((tid, i) => {
      const d = new Date(start); d.setDate(start.getDate() + Math.floor(i / perDay));
      const key = d.toISOString().split('T')[0];
      addTopicToPlan(key, tid);
  });
  save();
  renderPlannerTool();
  renderDashboard();
  renderDateScroller();
  showToast(`Generated a ${days}-day smart plan!`);
}

/* =============== EVENTS =============== */
// Main tabs
$('#mainTabs')?.addEventListener('click', e=>{
  const t = e.target.closest('button'); if(!t) return; $$('#mainTabs .seg-btn').forEach(b=>b.classList.toggle('active', b===t));
  views.dash?.classList.toggle('hidden', t.dataset.tab!=="dashboard");
  views.syl?.classList.toggle('hidden', t.dataset.tab!=="syllabus");
  views.tools?.classList.toggle('hidden', t.dataset.tab!=="tools");
});

// Dashboard tabs
$('#dashboard-tabs')?.addEventListener('click', e => {
    const t = e.target.closest('button'); if (!t) return;
    $$('#dashboard-tabs .seg-btn').forEach(b => b.classList.toggle('active', b === t));
    $$('#dashboard-tab-content > div').forEach(el => {
        el.classList.toggle('hidden', el.id !== `dash-${t.dataset.dTab}`);
    });
});

// Subject tabs click
subjectTabs?.addEventListener('click', e=>{
  const b = e.target.closest('button'); if(!b) return; $$('#subject-tabs .subject-chip').forEach(x=>x.classList.toggle('active', x===b));
  renderSubjects(b.dataset.code);
});

// Date Scroller
$('#date-scroller')?.addEventListener('click', e => {
    const item = e.target.closest('.date-item');
    if (!item || !item.dataset.date) return;
    $$('.date-item').forEach(d => d.classList.remove('active'));
    item.classList.add('active');
    const date = new Date(item.dataset.date + 'T00:00:00');
    renderDailyPlan(date);
});

// Global click handler (checkbox, important, notes, etc.)
addEventListener('click', e=>{
  const target = e.target;
  const cb = target.matches('input[type="checkbox"]') ? target : null;
  if(cb && cb.id.startsWith('topic::')){ state.topic[cb.id]=state.topic[cb.id]||{}; state.topic[cb.id].done = cb.checked; save(); renderDashboard(); return; }
  if(cb && cb.id.startsWith('plan-check-')){ const topicId = cb.dataset.id; state.topic[topicId]=state.topic[topicId]||{}; state.topic[topicId].done = cb.checked; save(); renderDashboard(); return;}

  const actBtn = target.closest('[data-act]'); if(!actBtn) return; const id = actBtn.dataset.id; const act = actBtn.dataset.act;
  if(act==='important' && id){ const idx = state.important.indexOf(id); if(idx>-1) state.important.splice(idx,1); else state.important.push(id); save(); actBtn.classList.toggle('active', idx===-1); renderImportant(); return; }
  if(act==='nextup' && id){ const idx = state.nextUp.indexOf(id); if(idx>-1) state.nextUp.splice(idx,1); else state.nextUp.push(id); save(); actBtn.classList.toggle('active', idx===-1); renderNextUpList(); return; }
  if(act==='pin' && id){ const idx = state.pinned.indexOf(id); if(idx>-1) state.pinned.splice(idx,1); else state.pinned.push(id); save(); actBtn.classList.toggle('active', idx===-1); renderPinnedTopics(); return; }
  if(act==='notes' && id){ openNotes(id, actBtn.dataset.noteIndex); return; }
  if(act==='add-plan' && id){ openAddModal(id); return; }
  if(act==='add-unit-plan') { openAddModal(null, actBtn.dataset.unitid); return; }
  if(act==='reset-unit') { if (confirm('Reset progress for this entire unit?')) { getTopicsForUnit(actBtn.dataset.unitid).forEach(tid => { if(state.topic[tid]) state.topic[tid].done = false; }); save(); renderSubjects($('#subject-tabs .active').dataset.code); renderDashboard(); } return; }
  if(act==='rm-item' && id){ removeTopicFromPlan(actBtn.dataset.date, id); save(); renderPlansByDate(); renderDateScroller(); return; }
  if(act==='rm-date'){ delete state.schedule[actBtn.dataset.date]; save(); renderPlansByDate(); renderDateScroller(); return; }
  if(act==='subject-details') { openSubjectModal(actBtn.dataset.code); return; }
});

// Search
$('#search')?.addEventListener('input', e=>{
  const q = e.target.value.trim().toLowerCase(); const box = $('#search-results');
  if(q.length<2){ box.classList.add('hidden'); return; }
  const res = searchIndex.filter(r=>r.text.includes(q)).slice(0,12);
  if(!res.length){ box.classList.add('hidden'); return; }
  box.innerHTML = res.map(r=>`<div class="sr-item" data-id="${r.id}"><div class="sr-title">${r.title}</div><div class="sr-ctx">${r.ctx}</div></div>`).join('');
  box.classList.remove('hidden');
});
$('#search-results')?.addEventListener('click', e=>{
  const it = e.target.closest('.sr-item'); if(!it || !it.dataset.id) return; const meta = parseTopicId(it.dataset.id);
  if (!meta) return;
  $$('#mainTabs .seg-btn').forEach(b=> { if (b.dataset.tab==='syllabus') b.click() });
  const chip = $(`#subject-tabs .subject-chip[data-code="${meta.code}"]`); if(chip) chip.click();
  setTimeout(()=>{
    const el = document.getElementById(it.dataset.id); if(!el) return; const detailsEl = el.closest('details'); if(detailsEl) detailsEl.open = true; el.scrollIntoView({behavior:'smooth', block:'center'});
    const itemEl = el.closest('.item');
    if (itemEl) {
        itemEl.style.transition = 'background-color 0.5s';
        itemEl.style.backgroundColor = 'var(--brand-bg)';
        setTimeout(() => { itemEl.style.backgroundColor = ''; }, 2000);
    }
  }, 100);
  $('#search-results').classList.add('hidden'); $('#search').value='';
});
addEventListener('click', e=>{ if(!e.target.closest('#search-container')) $('#search-results')?.classList.add('hidden'); });

// Tools tabs
$('#toolsTabs')?.addEventListener('click', e=>{
  const b = e.target.closest('button'); if(!b) return; $$('#toolsTabs .seg-btn').forEach(x=>x.classList.toggle('active', x===b));
  $('#tools-planner')?.classList.toggle('hidden', b.dataset.t!=="planner");
  $('#tools-notes')?.classList.toggle('hidden', b.dataset.t!=="notes");
  $('#tools-important')?.classList.toggle('hidden', b.dataset.t!=="important");
});

// Planner controls
$('#savePlanSettings')?.addEventListener('click', ()=>{
    state.planName = $('#planName').value?.trim() || "My Study Plan";
    state.targetDate = $('#targetDate').value || '';
    state.countdownEnabled = $('#planCountdown').checked;
    save();
    renderDashboard();
    showToast("Plan settings saved!");
});
$$('[data-auto]').forEach(b => b.addEventListener('click', () => generateSmart(Number(b.dataset.auto))));
$('#genCustom')?.addEventListener('click', () => { const d = parseInt($('#customDays').value || '0', 10); if (d > 0) generateSmart(d); });
$('#topicSearch')?.addEventListener('input', renderTopicPicker);
$('#addToPlan')?.addEventListener('click', () => {
    const date = $('#addDate').value;
    const id = $('#topicSelect').value;
    if (!date || !id) {
        showToast('Please select a topic and a date.');
        return;
    }
    addTopicToPlan(date, id);
    save();
    renderPlansByDate();
    renderDateScroller();
    showToast("Topic added to plan!");
    $('#topicSearch').value = '';
    renderTopicPicker();
});

// Add-to-Plan Modal logic
let pendingAddTopicId = null;
let pendingAddUnitId = null;
function openAddModal(topicId, unitId){ 
  pendingAddTopicId = topicId; 
  pendingAddUnitId = unitId;
  $('#addModal')?.classList.remove('hidden');
  $('#modalDate').value = new Date().toISOString().slice(0,10);
  let summary = '';
  if (topicId) {
    const m = parseTopicId(topicId); 
    summary = `${m?.code} · Unit ${m?.unitNo} — ${m?.topic}`;
  } else if (unitId) {
    const [code, unitNo] = unitId.split('::');
    const sub = DATA.find(s=>s.code===code);
    const unit = sub?.units.find(u=>String(u.no).replace(/\s+/g,'')===unitNo);
    summary = `All topics from ${code} - Unit ${unit?.no}: ${unit?.title}`;
  }
  $('#modalTopicSummary').textContent = summary;
}
$('#addClose')?.addEventListener('click', ()=> $('#addModal')?.classList.add('hidden'));
$('#modalAddBtn')?.addEventListener('click', ()=>{
  const date = $('#modalDate').value;
  if(!date) { showToast("Please select a date."); return; }
  if(pendingAddTopicId) addTopicToPlan(date, pendingAddTopicId);
  if(pendingAddUnitId) getTopicsForUnit(pendingAddUnitId).forEach(tid => addTopicToPlan(date, tid));
  save(); 
  renderPlannerTool(); 
  renderPlansByDate(); 
  renderDateScroller(); 
  $('#addModal')?.classList.add('hidden');
  showToast("Added to plan!");
});

// Subject Detail Modal
function openSubjectModal(code) {
    const sub = DATA.find(s => s.code === code);
    if (!sub) return;
    $('#smTitle').textContent = `Progress for ${code}`;
    let html = '';
    sub.units.forEach(u => {
        html += `<div class="unit" style="margin: 8px 0;"><strong style="display: block; margin-bottom: 8px;">Unit ${u.no}: ${u.title}</strong>`;
        u.topics.forEach((t, i) => {
            const tid = idFor('topic', sub.code, u.no, i);
            const isDone = state.topic[tid]?.done;
            html += `<div style="padding: 2px 0; display: flex; align-items: center; gap: 8px;">${isDone ? '✅' : '🔲'} <span>${t}</span></div>`;
        });
        html += `</div>`;
    });
    $('#smBody').innerHTML = html;
    $('#subjectModal')?.classList.remove('hidden');
}
$('#smClose')?.addEventListener('click', ()=> $('#subjectModal')?.classList.add('hidden'));


// ================= Notes Modal V2 =================
let currentNoteId = null;
let currentNoteIndex = 0;
let autosaveTimeout = null;

function showSaveStatus(text, duration = 0) {
    const el = $('#noteSaveStatus');
    if (!el) return;
    el.textContent = text;
    el.style.opacity = '1';

    if (duration > 0) {
        setTimeout(() => {
            el.style.opacity = '0';
        }, duration);
    }
}

function saveCurrentNote(isAutosave = false) {
    if (!currentNoteId) return;
    const notes = state.topic[currentNoteId]?.notes;
    if (!notes) return;

    if (autosaveTimeout) clearTimeout(autosaveTimeout);
    autosaveTimeout = null;

    unwrapAllImages();
    const note = {
        title: $('#noteTitle').value.trim(),
        subtitle: $('#noteSubtitle').value.trim(),
        content: $('#noteContent').innerHTML.trim(),
        keypoints: $('#noteKeys').value.trim()
    };
    notes[currentNoteIndex] = note;
    save();

    renderNotes(); // Update main notes list in tools tab
    const currentNoteTitle = note.title || `Note ${currentNoteIndex + 1}`;
    const noteListItem = $(`#notes-list-panel .note-list-item[data-note-index="${currentNoteIndex}"]`);
    if (noteListItem) noteListItem.textContent = currentNoteTitle;


    if (isAutosave) {
        showSaveStatus("Saved", 2000);
    } else { // Manual save
        $('#notesModal')?.classList.add('hidden');
        showToast("Note saved!");
    }
}

function triggerAutosave() {
    if (autosaveTimeout) clearTimeout(autosaveTimeout);
    showSaveStatus("Saving...");
    autosaveTimeout = window.setTimeout(() => saveCurrentNote(true), 2000);
}

function setupEditorToolbar() {
    const toolbar = $('#editorToolbar');
    const tools = [
        { cmd: 'bold', icon: ICONS.bold, title: 'Bold' },
        { cmd: 'italic', icon: ICONS.italic, title: 'Italic' },
        { cmd: 'underline', icon: ICONS.underline, title: 'Underline' },
        { cmd: 'insertUnorderedList', icon: ICONS.list, title: 'Bullet List' },
        { cmd: 'justifyLeft', icon: ICONS.alignLeft, title: 'Align Left' },
        { cmd: 'justifyCenter', icon: ICONS.alignCenter, title: 'Align Center' },
        { cmd: 'justifyRight', icon: ICONS.alignRight, title: 'Align Right' },
        { cmd: 'addImage', icon: ICONS.image, title: 'Add Image' },
        { cmd: 'addDrawing', icon: ICONS.draw, title: 'Add Drawing' }
    ];
    toolbar.innerHTML = tools.map(t => `<button class="i-btn" data-command="${t.cmd}" title="${t.title}">${t.icon}</button>`).join('');
}

function renderNoteUI() {
    if (!currentNoteId) return;
    const topicData = state.topic[currentNoteId];
    const notes = topicData?.notes || [];
    
    const listPanel = $('#notes-list-panel');
    listPanel.innerHTML = notes.map((note, index) => 
        `<div class="note-list-item ${index === currentNoteIndex ? 'active' : ''}" data-note-index="${index}">${note.title || `Note ${index + 1}`}</div>`
    ).join('');
    
    const note = notes[currentNoteIndex];
    $('#noteTitle').value = note?.title || '';
    $('#noteSubtitle').value = note?.subtitle || '';
    $('#noteContent').innerHTML = note?.content || '';
    $('#noteKeys').value = note?.keypoints || '';
}

function unwrapAllImages() {
    const editor = $('#noteContent');
    editor.querySelectorAll('.resizable-image-wrapper').forEach(wrapper => {
        const img = wrapper.querySelector('img');
        if (img) {
            const wrapperEl = wrapper;
            img.style.width = wrapperEl.style.width;
            img.style.height = wrapperEl.style.height;
            wrapper.parentNode?.replaceChild(img, wrapper);
        }
    });
}

function openNotes(id, noteIndex){ 
  if (autosaveTimeout) saveCurrentNote(true);
  stopNoteAudio();

  currentNoteId = id; 
  currentNoteIndex = parseInt(noteIndex || '0', 10);
  const m = parseTopicId(id); 
  if(!m) { console.error("Invalid topic ID for notes:", id); return; }

  state.topic[currentNoteId] = state.topic[currentNoteId] || {};
  if (!state.topic[currentNoteId].notes || state.topic[currentNoteId].notes.length === 0) {
      state.topic[currentNoteId].notes = [{ title: 'Note 1', subtitle: '', content: '', keypoints: '' }];
      currentNoteIndex = 0;
  }
  
  $('#nmTitle').textContent=`Notes — ${m.code} · Unit ${m.unitNo}`;
  renderNoteUI();
  $('#notesModal')?.classList.remove('hidden'); 
  $('#noteContent').focus();
}

$('#editorToolbar')?.addEventListener('click', (e) => {
    const btn = e.target.closest('button');
    if (!btn) return;
    const command = btn.dataset.command;
    const noteContent = $('#noteContent');

    if (command === 'addImage') {
        $('#imageInput').click();
    } else if (command === 'addDrawing') {
        openDrawingCanvas();
    } else if (command) {
        document.execCommand(command, false, undefined);
        triggerAutosave();
    }
    noteContent.focus();
});

$('#imageInput')?.addEventListener('change', (e) => {
    const file = e.target.files?.[0];
    if (!file) return;
    const reader = new FileReader();
    reader.onload = (event) => {
        const dataUrl = event.target?.result;
        document.execCommand('insertImage', false, dataUrl);
        triggerAutosave();
    };
    reader.readAsDataURL(file);
    e.target.value = '';
});

$('#noteContent')?.addEventListener('click', (e) => {
    const target = e.target;
    unwrapAllImages();
    if (target.tagName === 'IMG') {
        const imgTarget = target;
        const wrapper = document.createElement('div');
        wrapper.className = 'resizable-image-wrapper';
        wrapper.setAttribute('contenteditable', 'false');
        wrapper.style.width = imgTarget.style.width || `${imgTarget.width}px`;
        wrapper.style.height = imgTarget.style.height || `auto`;
        
        imgTarget.parentNode?.insertBefore(wrapper, imgTarget);
        wrapper.appendChild(imgTarget);
        imgTarget.style.width = '100%';
        imgTarget.style.height = '100%';
    }
});

$('#notes-list-panel')?.addEventListener('click', e => {
    const item = e.target.closest('.note-list-item');
    if (item && item.dataset.noteIndex) {
        if (autosaveTimeout) saveCurrentNote(true);
        stopNoteAudio();
        currentNoteIndex = parseInt(item.dataset.noteIndex, 10);
        renderNoteUI();
    }
});
$('#nmClose')?.addEventListener('click', ()=> {
    if (autosaveTimeout) saveCurrentNote(true);
    stopNoteAudio();
    unwrapAllImages();
    $('#notesModal')?.classList.add('hidden');
});

$('#noteSave')?.addEventListener('click', () => saveCurrentNote(false));

$('#noteNew')?.addEventListener('click', () => {
    if (!currentNoteId) return;
    const notes = state.topic[currentNoteId]?.notes;
    if (!notes) return;
    if (autosaveTimeout) saveCurrentNote(true);
    stopNoteAudio();
    
    unwrapAllImages();
    const newIndex = notes.length;
    notes.push({ title: `Note ${newIndex + 1}`, subtitle: '', content: '', keypoints: '' });
    currentNoteIndex = newIndex;
    renderNoteUI();
});
$('#noteDelete')?.addEventListener('click', ()=>{ 
    if(!currentNoteId || !confirm('Delete this note?')) return; 
    
    if (autosaveTimeout) clearTimeout(autosaveTimeout);
    autosaveTimeout = null;
    stopNoteAudio();
    
    const notes = state.topic[currentNoteId]?.notes;
    if (!notes) return;
    notes.splice(currentNoteIndex, 1);
    if (currentNoteIndex >= notes.length) {
        currentNoteIndex = Math.max(0, notes.length - 1);
    }
    if (notes.length === 0) {
        $('#notesModal')?.classList.add('hidden');
    } else {
        renderNoteUI();
    }
    save(); 
    renderNotes(); 
    showToast("Note deleted.");
});
$('#noteKeys')?.addEventListener('keydown', e => {
    const el = e.target;
    if (e.key === 'Enter') {
        e.preventDefault();
        const { value, selectionStart } = el;
        const newValue = value.slice(0, selectionStart) + '\n• ' + value.slice(selectionStart);
        el.value = newValue;
        el.selectionStart = el.selectionEnd = selectionStart + 3;
    }
    if (e.key === 'Backspace') {
        const { value, selectionStart } = el;
        if (selectionStart > 1 && value.substring(selectionStart - 2, selectionStart) === '• ') {
            e.preventDefault();
            const newValue = value.slice(0, selectionStart - 2) + value.slice(selectionStart);
            el.value = newValue;
            el.selectionStart = el.selectionEnd = selectionStart - 2;
        }
    }
});
$('#noteExport')?.addEventListener('click', () => {
    if (autosaveTimeout) saveCurrentNote(true);
    if (!currentNoteId) return;
    unwrapAllImages();
    stopNoteAudio();
    const note = state.topic[currentNoteId]?.notes?.[currentNoteIndex];
    const meta = parseTopicId(currentNoteId);
    if (!note || !meta) { showToast("Error: Could not find note data."); return; }

    const printWindow = window.open('', '_blank');
    if (!printWindow) { showToast("Could not open print window. Please disable popup blockers."); return; }

    printWindow.document.write(`
        <html>
            <head>
                <title>Export Note: ${note.title}</title>
                <style>
                    body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; margin: 2cm; }
                    h1, h2, h3 { margin: 0; }
                    h1 { font-size: 24px; }
                    h2 { font-size: 16px; font-style: italic; color: #666; font-weight: normal; margin-top: 4px; }
                    h3 { font-size: 12px; color: #888; font-weight: normal; margin-top: 8px; margin-bottom: 12px; border-bottom: 1px solid #ccc; padding-bottom: 8px; }
                    #content img { max-width: 100%; border-radius: 4px; }
                    #keypoints { white-space: pre-wrap; background: #f5f5f5; padding: 12px; border-radius: 4px; margin-top: 20px; }
                    @media print {
                        body { margin: 1in; }
                        #keypoints { background: #f5f5f5 !important; -webkit-print-color-adjust: exact; }
                    }
                </style>
            </head>
            <body>
                <h1>${note.title || 'Untitled Note'}</h1>
                ${note.subtitle ? `<h2>${note.subtitle}</h2>` : ''}
                <h3>${meta.code} · Unit ${meta.unitNo} · ${meta.unitTitle}</h3>
                <div id="content">${$('#noteContent').innerHTML}</div>
                ${note.keypoints ? `<h3>Key Points</h3><div id="keypoints">${note.keypoints}</div>` : ''}
            </body>
        </html>
    `);
    
    printWindow.document.close();
    setTimeout(() => {
        printWindow.focus();
        printWindow.print();
        printWindow.close();
    }, 500);
});

// ================= Drawing Canvas Logic =================
function openDrawingCanvas() {
    const modal = $('#drawingModal');
    const canvas = $('#drawingCanvas');
    const ctx = canvas.getContext('2d');
    if (!ctx) return;
    
    modal.classList.remove('hidden');

    const rect = canvas.parentElement.getBoundingClientRect();
    canvas.width = rect.width - 32;
    canvas.height = 400;

    let isDrawing = false;
    let lastX = 0, lastY = 0;

    function draw(e) {
        if (!isDrawing) return;
        e.preventDefault();
        const [x, y] = getCoords(e);
        ctx.beginPath();
        ctx.moveTo(lastX, lastY);
        ctx.lineTo(x, y);
        ctx.stroke();
        [lastX, lastY] = [x, y];
    }
    
    function getCoords(e) {
        const rect = canvas.getBoundingClientRect();
        if (e instanceof MouseEvent) {
            return [e.clientX - rect.left, e.clientY - rect.top];
        }
        if (e.touches && e.touches.length > 0) {
            return [e.touches[0].clientX - rect.left, e.touches[0].clientY - rect.top];
        }
        return [0, 0];
    }

    function startDrawing(e) {
        isDrawing = true;
        [lastX, lastY] = getCoords(e);
    }

    canvas.addEventListener('mousedown', startDrawing);
    canvas.addEventListener('mousemove', draw);
    canvas.addEventListener('mouseup', () => isDrawing = false);
    canvas.addEventListener('mouseout', () => isDrawing = false);
    
    canvas.addEventListener('touchstart', startDrawing);
    canvas.addEventListener('touchmove', draw);
    canvas.addEventListener('touchend', () => isDrawing = false);

    ctx.strokeStyle = $('#drawColor').value;
    ctx.lineWidth = parseInt($('#drawSize').value, 10);
    ctx.lineJoin = 'round';
    ctx.lineCap = 'round';
    ctx.globalCompositeOperation = 'source-over';

    $('#drawColor')?.addEventListener('input', e => ctx.strokeStyle = e.target.value);
    $('#drawSize')?.addEventListener('input', e => ctx.lineWidth = parseInt(e.target.value, 10));
    $('#drawPen')?.addEventListener('click', () => ctx.globalCompositeOperation = 'source-over');
    $('#drawEraser')?.addEventListener('click', () => ctx.globalCompositeOperation = 'destination-out');
    $('#drawClear')?.addEventListener('click', () => ctx.clearRect(0, 0, canvas.width, canvas.height));
    
    const saveHandler = () => {
        const dataUrl = canvas.toDataURL('image/png');
        document.execCommand('insertImage', false, dataUrl);
        modal.classList.add('hidden');
        triggerAutosave();
        // Clean up listeners
        $('#drawingSave').removeEventListener('click', saveHandler);
        $('#drawingCancel').removeEventListener('click', cancelHandler);
    };

    const cancelHandler = () => {
        modal.classList.add('hidden');
        // Clean up listeners
        $('#drawingSave').removeEventListener('click', saveHandler);
        $('#drawingCancel').removeEventListener('click', cancelHandler);
    };

    $('#drawingSave')?.addEventListener('click', saveHandler);
    $('#drawingCancel')?.addEventListener('click', cancelHandler);
}

// ================= TTS Player Logic =================
const synth = window.speechSynthesis;
let voices = [];
let currentUtterance = null;
let originalContent = null;

function populateVoiceList() {
    voices = synth.getVoices().filter(v => v.lang.startsWith('en'));
    const voiceSelect = $('#ttsVoice');
    voiceSelect.innerHTML = voices
        .map(voice => `<option value="${voice.name}">${voice.name} (${voice.lang})${voice.default ? ' — Default' : ''}</option>`)
        .join('');
}

function highlightText(text, startIndex, length) {
    const editor = $('#noteContent');
    if (originalContent === null) originalContent = editor.innerHTML;
    
    // Fallback for simple highlighting if regex fails
    const before = text.substring(0, startIndex);
    const word = text.substring(startIndex, startIndex + length);
    const after = text.substring(startIndex + length);

    const highlightedHTML = originalContent.replace(word, `<span class="tts-highlight">${word}</span>`);
    if(editor.innerHTML !== highlightedHTML) {
      editor.innerHTML = highlightedHTML;
    }
}

function removeHighlight() {
    const editor = $('#noteContent');
    if (originalContent !== null) {
      editor.innerHTML = originalContent;
      originalContent = null;
    }
}

function playNoteAudio() {
    const playBtn = $('#ttsPlay');
    if (synth.speaking && synth.paused) {
        synth.resume();
        playBtn.innerHTML = ICONS.pause;
        return;
    }
    if (synth.speaking) {
        synth.pause();
        playBtn.innerHTML = ICONS.play;
        return;
    }

    const tempDiv = document.createElement('div');
    tempDiv.innerHTML = $('#noteContent').innerHTML;
    const textToSpeak = tempDiv.textContent || 'No content to read.';
    
    const voiceSelect = $('#ttsVoice');
    const rateInput = $('#ttsRate');

    currentUtterance = new SpeechSynthesisUtterance(textToSpeak);
    const selectedVoice = voices.find(v => v.name === voiceSelect.value);
    if (selectedVoice) currentUtterance.voice = selectedVoice;
    currentUtterance.rate = parseFloat(rateInput.value);

    currentUtterance.onstart = () => {
        playBtn.innerHTML = ICONS.pause;
        originalContent = $('#noteContent').innerHTML;
    };
    currentUtterance.onend = () => {
        playBtn.innerHTML = ICONS.play;
        removeHighlight();
        currentUtterance = null;
    };
    currentUtterance.onpause = () => playBtn.innerHTML = ICONS.play;
    currentUtterance.onresume = () => playBtn.innerHTML = ICONS.pause;
    currentUtterance.onboundary = (event) => {
        if (event.name === 'word') {
             // Highlighting is complex and can be unreliable.
             // A simpler progress bar might be a better UX.
             // For now, we'll disable live highlighting to prevent bugs.
             // highlightText(textToSpeak, event.charIndex, event.charLength);
        }
    };

    synth.speak(currentUtterance);
}

function stopNoteAudio() {
    if (synth.speaking) {
        synth.cancel(); // onend will fire and handle cleanup
    }
}

$('#ttsPlay')?.addEventListener('click', playNoteAudio);
$('#ttsStop')?.addEventListener('click', stopNoteAudio);
$('#ttsRate')?.addEventListener('input', e => {
    const rate = parseFloat(e.target.value).toFixed(1);
    $('#ttsRateLabel').textContent = `${rate}x`;
    if(currentUtterance) currentUtterance.rate = parseFloat(rate);
});


/* =============== INIT =============== */
buildSearch();
renderDashboard();
renderDateScroller();
renderSubjectTabs();
renderSubjects(DATA[0].code);
renderNotes();
renderImportant();
renderPlannerTool();
renderTopicPicker();
setupEditorToolbar();

if (synth.onvoiceschanged !== undefined) {
    synth.onvoiceschanged = populateVoiceList;
}
populateVoiceList();

$('#noteTitle').addEventListener('input', triggerAutosave);
$('#noteSubtitle').addEventListener('input', triggerAutosave);
$('#noteContent').addEventListener('input', triggerAutosave);
$('#noteKeys').addEventListener('input', triggerAutosave);
</script>
</body>
</html>
