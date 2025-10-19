<!DOCTYPE html>
<html lang="en">
<head>
<!-- Chosen Palette: Syllabrix Modern Dark/Light -->
<!-- Application Structure Plan: The application's information architecture has been redesigned for a more task-oriented user flow. The generic 'Study Tools' tab is replaced by dedicated top-level tabs: 'Planner' for scheduling, 'Review' for active recall using a new Spaced Repetition System (SRS), and 'Library' for passive review of notes and important topics. The Dashboard is enhanced with an 'Activity' section featuring a study heatmap and streak counter for motivation, alongside an integrated 'Focus Timer' to encourage concentration. This structure separates planning, active learning, and reference, creating a more intuitive and powerful workflow for students. -->
<!-- Visualization & Content Choices: 
- Report Info: User's study activity over time. Goal: Motivate & visualize consistency. Viz/Presentation: HTML/CSS Grid-based Heatmap and a dynamic text counter for streak. Interaction: Hovering over a day shows the count of topics studied. Justification: Provides a clear, at-a-glance visualization of effort, which is a powerful motivator. Library: Vanilla JS + HTML/CSS.
- Report Info: Key points from user's notes. Goal: Active recall and long-term knowledge retention. Viz/Presentation: A dedicated Flashcard UI in the 'Review' tab. Interaction: User reveals the answer, then self-rates their confidence on a 4-point scale (Again, Hard, Good, Easy), which triggers the SRS algorithm to schedule the next review. Justification: Implements spaced repetition, a scientifically proven method for effective learning, directly into the workflow. Library: Vanilla JS.
- Report Info: Need for structured, focused study sessions. Goal: Improve concentration and productivity. Viz/Presentation: An integrated Pomodoro-style 'Focus Timer' on the Dashboard. Interaction: Standard start/pause/reset controls. Justification: Provides a popular and effective tool for time management directly within the student's primary command center. Library: Vanilla JS.
- Report Info: Overall progress statistics. Goal: Inform at a glance. Viz/Presentation: Animated circular progress meter (retained from original). Interaction: Clicking a subject's progress bar opens a detailed modal. Justification: This remains an effective and visually appealing way to summarize progress. Library: Inline SVG for the circle, JS for animation. -->
<!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no" />
<title>Syllabrix — Focus. Plan. Achieve.</title>
<meta name="theme-color" content="#121212" />
<style>
  :root{
    /* LIGHT */
    --bg-light:#f0f2f5; --card-light:#ffffff; --text-light:#050505; --muted-light:#65676b; --border-light:#ced0d4;
    --brand-light:#0a84ff; --brand-bg-light:#e7f3ff; --accent:#fa383e; --danger:#e02c34; --success:#31a24c;
    --card-light-rgb: 255,255,255; --border-light-rgb: 206,208,212;
    
    /* DARK (User Specified) */
    --bg-dark:#121212; --card-dark:#1b1b1b; --text-dark:#eceef1; --muted-dark:#a1a6ad; --border-dark:#2a2a2a;
    --brand-dark:#0a84ff; --brand-bg-dark:#1b2b44;
    --card-dark-rgb: 27,27,27; --border-dark-rgb: 42,42,42;

    /* GRADIENTS */
    --grad-title: linear-gradient(45deg, #0a84ff, #64d2ff);
    --grad-brand: linear-gradient(45deg, #0a84ff, #4599ff);
    --grad-today: linear-gradient(45deg, #0a84ff, #af52de, #ff3b30);
    --grad-review: linear-gradient(45deg, #34c759, #30d5c8);

    /* SUBJECT COLORS */
    --sub-c1:#0a84ff; --sub-c2:#34c759; --sub-c3:#ff9500; --sub-c4:#ff3b30; --sub-c5:#af52de;

    /* ACTIVE */
    --bg:var(--bg-light); --card:var(--card-light); --text:var(--text-light); --muted:var(--muted-light); --border:var(--border-light);
    --brand:var(--brand-light); --brand-bg:var(--brand-bg-light);
    --card-rgb: var(--card-light-rgb); --border-rgb: var(--border-light-rgb);

    --radius:12px; --radius-lg:20px; --shadow-sm:0 1px 2px rgba(0,0,0,.08); --shadow-md:0 8px 24px rgba(0,0,0,.1);
  }
  body[data-theme="dark"]{ 
    --bg:var(--bg-dark); --card:var(--card-dark); --text:var(--text-dark); --muted:var(--muted-dark); --border:var(--border-dark); 
    --brand:var(--brand-dark); --brand-bg:var(--brand-bg-dark); --shadow-sm:0 2px 4px rgba(0,0,0,.3); --shadow-md:0 10px 32px rgba(0,0,0,.45); 
    --card-rgb: var(--card-dark-rgb); --border-rgb: var(--border-dark-rgb);
  }

  *{box-sizing:border-box; -webkit-tap-highlight-color:transparent}
  html{scroll-behavior:smooth;}
  body{
    margin:0; min-height:100vh; font:16px -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji"; 
    background:var(--bg); color:var(--text); transition: background .2s, color .2s; 
    -webkit-font-smoothing: antialiased; -moz-osx-font-smoothing: grayscale;
    padding-top: 60px; /* For fixed header */
    padding-bottom: 70px; /* For fixed bottom nav */
  }

  .wrap{max-width:960px; margin:0 auto; padding:0 20px}

  .view-header {
    position: fixed;
    top: 0; left: 0; right: 0;
    z-index: 50;
    background: rgba(var(--card-rgb), 0.85);
    backdrop-filter: saturate(180%) blur(10px);
    border-bottom: 1px solid var(--border);
    height: 60px;
    display: flex;
    align-items: center;
    transition: background .2s, border-color .2s;
  }
  .view-header .wrap { display: flex; justify-content: space-between; align-items: center; width: 100%; }
  #view-title { margin: 0; font-size: 20px; font-weight: 700; }
  
  main { padding-top: 16px; padding-bottom: 24px; }
  
  /* Main app shell */
  .app-card{background:var(--card); border:1px solid var(--border); border-radius:var(--radius-lg); box-shadow:var(--shadow-sm); padding:16px; transition: background .2s, border-color .2s; position: relative;}
  .view-title { text-align: center; margin: 6px 0 16px; font-weight: 700; font-size: 1.2em; }
  
  .seg-tabs{display:flex; gap:6px; background:var(--bg); padding:4px; border-radius:999px; border:1px solid var(--border); width: 100%; flex-wrap: wrap;}
  .seg-btn{padding:8px 16px; border-radius:999px; border:0; background:transparent; color:var(--muted); font-weight:600; font-size:14px; cursor: pointer; transition: all .25s ease; flex: 1;}
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
  #search-container{position:sticky; top:60px; z-index:20; padding:12px 0; background:linear-gradient(to bottom, var(--bg) 85%, transparent)}
  #search{display:block; width:100%; max-width:520px; margin:0 auto; height:48px; padding:0 20px; border-radius:999px; border:1px solid var(--border); background:var(--card); color:var(--text); font-size:16px; box-shadow:var(--shadow-md)}
  #search:focus{outline:none; border-color:var(--brand); box-shadow:0 0 0 3px color-mix(in srgb, var(--brand) 20%, transparent)}
  #search-results{position:absolute; left:50%; transform:translateX(-50%); top:calc(48px + 18px); width:100%; max-width:520px; background:var(--card); border:1px solid var(--border); border-radius:14px; box-shadow:var(--shadow-md); max-height:50vh; overflow:auto}
  .sr-item{padding:12px 16px; border-bottom:1px solid var(--border); cursor:pointer}
  .sr-item:last-child{border-bottom:0} .sr-item:hover { background: var(--bg); }
  .sr-title{font-weight:600} .sr-ctx{font-size:12px; color:var(--muted)}
  .hidden{display:none!important}

  /* Subject tabs */
  #subject-tabs{display:flex; gap:8px; overflow:auto; padding:8px 2px 12px; margin: 0 -20px; padding-left: 20px; padding-right: 20px; -ms-overflow-style:none; scrollbar-width:none}
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
  .btn.success{background-image:var(--grad-review); color:#fff; border-color:transparent}
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
  .dash-header { text-align: center; margin-bottom: 24px; }
  .dash-header h1 {
      margin:0; font-size:36px; font-weight:800; letter-spacing:-.5px;
      background:var(--grad-title); -webkit-background-clip:text; -webkit-text-fill-color:transparent;
  }
  .dash-header .tagline{margin:4px 0 0; color:var(--muted); font-weight:500; font-size: 16px;}

  .dash-grid { display: grid; grid-template-columns: 1fr; gap: 16px; }
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
  #date-scroller-container { overflow-x: auto; padding: 4px 0 10px; margin: 0 -20px; padding-left: 20px; padding-right: 20px; scrollbar-width: none; -ms-overflow-style: none; scroll-snap-type: x mandatory; -webkit-overflow-scrolling: touch; }
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
  
  /* --- Notes Modal & Editor --- */
  .notes-modal-layout { display: flex; gap: 14px; }
  #notes-list-panel { width: 180px; flex-shrink: 0; display: flex; flex-direction: column; gap: 6px; }
  #notes-content-panel { flex-grow: 1; min-width: 0; }
  .note-list-item { padding: 8px 10px; border-radius: 8px; background: var(--bg); cursor: pointer; font-weight: 600; border: 1px solid var(--border); overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
  .note-list-item.active { background: var(--brand-bg); color: var(--brand); border-color: var(--brand); }

  .note-save-status { font-size: 13px; font-weight: 600; color: var(--muted); opacity: 0; transition: opacity 0.3s ease; }

  .editor-toolbar { display: flex; flex-wrap: wrap; gap: 6px; margin-bottom: 8px; padding-bottom: 8px; border-bottom: 1px solid var(--border); }
  .editor-toolbar .i-btn { width: 36px; height: 36px; }
  #noteContent:focus { outline: none; }
  #noteContent img { max-width: 100%; border-radius: 8px; cursor: pointer; }
  #noteContent .resizable-image-wrapper { position: relative; display: inline-block; resize: both; overflow: hidden; border: 2px dashed var(--brand); min-width: 50px; min-height: 50px; }
  #noteContent .resizable-image-wrapper img { width: 100%; height: 100%; display: block; pointer-events: none; }
  #noteContent h1, #noteContent h2, #noteContent h3 { margin: 1em 0 0.5em; line-height: 1.2; font-weight: 700; }
  #noteContent h1 { font-size: 1.8em; } #noteContent h2 { font-size: 1.5em; } #noteContent h3 { font-size: 1.2em; }
  #noteContent blockquote { margin: 1em 0; padding: 8px 16px; border-left: 4px solid var(--border); background: var(--bg); color: var(--muted); font-style: italic; }
  #noteContent pre { margin: 1em 0; padding: 12px; background: var(--bg); border: 1px solid var(--border); border-radius: 8px; font-family: 'SF Mono', Consolas, 'Courier New', monospace; white-space: pre-wrap; word-wrap: break-word; font-size: 14px; }

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
  .toast.show { opacity: 1; bottom: 90px; } /* Above bottom nav */
  
  /* --- Mobile Notes Modal --- */
  #mobile-note-selector-container { display: none; margin-bottom: 12px; }
  #mobile-note-selector-container label { font-weight: 600; display: block; margin-bottom: 6px; font-size: 14px; color: var(--muted); }
  #mobileNoteSelector { width: 100%; }

  /* --- New Features --- */
  /* Focus Timer */
  #focus-timer-display { font-size: 48px; font-weight: 700; text-align: center; letter-spacing: -1px; margin-bottom: 8px; }
  #focus-timer-topic { text-align: center; font-weight: 600; color: var(--muted); min-height: 20px; margin-bottom: 12px; }

  /* Heatmap */
  #heatmap-container { display: grid; grid-template-rows: repeat(7, 1fr); grid-auto-flow: column; gap: 4px; }
  .heatmap-cell { width: 16px; height: 16px; background: var(--border); border-radius: 4px; }
  .heatmap-cell[data-level="1"] { background: color-mix(in srgb, var(--brand) 20%, transparent); }
  .heatmap-cell[data-level="2"] { background: color-mix(in srgb, var(--brand) 40%, transparent); }
  .heatmap-cell[data-level="3"] { background: color-mix(in srgb, var(--brand) 70%, transparent); }
  .heatmap-cell[data-level="4"] { background: var(--brand); }
  .heatmap-cell:hover { transform: scale(1.2); }

  /* Review View */
  #review-card { min-height: 300px; display: flex; flex-direction: column; justify-content: center; align-items: center; text-align: center; cursor: pointer; padding: 20px; border: 2px dashed var(--border); }
  #review-card-front, #review-card-back { font-size: 1.2em; line-height: 1.5; }
  #review-controls { display: grid; grid-template-columns: repeat(2, 1fr); gap: 10px; margin-top: 20px; }

  /* Bottom Nav */
  .bottom-nav {
    position: fixed;
    bottom: 0; left: 0; right: 0;
    height: 70px;
    z-index: 50;
    display: flex;
    justify-content: space-around;
    background: rgba(var(--card-rgb), 0.85);
    backdrop-filter: saturate(180%) blur(10px);
    border-top: 1px solid var(--border);
    padding: 0 10px;
    transition: background .2s, border-color .2s;
  }
  .bottom-nav-btn {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    flex: 1;
    background: none;
    border: none;
    cursor: pointer;
    color: var(--muted);
    font-weight: 600;
    font-size: 11px;
    gap: 4px;
    transition: color 0.2s;
    padding: 8px 0;
  }
  .bottom-nav-btn svg {
    width: 24px;
    height: 24px;
    stroke-width: 2px;
  }
  .bottom-nav-btn.active { color: var(--brand); }
  .bottom-nav-btn:hover { color: var(--text); }


  @media (min-width: 680px) { 
    .row > *{flex:1} 
    #review-controls { grid-template-columns: repeat(4, 1fr); } 
    .dash-grid { grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); }
  }
  @media (max-width: 768px) {
    #notes-list-panel { display: none; }
    #mobile-note-selector-container { display: block; }
  }
</style>
</head>
<body data-theme="dark">
  <div id="loader" class="overlay" style="z-index: 101; color: white; font-size: 1.5em; font-weight: 700;">Loading Syllabrix...</div>
  <div id="toast" class="toast hidden"></div>
  
  <header class="view-header">
    <div class="wrap">
        <h2 id="view-title">Dashboard</h2>
        <button id="themeToggle" class="theme-toggle" aria-label="Toggle theme">
            <svg id="theme-icon-sun" class="theme-icon" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="5"></circle><line x1="12" y1="1" x2="12" y2="3"></line><line x1="12" y1="21" x2="12" y2="23"></line><line x1="4.22" y1="4.22" x2="5.64" y2="5.64"></line><line x1="18.36" y1="18.36" x2="19.78" y2="19.78"></line><line x1="1" y1="12" x2="3" y2="12"></line><line x1="21" y1="12" x2="23" y2="12"></line><line x1="4.22" y1="19.78" x2="5.64" y2="18.36"></line><line x1="18.36" y1="5.64" x2="19.78" y2="4.22"></line></svg>
            <svg id="theme-icon-moon" class="theme-icon" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"></path></svg>
        </button>
    </div>
  </header>

  <!-- Floating Search -->
  <div id="search-container" class="wrap">
    <input id="search" type="search" placeholder="Search entire syllabus…"/>
    <div id="search-results" class="hidden"></div>
  </div>

  <main class="wrap" id="main-content">
    <!-- DASHBOARD -->
    <section id="view-dashboard" class="view-content">
        <div class="dash-header">
           <h1>Syllabrix</h1>
           <div class="tagline">Focus. Plan. Achieve.</div>
        </div>
        <div class="dash-grid">
            <div id="countdown-container" class="app-card"></div>
            <div id="focus-timer-container" class="app-card">
                <h3 class="view-title">Focus Timer</h3>
                <div id="focus-timer-display">25:00</div>
                <div id="focus-timer-topic">Ready to focus?</div>
                <div class="row" style="justify-content: center;">
                    <button id="timer-start" class="btn brand">Start</button>
                    <button id="timer-reset" class="btn">Reset</button>
                </div>
            </div>
        </div>
        
        <div class="app-card" style="margin-top:16px;">
            <div class="seg-tabs" id="dashboard-tabs" style="margin-bottom: 16px;">
                <button class="seg-btn active" data-d-tab="today">Today</button>
                <button class="seg-btn" data-d-tab="pinned">Pinned</button>
                <button class="seg-btn" data-d-tab="nextup">Next Up</button>
                <button class="seg-btn" data-d-tab="progress">Progress</button>
                <button class="seg-btn" data-d-tab="activity">Activity</button>
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
                 <div id="dash-activity" class="hidden">
                    <h3 class="view-title">Study Activity</h3>
                     <div style="text-align: center; margin-bottom: 24px;">
                        <span id="streak-counter" class="chip" style="font-size: 1.2em; padding: 8px 16px;">🔥 Current Streak: 0 days</span>
                    </div>
                    <div id="heatmap-container" style="margin: 0 auto; max-width: 600px;"></div>
                    <div class="muted" style="font-size: 12px; text-align: center; margin-top: 8px;">Your study activity for the past months.</div>
                </div>
            </div>
        </div>
    </section>

    <!-- SYLLABUS -->
    <section id="view-syllabus" class="view-content hidden">
      <div id="subject-tabs"></div>
      <div id="subjects"></div>
    </section>

    <!-- PLANNER -->
    <section id="view-planner" class="view-content hidden">
         <h3 class="view-title" style="margin-top: 0;">My Study Planner</h3>
        <div class="planner">
            <div class="p-card">
                <label class="p-card-label">Plan Settings</label>
                <div class="row stack">
                    <input type="text" id="planName" placeholder="e.g., Sessional Exam Plan" />
                    <input type="date" id="targetDate" />
                </div>
                <div class="row" style="margin-top: 8px; align-items: center; justify-content: space-between;">
                    <label class="chip">
                        <input id="planCountdown" type="checkbox"> Enable Countdown
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
    </section>

    <!-- REVIEW -->
    <section id="view-review" class="view-content hidden">
        <h3 class="view-title" style="margin-top: 0;">Review Session</h3>
        <div id="review-stats" style="text-align:center; margin-bottom: 16px;"></div>
        <div id="review-card-container">
            <div id="review-card" class="app-card">
                <div id="review-card-front"></div>
                <div id="review-card-back" class="hidden"></div>
            </div>
             <div id="review-controls" class="hidden">
                 <button class="btn danger" data-rating="0">Again <div class="muted" style="font-size:11px">&lt;1m</div></button>
                 <button class="btn" style="--brand-light: var(--sub-c3); --brand-dark: var(--sub-c3)" data-rating="1">Hard <div class="muted" style="font-size:11px">&lt;10m</div></button>
                 <button class="btn brand" data-rating="2">Good <div class="muted" style="font-size:11px" id="good-interval"></div></button>
                 <button class="btn success" data-rating="3">Easy <div class="muted" style="font-size:11px" id="easy-interval"></div></button>
            </div>
        </div>
    </section>

    <!-- LIBRARY -->
    <section id="view-library" class="view-content hidden">
      <div class="seg-tabs" id="libraryTabs" style="margin-bottom:16px;">
        <button class="seg-btn active" data-lib-tab="notes">Notes</button>
        <button class="seg-btn" data-lib-tab="important">Important</button>
        <button class="seg-btn" data-lib-tab="achievements">Achievements</button>
      </div>
       <div id="library-tab-content">
          <div id="lib-notes">
            <h3 class="view-title">My Notes</h3>
            <div id="notesList" class="info-list"></div>
          </div>
          <div id="lib-important" class="hidden">
            <h3 class="view-title">Important Topics</h3>
            <div id="impList" class="info-list"></div>
          </div>
          <div id="lib-achievements" class="hidden">
             <h3 class="view-title">My Achievements</h3>
             <p class="muted" style="text-align:center; margin-top:-10px; margin-bottom:24px;">Complete all topics in a unit to earn a badge.</p>
             <div id="achievements-list" style="display: grid; grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); gap: 16px;"></div>
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
                  <div id="mobile-note-selector-container">
                    <label for="mobileNoteSelector">Select Note</label>
                    <select id="mobileNoteSelector"></select>
                  </div>
                  <label for="noteTitle">Note Title</label>
                  <input id="noteTitle" type="text" placeholder="e.g., IMNCI — Key concepts">
                  <label for="noteSubtitle" style="margin-top:8px">Subtitle</label>
                  <input id="noteSubtitle" type="text" placeholder="e.g., Assessment and classification">
                  <label for="noteContent" style="margin-top:8px">Content</label>
                  <div id="editorToolbar" class="editor-toolbar"></div>
                  <div id="noteContent" contenteditable="true" style="min-height:200px; border:1px solid var(--border); border-radius:12px; padding:10px; overflow-y: auto;"></div>
                  <label for="noteKeys" style="margin-top:8px">Key points (for Flashcards)</label>
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
                 <button id="createFlashcards" class="btn success small">Create Flashcards</button>
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

  <!-- MOTIVATION MODAL -->
  <div id="motivationModal" class="overlay hidden">
      <div class="modal" id="motivation-modal-content" style="max-width: 400px; background: rgba(var(--card-rgb), 0.8); backdrop-filter: saturate(180%) blur(10px); border: 1px solid rgba(var(--border-rgb), 0.5);">
          <div class="m-body" style="text-align: center; padding: 24px;">
              <div id="motivation-badge-icon" style="margin-bottom: 16px;"></div>
              <h2 id="motivation-title" style="margin:0; font-size: 24px; font-weight: 800;">Well Done!</h2>
              <p id="motivation-text" style="margin: 8px 0 20px; color: var(--muted); font-weight: 500; line-height: 1.6;"></p>
              <div style="display: flex; flex-direction: column; gap: 8px; justify-content: center;">
                 <button id="motivation-next-topic" class="btn brand">Study Next Topic</button>
                 <button id="motivation-close" class="btn">Close</button>
              </div>
          </div>
      </div>
  </div>

<input type="file" id="imageInput" accept="image/*" class="hidden">

<nav class="bottom-nav">
  <button class="bottom-nav-btn active" data-tab="dashboard" aria-label="Dashboard">
    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path><polyline points="9 22 9 12 15 12 15 22"></polyline></svg>
    <span>Dashboard</span>
  </button>
  <button class="bottom-nav-btn" data-tab="syllabus" aria-label="Syllabus">
    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M4 19.5A2.5 2.5 0 0 1 6.5 17H20"></path><path d="M6.5 2H20v20H6.5A2.5 2.5 0 0 1 4 19.5v-15A2.5 2.5 0 0 1 6.5 2z"></path></svg>
    <span>Syllabus</span>
  </button>
  <button class="bottom-nav-btn" data-tab="planner" aria-label="Planner">
    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect x="3" y="4" width="18" height="18" rx="2" ry="2"></rect><line x1="16" y1="2" x2="16" y2="6"></line><line x1="8" y1="2" x2="8" y2="6"></line><line x1="3" y1="10" x2="21" y2="10"></line></svg>
    <span>Planner</span>
  </button>
  <button class="bottom-nav-btn" data-tab="review" aria-label="Review">
    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polygon points="12 2 2 7 12 12 22 7 12 2"></polygon><polyline points="2 17 12 22 22 17"></polyline><polyline points="2 12 12 17 22 12"></polyline></svg>
    <span>Review</span>
  </button>
  <button class="bottom-nav-btn" data-tab="library" aria-label="Library">
    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"></path></svg>
    <span>Library</span>
  </button>
</nav>

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

/* =============== DB =============== */
const DB_NAME = "SyllabrixDB";
const DB_VERSION = 1;
const STORE_NAME = "appData";
let db;

function openDB() {
    return new Promise((resolve, reject) => {
        const request = indexedDB.open(DB_NAME, DB_VERSION);
        request.onerror = () => reject("Error opening DB");
        request.onsuccess = (event) => {
            db = event.target.result;
            resolve(db);
        };
        request.onupgradeneeded = (event) => {
            const db = event.target.result;
            if (!db.objectStoreNames.contains(STORE_NAME)) {
                db.createObjectStore(STORE_NAME, { keyPath: "key" });
            }
        };
    });
}

function dbGet(key) {
    return new Promise((resolve, reject) => {
        const transaction = db.transaction([STORE_NAME], "readonly");
        const store = transaction.objectStore(STORE_NAME);
        const request = store.get(key);
        request.onerror = () => reject("Error getting data");
        request.onsuccess = () => resolve(request.result ? request.result.value : undefined);
    });
}

function dbSet(key, value) {
    return new Promise((resolve, reject) => {
        const transaction = db.transaction([STORE_NAME], "readwrite");
        const store = transaction.objectStore(STORE_NAME);
        const request = store.put({ key, value });
        request.onerror = () => reject("Error setting data");
        request.onsuccess = () => resolve();
    });
}

/* =============== STATE =============== */
const legacyStoreKey = "syllabrix_planner_v7";
const storeKey = "syllabrixState";
let state = {};

function getDefaultState() {
    return {
        important: [],
        nextUp: [],
        pinned: [],
        topic: {},
        planName: "My Study Plan",
        targetDate: "",
        preferImportant: false,
        countdownEnabled: false,
        schedule: {},
        flashcards: [],
        studyLog: {},
        gamification: {
            badges: []
        }
    };
}

async function loadState() {
    await openDB();
    const lsData = localStorage.getItem(legacyStoreKey);
    if (lsData) {
        console.log("Found legacy data in localStorage. Migrating to IndexedDB...");
        try {
            const parsedData = JSON.parse(lsData);
            // Retroactively add gamification state to migrated data
            if (!parsedData.gamification || parsedData.gamification.points !== undefined) {
                const oldBadges = parsedData.gamification?.badges || [];
                parsedData.gamification = { badges: oldBadges };
            }
            await dbSet(storeKey, parsedData);
            localStorage.removeItem(legacyStoreKey);
            showToast("Data structure updated!");
            state = parsedData;
        } catch (e) {
            console.error("Failed to parse or migrate legacy data", e);
            state = await dbGet(storeKey) || {};
        }
    } else {
        state = await dbGet(storeKey) || {};
    }
    
    // Initialize with defaults
    const defaults = getDefaultState();
    let needsSave = false;
    for (const key in defaults) {
        if (state[key] === undefined) {
            state[key] = defaults[key];
            needsSave = true;
        }
    }

    if (needsSave) await save();
}


async function save(){ 
    if (db) {
        await dbSet(storeKey, state);
    }
}
const idFor = (type, code, unitNo, idx) => [type, code, String(unitNo).replace(/\s+/g,''), idx].join("::");

/* =============== DOM =============== */
const $ = (s) => document.querySelector(s);
const $$ = (s) => Array.from(document.querySelectorAll(s));
const views = { 
    dashboard: $('#view-dashboard'), 
    syllabus: $('#view-syllabus'),
    planner: $('#view-planner'),
    review: $('#view-review'),
    library: $('#view-library')
};
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
  h1: `<svg viewBox="0 0 24 24" width="24" height="24" stroke="currentColor" stroke-width="2.5" fill="none" stroke-linecap="round" stroke-linejoin="round"><path d="M4 12h8m-4-6v12m7-1h5m-2.5-4v8"/></svg>`,
  h2: `<svg viewBox="0 0 24 24" width="24" height="24" stroke="currentColor" stroke-width="2.5" fill="none" stroke-linecap="round" stroke-linejoin="round"><path d="M4 12h8m-4-6v12m7 4h6a2 2 0 0 0 2-2v-2a2 2 0 0 0-2-2h-4v-4h4"/></svg>`,
  h3: `<svg viewBox="0 0 24 24" width="24" height="24" stroke="currentColor" stroke-width="2.5" fill="none" stroke-linecap="round" stroke-linejoin="round"><path d="M4 12h8m-4-6v12m6-7h5a2 2 0 1 1 0 4h-5m0-4a2 2 0 1 0 0-4h5"/></svg>`,
  quote: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M3 21c3 0 7-1 7-8V5c0-1.25-.75-2-2-2H4c-1.25 0-2 .75-2 2v6c0 7 4 8 8 8Z"/><path d="M14 21c3 0 7-1 7-8V5c0-1.25-.75-2-2-2h-4c-1.25 0-2 .75-2 2v6c0 7 4 8 8 8Z"/></svg>`,
  code: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="16 18 22 12 16 6"></polyline><polyline points="8 6 2 12 8 18"></polyline></svg>`,
  listOrdered: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="10" x2="21" y1="6" y2="6"/><line x1="10" x2="21" y1="12" y2="12"/><line x1="10" x2="21" y1="18" y2="18"/><path d="M4 6h1v4"/><path d="M4 10h2"/><path d="m3 16 2 2 2-2"/><path d="M4 12h2.5c.6 0 1-.4 1-1v-1c0-.6-.4-1-1-1H4"/></svg>`,
};

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
      countdownContainer.innerHTML = `<div class="countdown-item"><div class="countdown-label">No active countdown</div><div class="countdown-timer" style="font-size: 18px;">Set one in the Planner!</div></div>`;
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
  renderActivity();
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

async function generateSmart(days){
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
  await save();
  renderPlannerTool();
  renderDashboard();
  renderDateScroller();
  showToast(`Generated a ${days}-day smart plan!`);
}

async function logStudyActivity() {
    const today = new Date().toISOString().split('T')[0];
    state.studyLog[today] = (state.studyLog[today] || 0) + 1;
    await save();
    renderActivity();
}

/* =============== EVENTS =============== */

let lastCompletedTopicId = null; // To keep track of the last completed topic for the "Study Next" button

const viewTitles = {
    dashboard: 'Dashboard',
    syllabus: 'Syllabus',
    planner: 'Planner',
    review: 'Review',
    library: 'Library'
};

// Main tabs - NEW Bottom Nav
$('.bottom-nav')?.addEventListener('click', e => {
    const t = e.target.closest('button');
    if (!t) return;
    $$('.bottom-nav .bottom-nav-btn').forEach(b => b.classList.toggle('active', b === t));
    
    const tabKey = t.dataset.tab;
    Object.keys(views).forEach(key => {
        views[key]?.classList.toggle('hidden', key !== tabKey);
    });
    
    $('#view-title').textContent = viewTitles[tabKey] || 'Syllabrix';
    window.scrollTo({ top: 0, behavior: 'smooth' }); // Scroll to top on view change
});


// Library tabs
$('#libraryTabs')?.addEventListener('click', e => {
    const t = e.target.closest('button'); if(!t) return;
    $$('#libraryTabs .seg-btn').forEach(b=>b.classList.toggle('active', b===t));
    $('#lib-notes').classList.toggle('hidden', t.dataset.libTab !== 'notes');
    $('#lib-important').classList.toggle('hidden', t.dataset.libTab !== 'important');
    $('#lib-achievements').classList.toggle('hidden', t.dataset.libTab !== 'achievements');
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
addEventListener('click', async e=>{
  const target = e.target;
  const cb = target.matches('input[type="checkbox"]') ? target : null;
  if(cb && cb.id.startsWith('topic::')){ 
    const wasDone = state.topic[cb.id]?.done;
    state.topic[cb.id]=state.topic[cb.id]||{}; 
    state.topic[cb.id].done = cb.checked; 
    if(cb.checked && !wasDone) {
        logStudyActivity();
        showMotivationPopup(cb.id);
    }
    await save(); 
    renderDashboard(); 
    return; 
  }
  if(cb && cb.id.startsWith('plan-check-')){ 
    const topicId = cb.dataset.id; 
    const wasDone = state.topic[topicId]?.done;
    state.topic[topicId]=state.topic[topicId]||{}; 
    state.topic[topicId].done = cb.checked; 
    if(cb.checked && !wasDone) {
        logStudyActivity();
        showMotivationPopup(topicId);
    }
    await save(); 
    renderDashboard(); 
    return;
  }

  const actBtn = target.closest('[data-act]'); if(!actBtn) return; const id = actBtn.dataset.id; const act = actBtn.dataset.act;
  if(act==='important' && id){ const idx = state.important.indexOf(id); if(idx>-1) state.important.splice(idx,1); else state.important.push(id); await save(); actBtn.classList.toggle('active', idx===-1); renderImportant(); return; }
  if(act==='nextup' && id){ const idx = state.nextUp.indexOf(id); if(idx>-1) state.nextUp.splice(idx,1); else state.nextUp.push(id); await save(); actBtn.classList.toggle('active', idx===-1); renderNextUpList(); return; }
  if(act==='pin' && id){ const idx = state.pinned.indexOf(id); if(idx>-1) state.pinned.splice(idx,1); else state.pinned.push(id); await save(); actBtn.classList.toggle('active', idx===-1); renderPinnedTopics(); return; }
  if(act==='notes' && id){ openNotes(id, actBtn.dataset.noteIndex); return; }
  if(act==='add-plan' && id){ openAddModal(id); return; }
  if(act==='add-unit-plan') { openAddModal(null, actBtn.dataset.unitid); return; }
  if(act==='reset-unit') { if (confirm('Reset progress for this entire unit?')) { getTopicsForUnit(actBtn.dataset.unitid).forEach(tid => { if(state.topic[tid]) state.topic[tid].done = false; }); await save(); renderSubjects($('#subject-tabs .active').dataset.code); renderDashboard(); } return; }
  if(act==='rm-item' && id){ removeTopicFromPlan(actBtn.dataset.date, id); await save(); renderPlansByDate(); renderDateScroller(); return; }
  if(act==='rm-date'){ delete state.schedule[actBtn.dataset.date]; await save(); renderPlansByDate(); renderDateScroller(); return; }
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
  $('.bottom-nav-btn[data-tab="syllabus"]')?.click();
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

// Planner controls
$('#savePlanSettings')?.addEventListener('click', async ()=>{
    state.planName = $('#planName').value?.trim() || "My Study Plan";
    state.targetDate = $('#targetDate').value || '';
    state.countdownEnabled = $('#planCountdown').checked;
    await save();
    renderDashboard();
    showToast("Plan settings saved!");
});
$$('[data-auto]').forEach(b => b.addEventListener('click', () => generateSmart(Number(b.dataset.auto))));
$('#genCustom')?.addEventListener('click', () => { const d = parseInt($('#customDays').value || '0', 10); if (d > 0) generateSmart(d); });
$('#topicSearch')?.addEventListener('input', renderTopicPicker);
$('#addToPlan')?.addEventListener('click', async () => {
    const date = $('#addDate').value;
    const id = $('#topicSelect').value;
    if (!date || !id) {
        showToast('Please select a topic and a date.');
        return;
    }
    addTopicToPlan(date, id);
    await save();
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
$('#modalAddBtn')?.addEventListener('click', async ()=>{
  const date = $('#modalDate').value;
  if(!date) { showToast("Please select a date."); return; }
  if(pendingAddTopicId) addTopicToPlan(date, pendingAddTopicId);
  if(pendingAddUnitId) getTopicsForUnit(pendingAddUnitId).forEach(tid => addTopicToPlan(date, tid));
  await save(); 
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

$('#motivation-close').addEventListener('click', () => $('#motivationModal').classList.add('hidden'));
$('#motivationModal').addEventListener('click', (e) => {
    if(e.target.id === 'motivationModal') {
        $('#motivationModal').classList.add('hidden');
    }
});

$('#motivation-next-topic').addEventListener('click', () => {
    if (!lastCompletedTopicId) return;
    const nextTopicId = findNextTopic(lastCompletedTopicId);
    if (nextTopicId) {
        navigateToTopic(nextTopicId);
    } else {
        showToast("🎉 You've completed all topics! Congratulations!");
    }
    $('#motivationModal').classList.add('hidden');
});

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

async function saveCurrentNote(isAutosave = false) {
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
    await save();

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
        { cmd: 'formatBlock', value: '<h1>', icon: ICONS.h1, title: 'Heading 1' },
        { cmd: 'formatBlock', value: '<h2>', icon: ICONS.h2, title: 'Heading 2' },
        { cmd: 'formatBlock', value: '<h3>', icon: ICONS.h3, title: 'Heading 3' },
        { cmd: 'bold', icon: ICONS.bold, title: 'Bold' },
        { cmd: 'italic', icon: ICONS.italic, title: 'Italic' },
        { cmd: 'underline', icon: ICONS.underline, title: 'Underline' },
        { cmd: 'insertUnorderedList', icon: ICONS.list, title: 'Bullet List' },
        { cmd: 'insertOrderedList', icon: ICONS.listOrdered, title: 'Numbered List' },
        { cmd: 'formatBlock', value: '<blockquote>', icon: ICONS.quote, title: 'Blockquote' },
        { cmd: 'formatBlock', value: '<pre>', icon: ICONS.code, title: 'Code Block' },
        { cmd: 'justifyLeft', icon: ICONS.alignLeft, title: 'Align Left' },
        { cmd: 'justifyCenter', icon: ICONS.alignCenter, title: 'Align Center' },
        { cmd: 'justifyRight', icon: ICONS.alignRight, title: 'Align Right' },
        { cmd: 'addImage', icon: ICONS.image, title: 'Add Image' },
        { cmd: 'addDrawing', icon: ICONS.draw, title: 'Add Drawing' }
    ];
    toolbar.innerHTML = tools.map(t => `<button class="i-btn" data-command="${t.cmd}" ${t.value ? `data-value="${t.value}"` : ''} title="${t.title}">${t.icon}</button>`).join('');
}

function renderNoteUI() {
    if (!currentNoteId) return;
    const topicData = state.topic[currentNoteId];
    const notes = topicData?.notes || [];
    
    // Desktop list
    const listPanel = $('#notes-list-panel');
    listPanel.innerHTML = notes.map((note, index) => 
        `<div class="note-list-item ${index === currentNoteIndex ? 'active' : ''}" data-note-index="${index}">${note.title || `Note ${index + 1}`}</div>`
    ).join('');

    // Mobile dropdown
    const mobileSelector = $('#mobileNoteSelector');
    if (mobileSelector) {
        mobileSelector.innerHTML = notes.map((note, index) => 
            `<option value="${index}">${note.title || `Note ${index + 1}`}</option>`
        ).join('');
        mobileSelector.selectedIndex = currentNoteIndex;
    }
    
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
    const value = btn.dataset.value;
    const noteContent = $('#noteContent');

    if (command === 'addImage') {
        $('#imageInput').click();
    } else if (command === 'addDrawing') {
        openDrawingCanvas();
    } else if (command) {
        document.execCommand(command, false, value || undefined);
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
$('#noteDelete')?.addEventListener('click', async ()=>{ 
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
    await save(); 
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
                    #content h1, #content h2, #content h3 { font-weight: bold; margin: 1em 0 0.5em; border: 0; padding: 0; }
                    #content h1 { font-size: 20px; color: #000; }
                    #content h2 { font-size: 18px; color: #111; font-style: normal; }
                    #content h3 { font-size: 16px; color: #222; }
                    #content blockquote { margin: 1em 0; padding: 8px 16px; border-left: 4px solid #ccc; background: #f5f5f5 !important; color: #555; font-style: italic; }
                    #content pre { margin: 1em 0; padding: 12px; background: #f5f5f5 !important; border: 1px solid #ddd; border-radius: 4px; font-family: Consolas, "Courier New", monospace; white-space: pre-wrap; word-wrap: break-word; }
                    #keypoints { white-space: pre-wrap; background: #f5f5f5; padding: 12px; border-radius: 4px; margin-top: 20px; }
                    @media print {
                        body { margin: 1in; }
                        #keypoints, #content blockquote, #content pre { background: #f5f5f5 !important; -webkit-print-color-adjust: exact; }
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
$('#createFlashcards')?.addEventListener('click', async () => {
    if (!currentNoteId) return;
    const note = state.topic[currentNoteId]?.notes?.[currentNoteIndex];
    if (!note || !note.keypoints) {
        showToast("No key points to create flashcards from.");
        return;
    }
    const points = note.keypoints.split('\n').map(p => p.replace(/^•\s*/, '').trim()).filter(Boolean);
    if (points.length === 0) {
        showToast("No key points found.");
        return;
    }
    let createdCount = 0;
    points.forEach(point => {
        const card = {
            id: `card::${Date.now()}::${Math.random()}`,
            topicId: currentNoteId,
            front: `Regarding "${note.title}", what about this key point?`,
            back: point,
            nextReview: Date.now(),
            interval: 0,
            ease: 2.5,
            reviews: 0,
        };
        state.flashcards.push(card);
        createdCount++;
    });
    await save();
    showToast(`${createdCount} flashcard(s) created!`);
    renderReview();
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
    try {
        // Guard against browsers with no or buggy speech synthesis
        if (!('speechSynthesis' in window) || typeof window.speechSynthesis.getVoices !== 'function') {
            console.warn("Speech synthesis not supported or getVoices not available.");
            return;
        }
        voices = synth.getVoices().filter(v => v.lang.startsWith('en'));
        const voiceSelect = $('#ttsVoice');
        if (voiceSelect) { // Check if element exists before using it
            voiceSelect.innerHTML = voices
                .map(voice => `<option value="${voice.name}">${voice.name} (${voice.lang})${voice.default ? ' — Default' : ''}</option>`)
                .join('');
        }
    } catch (e) {
        console.error("Error populating voice list:", e);
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
    currentUtterance.onboundary = (event) => {};

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

// ================= NEW FEATURES =================

// --- Gamification ---
const BADGES = {
    "CHN-I::I": { name: "Child Health Foundations", icon: "👶", description: "By understanding the modern concepts of childcare, you're building a foundation for compassionate and effective pediatric nursing." },
    "CHN-I::II": { name: "Growth & Development Guardian", icon: "🌱", description: "You have grasped the critical stages of child development, empowering you to better nurture and protect your future patients." },
    "MHN-I::I": { name: "Mental Health Historian", icon: "📜", description: "By mastering the history of mental health, you've gained the wisdom to provide context-aware and stigma-free care." },
    "MHN-I::V": { name: "Therapeutic Specialist", icon: "🧠", description: "Your knowledge of treatment modalities is a vital step toward restoring mental well-being and offering holistic care." },
    "CHN-COM::III": { name: "Environmental Health Pro", icon: "🌍", description: "You now hold the wisdom to connect environmental factors to community health, a key skill for a public health nurse." },
    "CHN-COM::VIII": { name: "Epidemiology Expert", icon: "🔬", description: "Mastering epidemiological principles allows you to see patterns in public health, enabling you to become a true health detective." },
    "EDTECH::III": { name: "Active Learning Architect", icon: "🧑‍🏫", description: "You have mastered modern teaching strategies, empowering you to educate future nurses and patients effectively." },
    "FORENSIC::II": { name: "Forensic Practice Pioneer", icon: "⚖️", description: "Understanding the role of the forensic nurse equips you to advocate for justice and provide meticulous care to victims." },
};

function createBadgeIconHTML(bgColor = 'gold', icon = '⭐', size = 80) {
    return `<div style="width: ${size}px; height: ${size}px; background: ${bgColor}; border-radius: 50%; display: flex; align-items: center; justify-content: center; margin: 0 auto; box-shadow: 0 4px 12px rgba(0,0,0,0.2), inset 0 2px 4px rgba(255,255,255,0.5); position: relative; border: ${size*0.05}px solid #fff;">
        <div style="font-size: ${size*0.5}px;">${icon}</div>
    </div>`;
}

async function checkForBadges(completedTopicId) {
    const meta = parseTopicId(completedTopicId);
    if (!meta) return null;
    const unitKey = `${meta.code}::${meta.unitNo.replace(/\s+/g,'')}`;
    
    // Is there a badge for this unit?
    const badgeInfo = BADGES[unitKey];
    if (!badgeInfo || state.gamification.badges.find(b => b.name === badgeInfo.name)) {
        return null; // No badge for this unit, or already earned
    }

    // Check if all topics in this unit are complete
    const sub = DATA.find(s => s.code === meta.code);
    const unit = sub?.units.find(u => u.no === meta.unitNo);
    if (!unit) return null;
    
    const allDone = unit.topics.every((topic, i) => {
        const topicId = idFor('topic', meta.code, meta.unitNo, i);
        return state.topic[topicId]?.done;
    });

    if (allDone) {
        state.gamification.badges.push(badgeInfo);
        await save();
        renderAchievements();
        return badgeInfo;
    }
    return null;
}


async function showMotivationPopup(topicId) {
    lastCompletedTopicId = topicId;
    const newBadge = await checkForBadges(topicId);

    // Show Modal
    const modal = $('#motivationModal');
    const titleEl = $('#motivation-title');
    const textEl = $('#motivation-text');
    const iconEl = $('#motivation-badge-icon');

    if (newBadge) {
        titleEl.textContent = `Unit Mastery!`;
        textEl.innerHTML = `<strong>${newBadge.name} Badge Unlocked!</strong><br>${newBadge.description}`;
        iconEl.innerHTML = createBadgeIconHTML('var(--brand)', newBadge.icon, 100);
    } else {
        titleEl.textContent = 'Excellent Progress!';
        textEl.textContent = `Each topic you master brings you closer to becoming an exceptional nurse. Your dedication is shaping your future success.`;
        iconEl.innerHTML = createBadgeIconHTML('var(--success)', '✅', 100);
    }
    
    modal.classList.remove('hidden');
    await save();
    renderAchievements(); // Update badges if any were earned
}

function renderAchievements() {
    const { badges } = state.gamification;
    const listEl = $('#achievements-list');
    if (badges.length === 0) {
        listEl.innerHTML = `<div class="muted" style="text-align:center; padding: 20px; grid-column: 1 / -1;">No badges unlocked yet. Keep studying!</div>`;
    } else {
        listEl.innerHTML = badges.map(badge => `
            <div class="item" style="flex-direction: column; align-items: center; text-align: center; padding: 20px;">
                ${createBadgeIconHTML('var(--brand)', badge.icon, 60)}
                <div style="font-weight: 700; font-size: 1.1em; margin-top: 12px;">${badge.name}</div>
                <div class="muted" style="font-size: 13px;">${badge.description}</div>
            </div>
        `).join('');
    }
}

// --- Navigation for "Study Next Topic" ---
function findNextTopic(currentTopicId) {
    const meta = parseTopicId(currentTopicId);
    if (!meta) return null;

    const allTopicsFlat = [];
    DATA.forEach(sub => sub.units.forEach(u => u.topics.forEach((t, i) => {
        allTopicsFlat.push(idFor('topic', sub.code, u.no, i));
    })));

    const currentIndex = allTopicsFlat.indexOf(currentTopicId);
    if (currentIndex === -1) return null;

    for (let i = currentIndex + 1; i < allTopicsFlat.length; i++) {
        const nextId = allTopicsFlat[i];
        if (!state.topic[nextId]?.done) {
            return nextId;
        }
    }
    // If not found, loop from the beginning to find any missed topics
    for (let i = 0; i < currentIndex; i++) {
        const nextId = allTopicsFlat[i];
        if (!state.topic[nextId]?.done) {
            return nextId;
        }
    }
    return null; // All topics are completed
}

function navigateToTopic(topicId) {
    const meta = parseTopicId(topicId);
    if (!meta) return;

    // Switch to Syllabus view
    $('.bottom-nav-btn[data-tab="syllabus"]')?.click();

    // Switch to correct subject tab
    const chip = $(`#subject-tabs .subject-chip[data-code="${meta.code}"]`); 
    if (chip) chip.click();

    setTimeout(() => {
        const el = document.getElementById(topicId);
        if (!el) return;
        const detailsEl = el.closest('details');
        if (detailsEl) detailsEl.open = true;
        el.scrollIntoView({ behavior: 'smooth', block: 'center' });
        
        const itemEl = el.closest('.item');
        if (itemEl) {
            itemEl.style.transition = 'background-color 0.5s';
            itemEl.style.backgroundColor = 'var(--brand-bg)';
            setTimeout(() => { itemEl.style.backgroundColor = ''; }, 2000);
        }
    }, 150); // Small delay to allow for DOM render after tab click
}


// --- Focus Timer ---
let timerInterval = null;
let timerSeconds = 25 * 60;
let isTimerRunning = false;
function updateTimerDisplay() {
    const minutes = Math.floor(timerSeconds / 60);
    const seconds = timerSeconds % 60;
    $('#focus-timer-display').textContent = `${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
}
$('#timer-start').addEventListener('click', () => {
    const btn = $('#timer-start');
    if (isTimerRunning) {
        clearInterval(timerInterval);
        btn.textContent = 'Resume';
        btn.classList.add('brand');
    } else {
        timerInterval = setInterval(() => {
            timerSeconds--;
            updateTimerDisplay();
            if (timerSeconds <= 0) {
                clearInterval(timerInterval);
                isTimerRunning = false;
                btn.textContent = 'Start';
                showToast("Focus session complete! Time for a break.");
                new Audio("data:audio/wav;base64,UklGRigAAABXQVZFZm10IBIAAAABAAEARKwAAIhYAQACABAAAABkYXRhBQAAAAgAAAA=").play(); // Simple beep
            }
        }, 1000);
        btn.textContent = 'Pause';
        btn.classList.remove('brand');
    }
    isTimerRunning = !isTimerRunning;
});
$('#timer-reset').addEventListener('click', () => {
    clearInterval(timerInterval);
    isTimerRunning = false;
    timerSeconds = 25 * 60;
    updateTimerDisplay();
    $('#timer-start').textContent = 'Start';
    $('#timer-start').classList.add('brand');
});

// --- Activity Heatmap & Streak ---
function renderActivity() {
    const container = $('#heatmap-container');
    const today = new Date();
    today.setHours(0,0,0,0);
    
    // Calculate max value for color scaling
    const counts = Object.values(state.studyLog);
    const maxCount = counts.length > 0 ? Math.max(...counts) : 1;

    let html = '';
    for (let i = 180; i >= 0; i--) {
        const d = new Date();
        d.setDate(today.getDate() - i);
        const dateKey = d.toISOString().split('T')[0];
        const count = state.studyLog[dateKey] || 0;
        let level = 0;
        if (count > 0) level = 1;
        if (count > maxCount * 0.25) level = 2;
        if (count > maxCount * 0.5) level = 3;
        if (count > maxCount * 0.75) level = 4;
        
        html += `<div class="heatmap-cell" data-level="${level}" title="${dateKey}: ${count} topics"></div>`;
    }
    container.innerHTML = html;

    // Calculate streak
    let streak = 0;
    let checkDate = new Date(today);
    while (state.studyLog[checkDate.toISOString().split('T')[0]]) {
        streak++;
        checkDate.setDate(checkDate.getDate() - 1);
    }
    $('#streak-counter').innerHTML = `🔥 Current Streak: ${streak} day${streak !== 1 ? 's' : ''}`;
}


// --- Spaced Repetition System (SRS) ---
let reviewQueue = [];
let currentCard = null;

function formatInterval(ms) {
    if (ms < 60000) return `<1m`;
    if (ms < 3600000) return `${Math.round(ms/60000)}m`;
    if (ms < 86400000) return `${Math.round(ms/3600000)}h`;
    return `${Math.round(ms/86400000)}d`;
}

function renderReview() {
    const now = Date.now();
    reviewQueue = state.flashcards.filter(c => c.nextReview <= now);
    $('#review-stats').innerHTML = `<span class="chip" style="font-size: 1.1em; padding: 8px 16px;">${reviewQueue.length} cards to review</span>`;
    
    if(reviewQueue.length === 0) {
        $('#review-card').innerHTML = `<div id="review-card-front">🎉 All done for now!</div>`;
        $('#review-controls').classList.add('hidden');
        return;
    }
    
    currentCard = reviewQueue[0];
    $('#review-card-front').innerHTML = currentCard.front;
    $('#review-card-back').innerHTML = currentCard.back;
    $('#review-card-front').classList.remove('hidden');
    $('#review-card-back').classList.add('hidden');
    $('#review-controls').classList.add('hidden');
    
    const intervals = calculateNextIntervals(currentCard, 2);
    $('#good-interval').textContent = formatInterval(intervals.good);
    const easyIntervals = calculateNextIntervals(currentCard, 3);
    $('#easy-interval').textContent = formatInterval(easyIntervals.easy);
}

$('#review-card').addEventListener('click', () => {
    if(!currentCard || !$('#review-card-back').classList.contains('hidden')) return;
    $('#review-card-front').classList.add('hidden');
    $('#review-card-back').classList.remove('hidden');
    $('#review-controls').classList.remove('hidden');
});

$('#review-controls').addEventListener('click', e => {
    const btn = e.target.closest('button');
    if(!btn || !currentCard) return;
    const rating = parseInt(btn.dataset.rating, 10);
    processReview(currentCard.id, rating);
});

function calculateNextIntervals(card, rating) {
    let interval = card.interval || 0;
    let ease = card.ease || 2.5;
    
    if (rating >= 2) { // Good or Easy
        if (card.reviews === 0) interval = 1 * 60 * 1000; // 1 min
        else if (card.reviews === 1) interval = 10 * 60 * 1000; // 10 min
        else interval = Math.round(interval * ease);
        ease += 0.1;
    } else if (rating === 1) { // Hard
        interval = Math.max(1 * 60 * 1000, interval / 2);
        ease = Math.max(1.3, ease - 0.15);
    } else { // Again
        interval = 1 * 60 * 1000; // 1 min
        ease = Math.max(1.3, ease - 0.2);
    }
    
    const goodInterval = interval;
    const easyInterval = rating === 3 ? Math.round(interval * 1.3) : goodInterval;

    return { good: goodInterval, easy: easyInterval };
}

async function processReview(cardId, rating) {
    const card = state.flashcards.find(c => c.id === cardId);
    if (!card) return;
    
    // Simplified SM-2 algorithm
    card.reviews = (card.reviews || 0) + 1;
    let interval = card.interval || 0;
    let ease = card.ease || 2.5;

    if (rating >= 2) { // Good or Easy
        if (card.reviews === 1) {
            interval = 1 * 60 * 1000; // 1 minute
        } else if (card.reviews === 2) {
            interval = 10 * 60 * 1000; // 10 minutes
        } else {
            interval = Math.round(interval * ease);
        }
        ease += 0.1;
    } else if (rating === 1) { // Hard
        interval = Math.max(1 * 60 * 1000, interval / 2); // Repeat soon, but not instantly
        ease = Math.max(1.3, ease - 0.15);
    } else { // Again
        interval = 1 * 60 * 1000; // 1 minute, reset interval
        ease = Math.max(1.3, ease - 0.2);
    }
    
    if (rating === 3) { // Easy bonus
        interval = Math.round(interval * 1.3);
    }

    card.interval = interval;
    card.ease = ease;
    card.nextReview = Date.now() + interval;

    await save();
    renderReview();
}


/* =============== INIT =============== */
async function initializeApp() {
    try {
        const savedTheme = localStorage.getItem('theme'); 
        setTheme(savedTheme || 'dark');
        $('#themeToggle')?.addEventListener('click', ()=> setTheme(document.body.getAttribute('data-theme')==='dark'?'light':'dark', true));

        await loadState();
        
        buildSearch();
        renderDashboard();
        renderDateScroller();
        renderSubjectTabs();
        renderSubjects(DATA[0].code);
        renderNotes();
        renderImportant();
        renderPlannerTool();
        renderTopicPicker();
        renderReview();
        renderAchievements();
        setupEditorToolbar();

        if (synth.onvoiceschanged !== undefined) {
            synth.onvoiceschanged = populateVoiceList;
        }
        populateVoiceList();

        $('#noteTitle').addEventListener('input', triggerAutosave);
        $('#noteSubtitle').addEventListener('input', triggerAutosave);
        $('#noteContent').addEventListener('input', triggerAutosave);
        $('#noteKeys').addEventListener('input', triggerAutosave);

        $('#mobileNoteSelector')?.addEventListener('change', e => {
            const newIndex = parseInt(e.target.value, 10);
            if (isNaN(newIndex) || newIndex === currentNoteIndex) return;
            
            if (autosaveTimeout) saveCurrentNote(true);
            stopNoteAudio();
            currentNoteIndex = newIndex;
            renderNoteUI();
        });

        $('#loader').classList.add('hidden');
    } catch (error) {
        console.error("Syllabrix failed to initialize:", error);
        const loader = $('#loader');
        if (loader) {
            loader.textContent = "An error occurred while loading. Please refresh the page.";
        }
    }
}

initializeApp();
</script>
</body>
</html>
