<!DOCTYPE html>
<html lang="cs">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Filmový poradce</title>
    <!-- Tailwind CSS pro stylování a Font Inter -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700;800&display=swap" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css2?family=Mouse+Memoirs&display=swap" rel="stylesheet">
    <style>
        /* --- Styly s atmosférou kina pro poutavý vzhled --- */
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(-10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        @keyframes fadeInUp {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        /* --- Nová animace pro pulzující nadpis --- */
        @keyframes pulse-white {
            0%, 100% {
                text-shadow: 0 0 10px rgba(255, 255, 255, 0.5), 0 0 12px rgba(255, 255, 255, 0.3);
            }
            50% {
                text-shadow: 0 0 20px rgba(255, 255, 255, 0.8), 0 0 25px rgba(255, 255, 255, 0.5);
            }
        }

        body {
            font-family: 'Inter', sans-serif;
            background: linear-gradient(170deg, #1e1b4b 0%, #020617 100%);
            color: #d1d5db; /* Světle šedý text pro lepší čitelnost */
            min-height: 100vh;
        }
        
        .container {
            animation: fadeIn 0.8s ease-out forwards;
        }

        .card {
            background-color: rgba(30, 41, 59, 0.7); /* Tmavě břidlicová s průhledností */
            border-radius: 1.5rem;
            padding: 2rem;
            box-shadow: 0 0 30px rgba(0, 0, 0, 0.5);
            border: 1px solid rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
        }
        
        #user-input {
            background-color: #1e293b;
            color: #e2e8f0;
            border-color: #334155;
        }
        #user-input::placeholder {
            color: #64748b;
        }
        #user-input:focus {
            background-color: #0f172a;
            border-color: #3b82f6;
        }

        .btn {
            background-color: #3b82f6; /* Jasnější modrá pro kontrast */
            color: white;
            padding: 1rem 2rem;
            border-radius: 9999px;
            font-weight: bold;
            text-align: center;
            transition: all 0.3s ease-in-out;
            box-shadow: 0 4px 15px -5px rgba(59, 130, 246, 0.6);
        }
        .btn:hover:not(:disabled) {
            background-color: #60a5fa;
            transform: scale(1.05);
            box-shadow: 0 6px 20px -5px rgba(96, 165, 250, 0.7);
        }
        .btn:disabled {
            background-color: #334155;
            cursor: not-allowed;
            opacity: 0.6;
        }
        
        .action-row-btn {
            padding: 0.75rem 1rem;
            font-size: 0.875rem;
            white-space: nowrap;
        }
        
        .random-btn {
            background-color: #1e293b;
            color: #94a3b8;
            border: 1px solid #334155;
        }
        .random-btn:hover:not(:disabled) {
            background-color: #334155;
            color: #e2e8f0;
            border-color: #475569;
        }
        
        /* Styl pro tlačítka žebříčků */
        .rankings-btn {
            background-color: #1e293b;
            color: #94a3b8;
            border: 1px solid #334155;
            padding: 0.5rem 1rem;
            border-radius: 0.5rem;
            transition: background-color 0.3s ease;
        }
        .rankings-btn.active {
            background-color: #3b82f6;
            color: white;
            border-color: #3b82f6;
        }
        .rankings-btn:hover:not(.active) {
            background-color: #334155;
        }

        /* Vylepšená animace pro sekci žebříčků */
        #rankings-options {
            transition: opacity 0.3s ease, transform 0.3s ease, max-height 0.5s ease;
            transform: translateY(-10px);
            max-height: 0;
            opacity: 0;
            overflow: hidden;
        }
        #rankings-options.show-rankings {
            transform: translateY(0);
            max-height: 500px;
            opacity: 1;
            margin-top: 1rem;
        }

        .item-card {
            background-color: #1e293b;
            border-radius: 1rem;
            padding: 1.5rem;
            border: 1px solid #334155;
            transition: transform 0.3s ease-in-out, box-shadow 0.3s ease-in-out, border-color 0.3s ease-in-out;
            opacity: 0;
            animation: fadeInUp 0.5s ease-out forwards;
        }
        .item-card:hover {
            transform: translateY(-8px) scale(1.02);
            box-shadow: 0 10px 20px rgba(0,0,0,0.3);
            border-color: #3b82f6;
        }
        
        /* --- Upravený styl pro nadpis h1 --- */
        h1 {
            font-family: 'Mouse Memoirs', 'Inter', sans-serif; /* Nové písmo s fallbackem */
            color: #ffffff;
            animation: pulse-white 15s ease-in-out infinite; /* Aplikace pulzující animace */
        }
        
        h2 {
           color: #f1f5f9;
        }
        
        .item-card .item-title {
           color: #f1f5f9;
           cursor: pointer;
           transition: color 0.2s ease-in-out;
        }
        .item-card .item-title:hover {
            color: #60a5fa;
        }

        .message-box {
            position: fixed;
            bottom: 2rem;
            left: 50%;
            transform: translateX(-50%);
            padding: 1rem 2rem;
            background-color: #16a34a;
            color: white;
            border-radius: 0.75rem;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            opacity: 0;
            transition: opacity 0.5s ease-in-out, transform 0.5s ease-in-out;
            z-index: 1000;
        }
        .message-box.show {
            opacity: 1;
            transform: translateX(-50%) translateY(0);
        }

        .pagination-btn {
            background-color: #334155;
            color: white;
            padding: 0.5rem 1rem;
            border-radius: 0.5rem;
            font-weight: bold;
            transition: background-color 0.3s ease-in-out;
        }
        .pagination-btn:hover:not(:disabled) {
            background-color: #475569;
        }
        .pagination-btn:disabled {
            background-color: #1e293b;
            color: #64748b;
            cursor: not-allowed;
        }
        
        .read-more-btn {
            color: #f472b6; /* Změněno na růžovou pro odlišení */
            background: none;
            border: none;
            padding: 0;
            cursor: pointer;
            font-weight: bold;
            transition: color 0.2s ease;
            display: inline-block;
            margin-left: 4px;
        }
        .read-more-btn:hover {
            color: #fb7185; /* Světlejší růžová */
            text-decoration: underline;
        }
        
        .action-btn {
            background-color: transparent;
            border: 1px solid;
            border-radius: 9999px;
            padding: 0.3rem 0.8rem;
            font-weight: bold;
            transition: all 0.2s ease;
            font-size: 0.8rem;
        }
         .action-btn:disabled {
            border-color: #475569;
            color: #475569;
            cursor: not-allowed;
            opacity: 0.5;
        }
        .action-btn:disabled:hover {
            background-color: transparent;
        }
        
        .trailer-btn { border-color: #3b82f6; color: #3b82f6; }
        .trailer-btn:hover:not(:disabled) { background-color: #3b82f6; color: white; }
        .trailer-btn-disabled { border-color: #475569; color: #475569; cursor: not-allowed; }
        
        .ai-review-btn { border-color: #a78bfa; color: #a78bfa; }
        .ai-review-btn:hover:not(:disabled) { background-color: #a78bfa; color: white; }

        .news-btn { border-color: #22d3ee; color: #22d3ee; }
        .news-btn:hover:not(:disabled) { background-color: #22d3ee; color: white; }
        
        .trivia-btn { border-color: #facc15; color: #facc15; }
        .trivia-btn:hover:not(:disabled) { background-color: #facc15; color: white; }
        
        .next-reco-btn { border-color: #06b6d4; color: #06b6d4; }
        .next-reco-btn:hover:not(:disabled) { background-color: #06b6d4; color: white; }

        .reco-item {
            cursor: pointer;
            transition: background-color 0.2s ease;
        }
        .reco-item:hover {
            background-color: rgba(71, 85, 105, 0.4);
        }
        
        .person-card {
            background-color: #1e293b;
            border-radius: 1rem;
            padding: 1.5rem;
            border: 1px solid #334155;
            transition: transform 0.3s ease-in-out, box-shadow 0.3s ease-in-out;
            cursor: pointer;
            text-align: center;
        }
        .person-card:hover {
            transform: translateY(-8px) scale(1.02);
            box-shadow: 0 10px 20px rgba(0,0,0,0.3);
            border-color: #3b82f6;
        }

        .modal { transition: opacity 0.3s ease; }
        .modal-content { max-height: 80vh; overflow-y: auto; }
        .modal-content::-webkit-scrollbar { width: 8px; }
        .modal-content::-webkit-scrollbar-track { background: #1e293b; }
        .modal-content::-webkit-scrollbar-thumb { background: #475569; border-radius: 4px;}
        
        .aspect-ratio-16-9 { position: relative; width: 100%; padding-bottom: 56.25%; }
        .aspect-ratio-16-9 iframe { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }
        
        .review-content, .web-content, .reco-content {
            background-color: #334155; padding: 1rem; border-radius: 0.5rem;
            white-space: pre-wrap; word-wrap: break-word; animation: fadeIn 0.5s ease-out;
        }
        .review-content { border-left: 4px solid #a78bfa; }
        .web-content { border-left: 4px solid #22d3ee; }
        .reco-content { border-left: 4px solid #06b6d4; }
        .cast-container { display: flex; overflow-x: auto; gap: 1rem; padding-bottom: 1rem; }
        
        .suggestion-item { padding: 0.75rem 1.5rem; cursor: pointer; transition: background-color 0.2s ease-in-out; }
        .suggestion-item:hover { background-color: #334155; }
        .search-type-btn { color: #94a3b8; }
        .search-type-btn.active { background-color: #2dd4bf; color: white; } /* Změněno na tyrkysovou */
        
        /* --- Styly pro Kvíz "Milionář" --- */
        #trivia-modal .modal-content { max-height: 90vh; }
        #trivia-question { min-height: 84px; }
        #trivia-feedback { min-height: 24px; margin-top: 1rem; }
        .trivia-answer-btn {
            background-color: #1e293b;
            border: 2px solid #334155;
            color: #e2e8f0;
            width: 100%;
            text-align: left;
            padding: 1rem;
            border-radius: 0.75rem;
            transition: all 0.2s ease;
            font-weight: bold;
            position: relative;
        }
        .trivia-answer-btn .audience-bar {
            position: absolute;
            left: 0; top: 0; bottom: 0;
            background-color: rgba(59, 130, 246, 0.5);
            width: 0;
            transition: width 0.5s ease-out;
            border-radius: 0.5rem;
        }
        .trivia-answer-btn .answer-text {
            position: relative; z-index: 1;
        }
        .trivia-answer-btn:not(:disabled):hover {
            background-color: #334155;
            border-color: #60a5fa;
        }
        .trivia-answer-btn.selected {
            background-color: #f59e0b;
            border-color: #facc15;
            animation: pulse 1s infinite;
        }
        @keyframes pulse { 0%, 100% { transform: scale(1); } 50% { transform: scale(1.02); } }
        .trivia-answer-btn.correct {
            background-color: #16a34a;
            border-color: #22c55e;
            color: white;
            animation: none;
        }
        .trivia-answer-btn.incorrect {
            background-color: #dc2626;
            border-color: #ef4444;
            color: white;
            animation: none;
        }
        .trivia-answer-btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }
        
        #trivia-prize-ladder { text-align: center; }
        .prize-level {
            padding: 0.25rem 0.5rem;
            border-radius: 0.25rem;
            margin: 0.1rem auto;
            transition: all 0.3s ease;
            color: #64748b;
        }
        .prize-level.current {
            background-color: #f59e0b;
            color: #1e293b;
            font-weight: bold;
            transform: scale(1.1);
        }
        .prize-level.guarantee { font-weight: bold; color: #e2e8f0; border: 1px solid #475569; }


        /* --- Styly pro Chatbota --- */
        #chat-toggle-btn {
            position: fixed;
            bottom: 3rem; /* Zvýšeno z 2rem */
            right: 2rem;
            background-color: #3b82f6;
            color: white;
            width: 60px;
            height: 60px;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            box-shadow: 0 4px 20px rgba(59, 130, 246, 0.5);
            transition: transform 0.3s ease, background-color 0.3s ease;
            z-index: 1000;
        }
        #chat-toggle-btn:hover {
            transform: scale(1.1);
            background-color: #60a5fa;
        }

        #chat-widget {
            position: fixed;
            bottom: 7.5rem; /* Zvýšeno z 6rem */
            right: 2rem;
            width: 90%;
            max-width: 400px;
            height: 500px;
            background-color: #0f172a;
            border: 1px solid #334155;
            border-radius: 1rem;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
            display: flex;
            flex-direction: column;
            overflow: hidden;
            transform-origin: bottom right;
            transition: transform 0.3s ease-out, opacity 0.3s ease-out;
            z-index: 999;
        }

        #chat-widget.hidden {
             transform: scale(0);
             opacity: 0;
        }

        #chat-header {
            background-color: #1e293b;
            padding: 1rem;
            font-weight: bold;
            text-align: center;
            color: white;
            border-bottom: 1px solid #334155;
        }

        #chat-messages {
            flex-grow: 1;
            padding: 1rem;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 0.75rem;
        }
        
        .user-message, .bot-message {
            padding: 0.75rem 1rem;
            border-radius: 1rem;
            max-width: 80%;
            word-wrap: break-word;
        }
        
        .user-message {
            background-color: #3b82f6;
            color: white;
            align-self: flex-end;
            border-bottom-right-radius: 0.25rem;
        }
        
        .bot-message {
            background-color: #334155;
            color: #e2e8f0;
            align-self: flex-start;
            border-bottom-left-radius: 0.25rem;
        }

        .thinking-indicator {
            align-self: flex-start;
            display: flex;
            gap: 4px;
        }
        .thinking-indicator span {
            width: 8px; height: 8px; background-color: #475569; border-radius: 50%;
            animation: bounce 1.4s infinite ease-in-out both;
        }
        .thinking-indicator span:nth-child(1) { animation-delay: -0.32s; }
        .thinking-indicator span:nth-child(2) { animation-delay: -0.16s; }
        @keyframes bounce {
          0%, 80%, 100% { transform: scale(0); }
          40% { transform: scale(1.0); }
        }

        #chat-form {
            display: flex;
            padding: 1rem;
            border-top: 1px solid #334155;
            gap: 0.5rem;
        }

        #chat-input {
            flex-grow: 1;
            background-color: #1e293b;
            border: 1px solid #334155;
            border-radius: 9999px;
            padding: 0.75rem 1rem;
            color: #e2e8f0;
            outline: none;
        }
        #chat-input:focus {
            border-color: #3b82f6;
        }

        #chat-send-btn {
            background-color: #3b82f6;
            border: none;
            color: white;
            width: 44px;
            height: 44px;
            border-radius: 50%;
            flex-shrink: 0;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: background-color 0.2s;
        }
        #chat-send-btn:hover { background-color: #60a5fa; }
    </style>
</head>
<body class="p-4 sm:p-8">

<!-- Hlavička s pozadím -->
<div class="relative h-56 md:h-72" style="background-image: linear-gradient(to right, #0c0a24, transparent 30%, transparent 70%, #0c0a24), url('https://i.postimg.cc/bvKrfwBB/8273ca67037b5d45e1dae5fd38ae6b09.jpg'); background-size: contain; background-position: center; background-repeat: no-repeat;">
    <div class="absolute inset-0 bg-gradient-to-t from-[#020617] via-[#1e1b4b]/80 to-transparent"></div>
    <div class="container relative max-w-5xl mx-auto px-4 h-full flex flex-col justify-center items-center text-center pt-16">
        <h1 class="text-4xl sm:text-5xl md:text-6xl font-bold text-white mb-4 tracking-wider">
            Filmový a seriálový poradce
        </h1>
        <p class="text-sm sm:text-base text-gray-300 max-w-2xl mx-auto">
            Popište svou náladu, zadejte název, nebo si promluvte s naším AI filmovým expertem a objevujte svět filmů a seriálů.
        </p>
    </div>
</div>

<!-- Hlavní obsah -->
<div class="container max-w-5xl mx-auto px-4 mt-4 md:-mt-10">
    <div class="card space-y-6">
        <!-- Přepínač typů vyhledávání -->
        <div class="flex justify-center mb-4">
            <div class="bg-slate-800 rounded-lg sm:rounded-full p-1 flex flex-wrap justify-center gap-1">
                <button id="search-type-movie" class="px-4 py-2 rounded-full text-sm font-bold transition-colors search-type-btn active">Filmy</button>
                <button id="search-type-tv" class="px-4 py-2 rounded-full text-sm font-bold transition-colors search-type-btn">Seriály</button>
                <button id="search-type-person" class="px-4 py-2 rounded-full text-sm font-bold transition-colors search-type-btn">Herci a herečky</button>
                <button id="show-rankings-btn" class="px-4 py-2 rounded-full text-sm font-bold transition-colors search-type-btn">🏆 Žebříčky</button>
            </div>
        </div>

        <!-- Formulář pro vyhledávání -->
        <div class="relative">
            <div class="flex flex-col sm:flex-row space-y-4 sm:space-y-0 sm:space-x-4">
                <input type="text" id="user-input" placeholder="Hledejte filmy, nebo popište náladu..." class="flex-1 p-3 border rounded-full focus:outline-none focus:ring-2 focus:ring-blue-500 w-full" autocomplete="off">
                <button id="send-btn" class="btn">Vyhledat</button>
            </div>
            <div id="autocomplete-suggestions" class="absolute top-full left-0 right-0 bg-slate-800 border border-slate-700 rounded-b-lg mt-1 z-10 hidden shadow-lg overflow-hidden"></div>
        </div>
        
        <!-- Akční tlačítka -->
        <div class="flex flex-col sm:flex-row justify-center items-center sm:items-stretch space-y-2 sm:space-y-0 sm:space-x-4">
             <button id="random-tip-btn" class="btn random-btn w-full max-w-sm sm:w-auto sm:flex-1 action-row-btn">🎲 Náhodný tip</button>
             <button id="movie-night-btn" class="btn w-full max-w-sm sm:w-auto sm:flex-1 action-row-btn" style="background-color: #8b5cf6;">✨ Vytvořit filmový večer</button>
             <button id="trivia-btn-main" class="btn w-full max-w-sm sm:w-auto sm:flex-1 action-row-btn" style="background-color: #f59e0b;">🎬 Kvíz</button>
        </div>
        
        <!-- Možnosti žebříčků -->
        <div id="rankings-options" class="flex-wrap justify-center gap-2">
            <button class="rankings-btn" data-ranking="popular_movies">Nejoblíbenější filmy</button>
            <button class="rankings-btn" data-ranking="top_rated_movies">Nejlépe hodnocené filmy</button>
            <button class="rankings-btn" data-ranking="now_playing_movies">Aktuálně v kinech</button>
            <button class="rankings-btn" data-ranking="popular_tv">Nejoblíbenější seriály</button>
            <button class="rankings-btn" data-ranking="top_rated_tv">Nejlépe hodnocené seriály</button>
        </div>

        <!-- Indikátor načítání -->
        <div id="loading" class="text-center text-blue-400 font-bold hidden">
            <svg class="animate-spin h-8 w-8 text-blue-400 mx-auto" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
            <p class="mt-2">Hledám doporučení...</p>
        </div>
        
        <!-- Zobrazení výsledků -->
        <div id="error-message" class="text-center text-red-400 font-bold hidden"></div>
        <div id="results" class="space-y-6 mt-8 hidden">
            <h2 id="results-title" class="text-2xl font-bold">Naše doporučení pro vás:</h2>
            <div id="item-list" class="space-y-4"></div>
            <!-- Stránkování -->
            <div id="pagination" class="flex justify-center items-center space-x-4 mt-6 hidden">
                <button id="prev-btn" class="pagination-btn">Předchozí</button>
                <span id="page-info" class="text-gray-400 font-semibold"></span>
                <button id="next-btn" class="pagination-btn">Další</button>
            </div>
        </div>
    </div>
</div>

<!-- Patička -->
<footer class="text-center py-8 text-gray-500 text-sm">
    <a href="https://www.themoviedb.org/" target="_blank" rel="noopener noreferrer">
        <img src="https://www.themoviedb.org/assets/2/v4/logos/v2/blue_long_2-9665a76b1ae401a510ec1e0ca40ddcb3b0cfe45f1d51b77a308fea0845885648.svg" alt="The Movie Database" class="h-6 mx-auto mb-2 opacity-50 hover:opacity-100 transition-opacity">
    </a>
    <p>Tento produkt využívá TMDb API, ale není podporován ani certifikován TMDb.</p>
</footer>

<!-- Prvky pro Chatbota -->
<div id="chat-toggle-btn">
    <svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8" viewBox="0 0 20 20" fill="currentColor">
        <path fill-rule="evenodd" d="M18 5v8a2 2 0 01-2 2h-5l-5 4v-4H4a2 2 0 01-2-2V5a2 2 0 012-2h12a2 2 0 012 2zM7 8H5v2h2V8zm2 0h2v2H9V8zm6 0h-2v2h2V8z" clip-rule="evenodd" />
    </svg>
</div>

<div id="chat-widget" class="hidden">
    <div id="chat-header">Filmový Bot "Klapka"</div>
    <div id="chat-messages"></div>
    <form id="chat-form">
        <input type="text" id="chat-input" placeholder="Zeptejte se na film..." autocomplete="off">
        <button id="chat-send-btn" type="submit">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" viewBox="0 0 20 20" fill="currentColor">
                <path d="M10.894 2.553a1 1 0 00-1.788 0l-7 14a1 1 0 001.169 1.409l5-1.429A1 1 0 009 15.571V11a1 1 0 112 0v4.571a1 1 0 00.725.962l5 1.428a1 1 0 001.17-1.408l-7-14z" />
            </svg>
        </button>
    </form>
</div>

<!-- Modální okna -->
<div id="message-box" class="message-box">Text byl zkopírován do schránky!</div>
<div id="trailer-modal" class="modal fixed inset-0 bg-black bg-opacity-80 flex justify-center items-center hidden z-50 p-4">
    <div class="bg-black p-2 rounded-lg shadow-xl relative w-full max-w-4xl">
        <button id="close-trailer-modal-btn" class="absolute -top-4 -right-2 bg-white text-black rounded-full h-8 w-8 flex justify-center items-center font-bold text-xl z-10">&times;</button>
        <div class="aspect-ratio-16-9">
            <iframe id="trailer-iframe" src="" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
        </div>
    </div>
</div>
<div id="ai-web-modal" class="modal fixed inset-0 bg-black bg-opacity-80 flex justify-center items-center hidden z-50 p-4">
    <div class="bg-slate-800 p-6 rounded-lg shadow-xl relative w-full max-w-2xl border border-slate-700">
        <button id="close-ai-web-modal-btn" class="absolute top-2 right-2 text-slate-400 hover:text-white">&times;</button>
        <h3 id="ai-web-modal-title" class="text-2xl font-bold mb-4 text-white">Načítám...</h3>
        <div id="ai-web-modal-content" class="modal-content text-slate-300"></div>
    </div>
</div>
<div id="next-reco-modal" class="modal fixed inset-0 bg-black bg-opacity-80 flex justify-center items-center hidden z-50 p-4">
    <div class="bg-slate-800 p-6 rounded-lg shadow-xl relative w-full max-w-2xl border border-slate-700">
        <button id="close-next-reco-modal-btn" class="absolute top-2 right-2 text-slate-400 hover:text-white">&times;</button>
        <h3 id="next-reco-modal-title" class="text-2xl font-bold mb-4 text-white">Doporučení pro vás...</h3>
        <div id="next-reco-modal-content" class="modal-content text-slate-300"></div>
    </div>
</div>
<div id="item-detail-modal" class="modal fixed inset-0 bg-black bg-opacity-90 hidden z-50 p-4">
    <div id="item-detail-content" class="modal-content bg-slate-900 rounded-lg shadow-xl relative w-full max-w-4xl mx-auto border border-slate-700"></div>
</div>
<div id="movie-night-modal" class="modal fixed inset-0 bg-black bg-opacity-80 flex justify-center items-center hidden z-50 p-4">
    <div class="bg-slate-800 p-6 rounded-lg shadow-xl relative w-full max-w-2xl border border-slate-700">
        <button id="close-movie-night-modal-btn" class="absolute top-2 right-2 text-slate-400 hover:text-white">&times;</button>
        <h3 class="text-2xl font-bold mb-4 text-white">✨ Vytvořit filmový večer</h3>
        <p class="text-slate-400 mb-4">Zadejte téma (např. "Strašidelný Halloween" nebo "Romantický večer") a já vám připravím seznam filmů.</p>
        <div class="flex space-x-2">
            <input type="text" id="movie-night-input" placeholder="Zadejte téma..." class="flex-1 p-3 border rounded-full focus:outline-none focus:ring-2 focus:ring-purple-500 w-full bg-slate-700 text-white border-slate-600">
            <button id="generate-movie-night-btn" class="btn" style="background-color: #8b5cf6;">Generovat</button>
        </div>
        <div id="movie-night-content" class="modal-content text-slate-300 mt-4"></div>
    </div>
</div>
<div id="trivia-modal" class="modal fixed inset-0 bg-black bg-opacity-90 flex justify-center items-start pt-8 sm:pt-12 hidden z-50 p-4">
    <div class="bg-slate-800 p-6 rounded-lg shadow-xl relative w-full max-w-4xl border border-slate-700 bg-opacity-80 backdrop-blur-sm">
        <button id="close-trivia-modal-btn" class="absolute top-2 right-2 text-slate-400 hover:text-white">&times;</button>
        <h3 class="text-3xl font-bold mb-4 text-white text-center">🎬 Kdo chce být filmovým milionářem?</h3>
        <div id="trivia-content" class="modal-content text-slate-300 mt-4">
            <div id="trivia-start-screen" class="text-center">
                 <p class="mb-6 text-lg">Odpovězte na 15 otázek a staňte se milionářem! Po 5. a 10. otázce máte zaručenou výhru.</p>
                 <button id="start-trivia-btn" class="font-bold text-white transition-colors py-3 px-10 rounded-full text-lg hover:bg-amber-600" style="background-color: #f59e0b;">Spustit hru</button>
            </div>
             <div id="trivia-end-screen" class="hidden text-center p-8">
                <h4 id="trivia-end-title" class="text-4xl font-bold text-amber-400 mb-4">Konec hry!</h4>
                <p id="trivia-end-message" class="text-xl mb-6"></p>
                <button id="restart-trivia-btn" class="font-bold text-white transition-colors py-3 px-10 rounded-full text-lg hover:bg-amber-600" style="background-color: #f59e0b;">Hrát znovu</button>
            </div>
            <div id="trivia-game-screen" class="hidden">
                 <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                    <div class="md:col-span-2">
                        <h4 class="text-lg font-bold text-center mb-2 mt-8">Otázka <span id="trivia-question-number">1</span> z 15</h4>
                        <div id="trivia-question" class="text-2xl mb-6 text-center font-semibold flex items-center justify-center bg-slate-900 p-6 rounded-lg"></div>
                        <div id="trivia-answers" class="grid grid-cols-1 md:grid-cols-2 gap-4"></div>
                        <div id="trivia-feedback" class="mt-4 text-center font-bold text-xl"></div>
                    </div>
                    <div id="trivia-prize-ladder" class="bg-slate-900 p-4 rounded-lg">
                        <h4 class="text-lg font-bold text-amber-400 mb-2">Výhry</h4>
                        <!-- Pyramida výher se vygeneruje zde -->
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>


<script>
    // --- Odkazy na DOM prvky (deklarace) ---
    let userInput, sendButton, randomTipButton, loadingMessage, errorMessage,
        resultsDiv, resultsTitle, itemListDiv, messageBox, paginationDiv,
        prevButton, nextButton, pageInfo, autocompleteContainer,
        searchTypeMovieButton, searchTypeTvButton, searchTypePersonButton, showRankingsButton,
        rankingsOptionsDiv, trailerModal, closeTrailerModalButton, trailerIframe,
        aiWebModal, closeAiWebModalButton, aiWebModalTitle, aiWebModalContent,
        nextRecoModal, closeNextRecoModalButton, nextRecoModalTitle,
        nextRecoModalContent, itemDetailModal, itemDetailContent,
        chatToggleButton, chatWidget, chatMessagesContainer, chatForm, chatInput,
        movieNightButton, movieNightModal, closeMovieNightModalButton, movieNightInput, generateMovieNightButton, movieNightContent,
        triviaMainButton, triviaModal, closeTriviaModalButton, triviaStartScreen, triviaGameScreen, startTriviaButton, triviaQuestionEl, triviaAnswersEl, triviaFeedbackEl,
        triviaEndScreen, triviaEndTitle, triviaEndMessage, restartTriviaButton, triviaQuestionNumberEl, triviaPrizeLadderEl;
    
    // --- Konfigurace API ---
    const GEMINI_MODEL = 'gemini-2.5-flash-preview-05-20';
    const BASE_IMAGE_URL = "https://image.tmdb.org/t/p/w500";
    const BASE_BACKDROP_URL = "https://image.tmdb.org/t/p/w1280";
    const CLOUDFLARE_WORKER_URL = "https://filmorex-smoke-b601.lukassporka.workers.dev"; // <-- Zkontrolujte, že adresa odpovídá vašemu workeru

    // --- Bezpečnostní funkce pro volání API přes Cloudflare Worker ---
    async function secureFetch(url, options = {}) {
        // Vytvoříme novou URL, abychom z ní mohli bezpečně odstranit případné zapomenuté API klíče.
        // Worker se postará o jejich správné doplnění na serveru.
        const urlObj = new URL(url);
        urlObj.searchParams.delete('api_key');
        urlObj.searchParams.delete('key');
        
        const cleanUrl = urlObj.toString();

        // Připravíme hlavičky pro náš worker. Nejdůležitější je 'X-Target-URL'.
        const headers = {
            ...(options.headers || {}),
            'X-Target-URL': cleanUrl
        };
        
        const cleanOptions = { ...options, headers };

        // Voláme naši bezpečnou bránu (worker) místo přímého volání na API.
        return fetch(CLOUDFLARE_WORKER_URL, cleanOptions);
    }
    
    // --- Správa stavu aplikace ---
    let currentPage = 1, totalPages = 1;
    let lastSearch = { type: 'none', params: {} };
    let errorTimeout, autocompleteTimeout;
    let areAiFeaturesEnabled = true;
    let genreCache = { movie: null, tv: null };
    let currentSearchType = 'movie';
    let currentItemsData = new Map();
    let chatHistory = [];
    const MAX_RETRIES = 3, INITIAL_RETRY_DELAY = 1000;
    
    // --- Stav Kvízu Milionář ---
    let triviaQuestionNumber = 0;
    let triviaQuestionsCache = [];
    let triviaHistory = [];
    const triviaPrizeLevels = [
        "100 Kč", "200 Kč", "300 Kč", "500 Kč", "1 000 Kč", "2 000 Kč", "4 000 Kč",
        "8 000 Kč", "16 000 Kč", "32 000 Kč", "64 000 Kč", "125 000 Kč",
        "250 000 Kč", "500 000 Kč", "1 000 000 Kč"
    ];

    // --- Systémový prompt pro Chatbota ---
    const CHATBOT_SYSTEM_PROMPT = `Jsi 'Klapka', přátelský a velmi znalý expert na filmy, seriály a dokumenty. Tvůj hlavní úkol je pomoci uživatelům najít název díla, na které si nemohou vzpomenout. Uživatel ti popíše děj, postavy, herce nebo jakýkoli útržek, který si pamatuje.

Pravidla:
1.  Vždy odpovídej POUZE jako čistý JSON objekt bez jakéhokoli markdown formátování.
2.  Analyzuj popis uživatele a kontext konverzace.
3.  Pokud identifikuješ možné shody, vrať je v poli 'suggestions'. Každý objekt v poli musí obsahovat 'title', 'year' a 'type' ('movie', 'tv' nebo 'documentary').
4.  Vytvoř přátelskou textovou odpověď ('reply_text'), která shrnuje tvé návrhy.
5.  Pokud si nejsi jistý nebo nemáš dost informací, polož doplňující otázku v 'follow_up_question'.
6.  Pokud je dotaz jen pozdrav nebo běžná konverzace, odpověz v 'reply_text' a pole 'suggestions' nech prázdné.

Příklad 1:
Uživatel: "jak se jmenuje takovy ten serial jak je tam ten arogatní doktor který má hůlku ?"
Odpověď JSON:
{
  "reply_text": "Aha, to zní velmi povědomě! Myslím, že by to mohl být tento seriál:",
  "suggestions": [
    { "title": "House", "year": 2004, "type": "tv" }
  ],
  "follow_up_question": "Je to on? Pokud ne, zkus mi o něm říct něco víc."
}

Příklad 2 (nejasný dotaz):
Uživatel: "nějaký film o vesmíru"
Odpověď JSON:
{
  "reply_text": "Téma vesmíru je obrovské! Mohl bys to trochu upřesnit?",
  "suggestions": [],
  "follow_up_question": "Hledáš spíše sci-fi, dokument, nebo něco jiného?"
}

Příklad 3 (konverzace):
Uživatel: "ahoj"
Odpověď JSON:
{
  "reply_text": "Ahoj! Jsem tu, abych ti pomohl najít jakýkoli film, seriál nebo dokument. Popiš mi, co hledáš.",
  "suggestions": [],
  "follow_up_question": ""
}`;


    // --- Funkce pro Chatbota ---
    function toggleChat() {
        chatWidget.classList.toggle('hidden');
        if (!chatWidget.classList.contains('hidden')) {
            chatInput.focus();
            if (chatMessagesContainer.children.length === 0) {
                appendMessage("Ahoj! Jsem Klapka. Nemůžeš si vzpomenout na název filmu nebo seriálu? Popiš mi, co si pamatuješ, a já ti pomůžu ho najít!", "bot");
            }
        }
    }

    function appendMessage(text, role) {
        const messageDiv = document.createElement('div');
        messageDiv.className = role === 'user' ? 'user-message' : 'bot-message';
        messageDiv.innerHTML = text;
        chatMessagesContainer.appendChild(messageDiv);
        chatMessagesContainer.scrollTop = chatMessagesContainer.scrollHeight;
    }

    function handleChatSuggestionClick(title, type) {
        const searchType = (type === 'tv') ? 'tv' : 'movie'; // Pro účely hledání považujeme dokumenty za filmy
        updateSearchTypeUI(searchType);
        userInput.value = title;
        toggleChat();
        handleSearch();
    }

    async function handleChatSubmit(e) {
        e.preventDefault();
        const query = chatInput.value.trim();
        if (!query) return;
        
        appendMessage(query, "user");
        chatInput.value = '';
        showThinkingIndicator(true);

        chatHistory.push({ role: "user", parts: [{ text: query }] });
        
        try {
            const aiResponse = await callGeminiApi(chatHistory.slice(-8), {}, CHATBOT_SYSTEM_PROMPT);
            const cleanResponse = aiResponse.replace(/```json/g, '').replace(/```/g, '').trim();
            const result = JSON.parse(cleanResponse);

            let htmlResponse = `<p>${result.reply_text}</p>`;

            if (result.suggestions && result.suggestions.length > 0) {
                htmlResponse += '<div class="mt-2 space-y-2">';
                result.suggestions.forEach(suggestion => {
                    const typeText = { 'movie': 'Film', 'tv': 'Seriál', 'documentary': 'Dokument' }[suggestion.type] || 'Dílo';
                    const escapedTitle = suggestion.title.replace(/'/g, "\\'").replace(/"/g, "&quot;");
                    htmlResponse += `
                        <button class="w-full text-left p-2 bg-slate-600 hover:bg-slate-700 rounded-lg" onclick="handleChatSuggestionClick('${escapedTitle}', '${suggestion.type}')">
                            <strong class="text-white">${suggestion.title}</strong> (${suggestion.year})
                            <span class="text-xs text-slate-400 block">${typeText}</span>
                        </button>
                    `;
                });
                htmlResponse += '</div>';
            }

            if (result.follow_up_question) {
                htmlResponse += `<p class="mt-3">${result.follow_up_question}</p>`;
            }

            appendMessage(htmlResponse, 'bot');
            chatHistory.push({ role: "model", parts: [{ text: JSON.stringify(result) }] });
            
        } catch (error) {
            console.error("Chyba chatbota:", error);
            appendMessage("Omlouvám se, ale zdá se, že mám potíže s porozuměním. Zkuste to prosím formulovat jinak.", "bot");
        } finally {
            showThinkingIndicator(false);
        }
    }
    
    function showThinkingIndicator(show) {
        const existingIndicator = document.getElementById('thinking');
        if (show && !existingIndicator) {
            const div = document.createElement('div');
            div.id = 'thinking';
            div.className = 'thinking-indicator';
            div.innerHTML = `<span></span><span></span><span></span>`;
            chatMessagesContainer.appendChild(div);
            chatMessagesContainer.scrollTop = chatMessagesContainer.scrollHeight;
        } else if (!show && existingIndicator) {
            existingIndicator.remove();
        }
    }

    async function callGeminiApi(promptOrHistory, config = {}, systemInstruction = null) {
        if (!areAiFeaturesEnabled) {
            throw new Error("AI funkce jsou deaktivovány. Chybí API klíč.");
        }
    
        const model = GEMINI_MODEL;
        const contents = Array.isArray(promptOrHistory) ? promptOrHistory : [{ role: 'user', parts: [{ text: promptOrHistory }] }];
        
        let data = {
            contents: contents,
            safetySettings: [
                { "category": "HARM_CATEGORY_HARASSMENT", "threshold": "BLOCK_NONE" },
                { "category": "HARM_CATEGORY_HATE_SPEECH", "threshold": "BLOCK_NONE" },
                { "category": "HARM_CATEGORY_SEXUALLY_EXPLICIT", "threshold": "BLOCK_NONE" },
                { "category": "HARM_CATEGORY_DANGEROUS_CONTENT", "threshold": "BLOCK_NONE" }
            ]
        };
    
        if (systemInstruction) {
            data.systemInstruction = { parts: [{ text: systemInstruction }] };
        }
        if (config.useGoogleSearch) {
            data.tools = [{ "google_search": {} }];
        }
        if (config.responseMimeType) {
            data.generationConfig = { responseMimeType: config.responseMimeType };
        }
    
        const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/${model}:generateContent`;
    
        const response = await fetchWithRetry(apiUrl, {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(data)
        });
    
        const result = await response.json();
        if (result.error) {
            console.error("Chyba Gemini API:", result.error);
            throw new Error(`Chyba Gemini API: ${result.error.message}`);
        }
        if (result.promptFeedback && result.promptFeedback.blockReason) {
            console.error("Dotaz byl zablokován:", result.promptFeedback);
            throw new Error(`Generování AI bylo zastaveno z bezpečnostních důvodů: ${result.promptFeedback.blockReason}`);
        }
        const candidate = result.candidates?.[0];
    
        if (!candidate || !candidate.content?.parts?.[0]?.text) {
            console.error("Neplatná struktura odpovědi od Gemini API:", result);
            const finishReason = candidate?.finishReason;
            if (finishReason && finishReason !== "STOP") {
                if (finishReason === 'PROHIBITED_CONTENT' || finishReason === 'SAFETY') {
                    throw new Error(`Generování AI bylo zastaveno z bezpečnostních důvodů. Původní text mohl obsahovat citlivé téma.`);
                }
                throw new Error(`Generování AI zastaveno z důvodu: ${finishReason}`);
            }
            throw new Error("V odpovědi AI modelu není platný obsah.");
        }
        return candidate.content.parts[0].text.trim();
    }
    
    function setTextTruncation(descriptionElementId, fullText) {
        const descriptionP = document.getElementById(descriptionElementId);
        if (!descriptionP) return;

        // Clean up any old button from the actions container just in case
        const actionsContainerId = `actions-container-${descriptionElementId.replace('summary-', '')}`;
        const actionsContainer = document.getElementById(actionsContainerId);
        if (actionsContainer) {
            const oldButton = actionsContainer.querySelector('.read-more-btn');
            if (oldButton) oldButton.remove();
        }
        
        const maxLength = 150;
        if (fullText && fullText.length > maxLength) {
            const truncatedText = fullText.substring(0, maxLength) + '... ';
            descriptionP.innerText = truncatedText;
            
            const readMoreButton = document.createElement('button');
            readMoreButton.innerText = 'číst dále';
            readMoreButton.className = 'read-more-btn';
            
            readMoreButton.onclick = () => {
                descriptionP.innerText = fullText; // This will remove the button as well
            };
            
            descriptionP.appendChild(readMoreButton);
        } else {
            descriptionP.innerText = fullText || 'Popis není k dispozici.';
        }
    }
    async function getCzechDescription(itemName, originalDescription, itemElementId) {
        if (!areAiFeaturesEnabled) {
            setTextTruncation(`summary-${itemElementId}`, originalDescription || 'Popis není k dispozici (vyžaduje API klíč pro překlad).');
            return;
        }
        const prompt = `Přeložte nebo shrňte následující popis filmu/seriálu do češtiny. Vraťte POUZE výsledný text bez jakéhokoli dalšího komentáře nebo úvodu. Souhrn by měl být stručný (max 2 věty) a neutrální. Pokud je původní popis prázdný, vytvořte krátký popis na základě názvu. Název: "${itemName}". Popis: "${originalDescription}"`;
        try {
            const resultText = await callGeminiApi(prompt);
            const descriptionEl = document.getElementById(`summary-${itemElementId}`);
            if (descriptionEl) {
                descriptionEl.classList.remove('italic', 'text-gray-500');
                setTextTruncation(`summary-${itemElementId}`, resultText);
            }
        } catch (error) {
            console.error(`Získání českého popisu selhalo: `, error);
            const descriptionEl = document.getElementById(`summary-${itemElementId}`);
            if (descriptionEl) {
                descriptionEl.classList.remove('italic', 'text-gray-500');
                const fallbackText = originalDescription ? `${originalDescription} (překlad selhal)` : 'Překlad selhal.';
                setTextTruncation(`summary-${itemElementId}`, fallbackText);
            }
        }
    }

    async function generateAiReview(button, itemId) {
        button.disabled = true;
        button.innerHTML = `<svg class="animate-spin h-4 w-4 mr-1 inline" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>Generuji...`;
        const reviewContainer = document.getElementById(`ai-review-container-${itemId}`);
        const item = currentItemsData.get(itemId);
        if (!reviewContainer || !item) { button.innerText = 'Chyba'; return; }
        reviewContainer.classList.remove('hidden');
        const name = item.title || item.name;
        const year = item.release_date || item.first_air_date ? new Date(item.release_date || item.first_air_date).getFullYear() : 'rok neuveden';
        const prompt = `Jsi filmový a seriálový kritik. Napiš stručnou, neutrální recenzi (3-4 věty v češtině) na dílo "${name}" (${year}). Zhodnoť klady a zápory, ale vyhni se kontroverzním tématům. Vrať pouze text recenze bez úvodních frází.`;
        try {
            const resultText = await callGeminiApi(prompt);
            reviewContainer.innerHTML = `<blockquote class="review-content">${resultText}</blockquote>`;
            button.style.display = 'none';
        } catch (error) {
            console.error("Generování AI recenze selhalo:", error);
            reviewContainer.innerHTML = `<p class="text-red-400">Při generování recenze došlo k chybě: ${error.message}</p>`;
            button.disabled = false;
            button.innerText = 'Zkusit znovu';
        }
    }

    async function fetchAiWebContent(button, itemId, type) {
        button.disabled = true;
        const originalText = button.innerHTML;
        button.innerHTML = `<svg class="animate-spin h-4 w-4 mr-1 inline" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>`;
        const item = currentItemsData.get(itemId);
        if (!item) { button.innerHTML = originalText; button.disabled = false; return; }
        const name = item.title || item.name;
        const year = item.release_date || item.first_air_date ? new Date(item.release_date || item.first_air_date).getFullYear() : 'rok neuveden';
        let prompt, modalTitle;
        if (type === 'news') {
            prompt = `Najdi nejnovější zprávy a novinky v češtině o díle "${name}" (${year}). Zaměř se na fakta a oficiální oznámení. Vrať pouze nalezené informace bez úvodních frází.`;
            modalTitle = `Aktuální novinky`;
        } else {
            prompt = `Najdi zajímavosti a perličky z natáčení v češtině k dílu "${name}" (${year}). Vyber fakta vhodná pro širokou veřejnost. Vrať pouze nalezené informace bez úvodních frází.`;
            modalTitle = `Zajímavosti`;
        }
        aiWebModalTitle.innerText = `${modalTitle} pro ${name}`;
        aiWebModalContent.innerHTML = `<p>Vyhledávám na internetu...</p>`;
        aiWebModal.classList.remove('hidden');
        try {
            const resultText = await callGeminiApi(prompt, { useGoogleSearch: true });
            aiWebModalContent.innerHTML = `<div class="web-content">${resultText || 'Nebyly nalezeny žádné informace.'}</div>`;
        } catch (error) {
            console.error(`Chyba při hledání '${type}':`, error);
            aiWebModalContent.innerHTML = `<p class="text-red-400">Při hledání informací došlo k chybě: ${error.message}</p>`;
        } finally {
            button.disabled = false;
            button.innerHTML = originalText;
        }
    }
    async function fetchAiPersonContent(button, personId, personName) {
        button.disabled = true;
        const originalText = button.innerHTML;
        button.innerHTML = `<svg class="animate-spin h-4 w-4 mr-1 inline" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>`;
        aiWebModalTitle.innerText = `Zajímavosti o herci: ${personName}`;
        aiWebModalContent.innerHTML = `<p>Vyhledávám na internetu...</p>`;
        aiWebModal.classList.remove('hidden');
        const prompt = `Najdi zajímavosti a perličky z profesního života a kariéry v češtině o herci/herečce "${personName}". Zaměř se na kariéru, ocenění a neobvyklé role. Vyhni se citlivým soukromým informacím. Vrať pouze nalezené informace bez úvodních frází.`;
        try {
            const resultText = await callGeminiApi(prompt, { useGoogleSearch: true });
            aiWebModalContent.innerHTML = `<div class="web-content">${resultText || 'Nebyly nalezeny žádné informace.'}</div>`;
        } catch (error) {
            console.error(`Chyba při hledání zajímavostí o herci:`, error);
            aiWebModalContent.innerHTML = `<p class="text-red-400">Při hledání informací došlo k chybě: ${error.message}</p>`;
        } finally {
            button.disabled = false;
            button.innerHTML = originalText;
        }
    }
    function handleRecommendationClick(title) {
        closeAllModals();
        userInput.value = title;
        handleSearch();
    }
    async function generateNextRecommendations(button, itemId) {
        button.disabled = true;
        const originalText = button.innerHTML;
        button.innerHTML = `<svg class="animate-spin h-4 w-4 mr-1 inline" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>`;
        const item = currentItemsData.get(itemId);
        if (!item) { button.innerHTML = originalText; button.disabled = false; return; }
        const name = item.title || item.name;
        nextRecoModalTitle.innerText = `Co dál po filmu/seriálu "${name}"?`;
        nextRecoModalContent.innerHTML = `<p>Generuji doporučení...</p>`;
        nextRecoModal.classList.remove('hidden');
        try {
            const prompt = `Jsi filmový expert. Vytvoř seznam 3 filmů nebo seriálů, které jsou podobné dílu "${name}". Pro každý titul uveď název a jednovětné zdůvodnění v češtině. Vrať JSON pole objektů s klíči 'title' a 'reason'. Příklad: [{"title": "Název 1", "reason": "Zdůvodnění 1."}]. Původní dílo: "${name}", Žánr: ${item.genres?.map(g => g.name).join(', ') || 'není známý'}, Popis: ${item.overview || 'není známý'}`;
            const jsonResult = await callGeminiApi(prompt, { responseMimeType: "application/json" });
            const recommendations = JSON.parse(jsonResult);
            const recommendationsHtml = recommendations.map(d => `<div onclick="handleRecommendationClick('${d.title.replace(/'/g, "\\'")}')" class="reco-item p-4 mb-2 bg-slate-700 rounded-lg transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500" role="button" tabindex="0" onkeydown="if(event.key==='Enter' || event.key===' ') { event.preventDefault(); this.click(); }"><h4 class="font-bold text-lg pointer-events-none">${d.title}</h4><p class="text-sm text-gray-400 mt-1 pointer-events-none">${d.reason}</p></div>`).join('');
            nextRecoModalContent.innerHTML = `<div class="reco-content">${recommendationsHtml}</div>`;
        } catch (error) {
            console.error("Generování doporučení selhalo:", error);
            nextRecoModalContent.innerHTML = `<p class="text-red-400">Při generování doporučení došlo k chybě: ${error.message}</p>`;
        } finally {
            button.disabled = false;
            button.innerHTML = originalText;
        }
    }
    async function getTrailerKey(itemId, itemType) {
        try {
            const url = `https://api.themoviedb.org/3/${itemType}/${itemId}/videos?language=en-US`;
            const response = await secureFetch(url);
            if (!response.ok) return null;
            const videoData = await response.json();
            const trailer = videoData.results.find(video => video.site === 'YouTube' && video.type === 'Trailer');
            return trailer ? trailer.key : null;
        } catch (error) { console.error('Chyba při načítání traileru:', error); return null; }
    }
    
    async function displayItems(itemList, title = "Naše doporučení pro vás:", showPagination = false) {
        itemListDiv.innerHTML = '';
        currentItemsData.clear();
        resultsTitle.innerText = title;

        // Vytvoření HTML pro všechny položky najednou
        const htmlPromises = itemList.map(async (item, index) => {
            const trailerKey = await getTrailerKey(item.id, item.title ? 'movie' : 'tv');
            const itemType = item.title || item.name ? (item.title ? 'movie' : 'tv') : item.media_type;
            if (!itemType) return '';

            const name = item.title || item.name;
            const posterUrl = item.poster_path ? `${BASE_IMAGE_URL}${item.poster_path}` : `https://placehold.co/500x750/1e293b/d1d5db?text=Obrázek+chybí`;
            const itemElementId = `item-${currentPage}-${index}`;
            const releaseYear = item.release_date || item.first_air_date ? new Date(item.release_date || item.first_air_date).getFullYear() : 'Neznámý';
            currentItemsData.set(item.id, item);

            const ratingHtml = item.vote_average > 0 ? `<div class="flex items-center gap-1 text-yellow-400" title="Hodnocení z TMDB"><svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" /></svg><span class="font-bold text-base">${item.vote_average.toFixed(1)} / 10</span></div>` : '';
            const trailerButtonHtml = trailerKey ? `<button onclick="openTrailer('${trailerKey}')" class="action-btn trailer-btn">Přehrát trailer</button>` : `<button class="action-btn trailer-btn-disabled" disabled>Trailer nedostupný</button>`;
            const aiButtonsDisabled = areAiFeaturesEnabled ? "" : "disabled";

            return `
                <div class="item-card flex flex-col md:flex-row items-center md:items-start gap-4" style="animation-delay: ${index * 100}ms;" data-item-id="${item.id}" data-item-type="${itemType}">
                    <div class="flex-shrink-0 w-24 md:w-32 text-center">
                        <img src="${posterUrl}" class="w-full rounded-lg shadow-md" alt="Plakát k ${name}" onerror="this.onerror=null;this.src='https://placehold.co/500x750/1e293b/d1d5db?text=Obrázek+chybí';">
                        <div id="providers-container-${itemElementId}" class="mt-2 flex justify-center gap-1 flex-wrap min-h-[28px]"></div>
                    </div>
                    <div class="flex-1 text-center md:text-left">
                        <div class="flex items-center justify-center md:justify-start gap-3 flex-wrap">
                            <h3 class="text-xl font-semibold item-title">${name} (${releaseYear})</h3>
                            ${ratingHtml}
                            <button onclick="copyToClipboard('${name} (${releaseYear})')" class="text-gray-400 hover:text-blue-400 transition-colors duration-200 p-1" aria-label="Zkopírovat název"><svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path d="M8 3a1 1 0 011-1h2a1 1 0 110 2H9a1 1 0 01-1-1z" /><path d="M6 3a2 2 0 00-2 2v11a2 2 0 002 2h8a2 2 0 002-2V5a2 2 0 00-2-2 2 2 0 01-2 2v2a2 2 0 01-2 2H8a2 2 0 01-2-2V5a2 2 0 012-2z" /></svg></button>
                        </div>
                        <div id="details-container-${itemElementId}" class="text-sm text-gray-400 mt-1 flex items-center justify-center md:justify-start flex-wrap"></div>
                        <p id="summary-${itemElementId}" class="text-gray-400 mt-2 italic">Načítám popis...</p>
                        <div id="actions-container-${itemElementId}" class="mt-4 flex flex-col items-center md:flex-row md:flex-wrap md:justify-start gap-2">
                            ${trailerButtonHtml}
                            <button onclick='generateNextRecommendations(this, ${item.id})' class="action-btn next-reco-btn" ${aiButtonsDisabled}>🎞️ Podobné tituly</button>
                            <button onclick='generateAiReview(this, ${item.id})' class="action-btn ai-review-btn" ${aiButtonsDisabled}>AI Recenze</button>
                            <button onclick="fetchAiWebContent(this, ${item.id}, 'news')" class="action-btn news-btn" ${aiButtonsDisabled}>📰 Novinky</button>
                            <button onclick="fetchAiWebContent(this, ${item.id}, 'trivia')" class="action-btn trivia-btn" ${aiButtonsDisabled}>💡 Zajímavosti</button>
                        </div>
                        <div id="ai-review-container-${item.id}" class="mt-4 hidden"></div>
                    </div>
                </div>`;
        });

        const resultsHtml = (await Promise.all(htmlPromises)).join('');
        itemListDiv.innerHTML = resultsHtml;
        resultsDiv.classList.remove('hidden');

        // Nastavení posluchačů a načítání dodatečných dat po vykreslení
        itemListDiv.querySelectorAll('.item-card').forEach((card, index) => {
            const item = itemList[index]; // Odkaz na původní data
             const itemElementId = `item-${currentPage}-${index}`;
            
            card.querySelector('.item-title').addEventListener('click', () => openItemDetails(item.id, card.dataset.itemType));
            
            fetchAndFillDetails(item, itemElementId, card.dataset.itemType);
            
            if (item.original_language !== 'cs' || !item.overview) {
                getCzechDescription(item.title || item.name, item.overview, itemElementId);
            } else {
                const descriptionEl = document.getElementById(`summary-${itemElementId}`);
                if (descriptionEl) descriptionEl.classList.remove('italic', 'text-gray-500');
                setTextTruncation(`summary-${itemElementId}`, item.overview);
            }
        });

        if (showPagination) { updatePaginationControls(); } 
        else { paginationDiv.classList.add('hidden'); }
    }


    async function startSmartSearch(query) {
        lastSearch = { type: 'none', params: {} };
        if (!areAiFeaturesEnabled) { await handleSimpleSearch(query); return; }
        try {
            const prompt = `Jsi expert na filmy a seriály. Tvým úkolem je analyzovat požadavek uživatele a extrahovat z něj strukturovaná data pro vyhledávání. Vrať JSON objekt. Pravidla pro typ: 1. Pokud text explicitně obsahuje slova jako 'seriál', 'show', 'epizody', nastav "type": "tv". 2. Pokud text explicitně obsahuje slova jako 'film', 'snímek', nastav "type": "movie". 3. Pokud text explicitně obsahuje slova jako 'herec', 'herečka', 'hrál', 'hrála', 'hráli', 's hercem', 's herečkou', 's herci', nastav "type": "person". 4. Pokud typ není jasně specifikován, klíč "type" do JSONu vůbec NEZAHRNUJ. Další pravidla: - Pokud text vypadá jako přímý název, vrať JSON s klíčem "title". - Jinak použij klíče: "genres", "actors", "director", "year", "decade". Abstraktní pojmy jako 'motivace' převeď na žánr 'drama'. - Zde je seznam oficiálních žánrů, které můžeš použít: Akční, Dobrodružný, Animovaný, Komedie, Krimi, Dokumentární, Drama, Rodinný, Fantasy, Historický, Horor, Hudební, Mysteriózní, Romantický, Sci-Fi, Televizní film, Thriller, Válečný, Western. Požadavek: "${query}"`;
            const aiResultText = await callGeminiApi(prompt, { responseMimeType: "application/json" });
            const aiParams = JSON.parse(aiResultText);
            const searchType = aiParams.type || currentSearchType;
            updateSearchTypeUI(searchType);
            if (aiParams.title) { await searchByTitle(aiParams.title, 1, searchType); } 
            else if (aiParams.actors && aiParams.actors.length > 0) { await searchByPerson(aiParams.actors[0]); } 
            else { await searchByParameters(aiParams, 1, searchType); }
        } catch (error) {
            console.error("Chyba při analýze dotazu pomocí AI, provádím standardní vyhledávání.", error);
            await handleSimpleSearch(query);
        }
    }
    async function handleSimpleSearch(query) {
       if (currentSearchType === 'person') { await searchByPerson(query); } 
       else { await searchByTitle(query, 1, currentSearchType); }
    }
    async function searchByTitle(query, page, type) {
        lastSearch = { type: 'title', params: { query, type } };
        const url = `https://api.themoviedb.org/3/search/${type}?query=${encodeURIComponent(query)}&language=cs-CZ&page=${page}`;
        await fetchAndDisplayItems(url, "Výsledky vyhledávání:", true);
    }
    async function searchByPerson(query) {
        setLoadingState(true);
        try {
            const url = `https://api.themoviedb.org/3/search/person?query=${encodeURIComponent(query)}&language=cs-CZ`;
            const response = await secureFetch(url);
            const data = await response.json();
            if (data.results && data.results.length > 0) { await displayPersonProfile(data.results[0]); } 
            else { showGlobalError(`Osoba s názvem "${query}" nebyla nalezena. Zkuste prosím přesnější jméno.`); }
        } catch (error) {
            console.error("Chyba při vyhledávání osoby:", error);
            showGlobalError("Při vyhledávání osoby došlo k chybě. Zkuste to prosím znovu.");
        } finally { setLoadingState(false); }
    }
    async function displayPersonProfile(person) {
        itemListDiv.innerHTML = '';
        currentItemsData.clear();
        paginationDiv.classList.add('hidden');
        resultsTitle.innerText = "Výsledek vyhledávání:";
        const profileUrl = person.profile_path ? `${BASE_IMAGE_URL}${person.profile_path}` : `https://placehold.co/500x750/1e293b/d1d5db?text=Obrázek+chybí`;
        const personCard = document.createElement('div');
        personCard.className = 'person-card flex flex-col md:flex-row items-center md:items-start gap-4';
        personCard.setAttribute('role', 'button');
        personCard.setAttribute('tabindex', '0');
        personCard.innerHTML = `<div class="flex-shrink-0 relative pointer-events-none"><img src="${profileUrl}" class="w-24 md:w-32 rounded-lg shadow-md" alt="Profilový obrázek ${person.name}" onerror="this.onerror=null;this.src='https://placehold.co/500x750/1e293b/d1d5db?text=Obrázek+chybí';"></div><div class="flex-1 text-center md:text-left pointer-events-none"><h3 class="text-xl font-semibold">${person.name}</h3><p class="text-gray-400 mt-2 italic">Klikněte pro zobrazení filmografie</p><div class="mt-4 pointer-events-auto"><button onclick='fetchAiPersonContent(this, ${person.id}, "${person.name.replace(/'/g, "\\'")}")' class="action-btn trivia-btn" ${areAiFeaturesEnabled ? "" : "disabled"}>💡 Zajímavosti</button></div></div>`;
        const handleCardClick = (e) => { if (e.target.tagName !== 'BUTTON' && e.target.closest('button') === null) { displayPersonFilmography(person.id, person.name); } };
        personCard.addEventListener('click', handleCardClick);
        personCard.addEventListener('keydown', (e) => { if (e.key === 'Enter' || e.key === ' ') { e.preventDefault(); handleCardClick(e); } });
        itemListDiv.appendChild(personCard);
        resultsDiv.classList.remove('hidden');
    }
    async function searchByParameters(params, page, type) {
        lastSearch = { type: 'discover', params: { ...params, type} };
        let url = `https://api.themoviedb.org/3/discover/${type}?language=cs-CZ&sort_by=popularity.desc&page=${page}`;
        const genreIds = await getGenreIds(params.genres || [], type);
        if (genreIds.length > 0) url += `&with_genres=${genreIds.join(',')}`;
        if (params.year) url += `&primary_release_year=${params.year}`;
        if (params.decade) {
            const year = parseInt(params.decade.substring(0, 4));
            if(type === 'movie') url += `&primary_release_date.gte=${year}-01-01&primary_release_date.lte=${year + 9}-12-31`;
            else url += `&first_air_date.gte=${year}-01-01&first_air_date.lte=${year + 9}-12-31`;
        }
        await fetchAndDisplayItems(url, "Výsledky vyhledávání na přání:", true);
    }
    async function fetchRankings(rankingType, page = 1) {
        setLoadingState(true);
        lastSearch = { type: 'rankings', params: { rankingType, page } };
        let url, title;
        const apiBaseUrl = `https://api.themoviedb.org/3/`;
        switch (rankingType) {
            case 'popular_movies': url = `${apiBaseUrl}movie/popular?language=cs-CZ&page=${page}`; title = 'Nejoblíbenější filmy'; break;
            case 'top_rated_movies': url = `${apiBaseUrl}movie/top_rated?language=cs-CZ&page=${page}`; title = 'Nejlépe hodnocené filmy'; break;
            case 'now_playing_movies': url = `${apiBaseUrl}movie/now_playing?language=cs-CZ&page=${page}`; title = 'Aktuálně v kinech'; break;
            case 'popular_tv': url = `${apiBaseUrl}tv/popular?language=cs-CZ&page=${page}`; title = 'Nejoblíbenější seriály'; break;
            case 'top_rated_tv': url = `${apiBaseUrl}tv/top_rated?language=cs-CZ&page=${page}`; title = 'Nejlépe hodnocené seriály'; break;
            default: showGlobalError("Neznámý typ žebříčku."); setLoadingState(false); return;
        }
        try {
            const response = await secureFetch(url);
            if (!response.ok) throw new Error(`HTTP chyba! Status: ${response.status}`);
            const data = await response.json();
            if (data.results && data.results.length > 0) {
                currentPage = data.page || 1;
                totalPages = data.total_pages || 1;
                if (rankingType.includes('movie')) updateSearchTypeUI('movie');
                else updateSearchTypeUI('tv');
                await displayItems(data.results, title, true);
            } else { showGlobalError("Nenašli jsme žádné výsledky pro tento žebříček."); }
        } catch (error) {
            console.error('Chyba při načítání žebříčku:', error);
            showGlobalError("Došlo k chybě při načítání žebříčku. Zkuste to znovu.");
        } finally { setLoadingState(false); }
    }
    async function fetchAndDisplayItems(url, title, showPagination) {
        try {
            const response = await secureFetch(url);
            if (!response.ok) throw new Error(`HTTP chyba! Stav: ${response.status}`);
            const data = await response.json();
            if (data.results && data.results.length > 0) {
                currentPage = data.page || 1;
                totalPages = data.total_pages || 1;
                await displayItems(data.results, title, showPagination);
            } else { showGlobalError("Nenašli jsme žádné filmy ani seriály. Zkuste prosím jiný dotaz."); }
        } catch (error) {
            console.error('Chyba při načítání z TMDb:', error);
            showGlobalError("Došlo k chybě při načítání. Zkontrolujte připojení a zkuste to znovu.");
        }
    }
    async function fetchRandomItems() {
        setLoadingState(true);
        try {
            let success = false;
            for (let i = 0; i < 3; i++) { 
                try {
                    const randomPage = Math.floor(Math.random() * 100) + 1; 
                    const url = `https://api.themoviedb.org/3/discover/${currentSearchType}?language=cs-CZ&sort_by=popularity.desc&include_adult=false&vote_count.gte=200&page=${randomPage}`;
                    const response = await secureFetch(url);
                    if (!response.ok) continue;
                    const data = await response.json();
                    if (data.results && data.results.length > 0) {
                        lastSearch = { type: 'none', params: {} };
                        await displayItems(data.results.slice(0, 10), "Náhodný tip pro vás:", false);
                        success = true;
                        break; 
                    }
                } catch (error) { console.error(`Pokus o náhodný tip ${i + 1} selhal:`, error); }
            }
            if (!success) { showGlobalError("Nepodařilo se najít náhodné tipy, zkuste to prosím znovu."); }
        } finally { setLoadingState(false); }
    }
    
    async function openItemDetails(itemId, itemType) {
        itemDetailModal.classList.remove('hidden');
        itemDetailContent.innerHTML = `<div class="p-8 text-center"><svg class="animate-spin h-8 w-8 text-blue-400 mx-auto" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg><p class="mt-2">Načítám detaily...</p></div>`;
        try {
            const [details, credits] = await Promise.all([
                secureFetch(`https://api.themoviedb.org/3/${itemType}/${itemId}?language=cs-CZ`).then(res => res.json()),
                secureFetch(`https://api.themoviedb.org/3/${itemType}/${itemId}/credits?language=cs-CZ`).then(res => res.json())
            ]);
            renderItemDetails(details, credits, itemType);
        } catch (error) {
            console.error("Chyba při načítání detailů:", error);
            itemDetailContent.innerHTML = `<div class="p-8 text-center text-red-400">Nepodařilo se načíst detaily. Zkuste to prosím znovu.</div>`;
        }
    }
    function renderItemDetails(details, credits, itemType) {
        const backdropUrl = details.backdrop_path ? `${BASE_BACKDROP_URL}${details.backdrop_path}` : '';
        const mainCast = credits.cast.slice(0, 10);
        const otherCast = credits.cast.slice(10, 25);
        const name = details.title || details.name;
        const tagline = details.tagline || (itemType === 'tv' ? `${details.number_of_seasons || ''} sérií, ${details.number_of_episodes || ''} epizod` : '');
        const director = credits.crew.find(c => c.job === 'Director');
        const writers = credits.crew.filter(c => c.department === 'Writing' && (c.job === 'Screenplay' || c.job === 'Writer')).map(c => c.name).join(', ') || 'není k dispozici';
        const cinematographer = credits.crew.find(c => c.job === 'Director of Photography');
        const composer = credits.crew.find(c => c.job === 'Original Music Composer');
        const crewHtml = `<div><h4 class="text-xl font-bold mb-2 text-white">Štáb</h4><ul class="text-gray-300 space-y-1"><li><strong>Režie:</strong> ${director ? director.name : 'není k dispozici'}</li><li><strong>Scénář:</strong> ${writers}</li><li><strong>Kamera:</strong> ${cinematographer ? cinematographer.name : 'není k dispozici'}</li><li><strong>Hudba:</strong> ${composer ? composer.name : 'není k dispozici'}</li></ul></div>`;
        const mainCastHtml = mainCast.map(h => `<div class="text-center flex-shrink-0 w-24 cursor-pointer focus:outline-none focus:ring-2 focus:ring-blue-500 rounded-lg p-1" role="button" tabindex="0" onclick="displayPersonFilmography(${h.id}, '${h.name.replace(/'/g, "\\'")}')" onkeydown="if(event.key==='Enter' || event.key===' ') { event.preventDefault(); this.click(); }"><img src="${h.profile_path ? BASE_IMAGE_URL + h.profile_path : 'https://placehold.co/200x300/1e293b/d1d5db?text=?'}" alt="${h.name}" class="w-20 h-20 object-cover rounded-full mx-auto mb-2 shadow-md pointer-events-none"><p class="text-sm font-bold text-white pointer-events-none">${h.name}</p><p class="text-xs text-gray-400 pointer-events-none">${h.character}</p></div>`).join('');
        const otherCastHtml = otherCast.length > 0 ? `<div><h4 class="text-xl font-bold mb-2 text-white">Další účinkující</h4><ul class="text-gray-300 grid grid-cols-2 sm:grid-cols-3 gap-2">${otherCast.map(h => `<li class="cursor-pointer hover:text-blue-400 transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500 rounded p-1" role="button" tabindex="0" onclick="displayPersonFilmography(${h.id}, '${h.name.replace(/'/g, "\\'")}')" onkeydown="if(event.key==='Enter' || event.key===' ') { event.preventDefault(); this.click(); }">${h.name}</li>`).join('')}</ul></div>` : '';
        itemDetailContent.innerHTML = `<div class="relative"><button id="close-item-detail-modal-btn" class="absolute top-2 right-2 bg-black bg-opacity-50 text-white rounded-full h-8 w-8 flex justify-center items-center font-bold text-2xl z-20">&times;</button><img src="${backdropUrl}" class="w-full h-48 md:h-64 object-cover rounded-t-lg brightness-50" onerror="this.style.display='none'"><div class="absolute bottom-0 left-0 p-6 text-white z-10"><h3 class="text-3xl md:text-4xl font-extrabold">${name}</h3><p class="text-lg italic text-gray-300">${tagline}</p></div></div><div class="p-6 space-y-6"><div><h4 class="text-xl font-bold mb-2 text-white">Příběh</h4><p class="text-gray-300">${details.overview || 'Popis není k dispozici.'}</p></div>${crewHtml}${mainCastHtml ? `<div><h4 class="text-xl font-bold mb-4 text-white">Hlavní herecké obsazení</h4><div class="cast-container">${mainCastHtml}</div></div>` : ''}${otherCastHtml}</div>`;
        document.getElementById('close-item-detail-modal-btn').addEventListener('click', closeAllModals);
    }
    async function displayPersonFilmography(personId, personName) {
        closeAllModals();
        setLoadingState(true);
        try {
            const url = `https://api.themoviedb.org/3/person/${personId}/combined_credits?language=cs-CZ`;
            const response = await secureFetch(url);
            if (!response.ok) throw new Error(`HTTP chyba! Status: ${response.status}`);
            const data = await response.json();
            const sortedItems = data.cast.filter(p => p.release_date || p.first_air_date).sort((a, b) => new Date(b.release_date || b.first_air_date) - new Date(a.release_date || a.first_air_date));
            if (sortedItems.length > 0) {
                 lastSearch = { type: 'person', params: { personId, personName, items: sortedItems } };
                 await handlePagination(1);
                 resultsDiv.scrollIntoView({ behavior: 'smooth' });
            } else { showGlobalError(`Pro ${personName} nebyly nalezeny žádné filmy ani seriály.`); }
        } catch (error) {
            console.error(`Chyba při načítání filmografie pro ${personName}:`, error);
            showGlobalError("Při načítání filmografie došlo k chybě.");
        } finally { setLoadingState(false); }
    }
    function formatTime(minutes) {
        if (!minutes || typeof minutes !== 'number') return '';
        const h = Math.floor(minutes / 60), m = minutes % 60;
        return h === 0 ? `${m}m` : (m === 0 ? `${h}h` : `${h}h ${m}m`);
    }
    function getFlagEmoji(isoCode) {
        if (!isoCode) return '';
        const flags = {'US':'🇺🇸','GB':'🇬🇧','FR':'🇫🇷','DE':'🇩🇪','ES':'🇪🇸','IT':'🇮🇹','JP':'🇯🇵','KR':'🇰🇷','CN':'🇨🇳','RU':'🇷🇺','CA':'🇨🇦','AU':'🇦🇺','IN':'🇮🇳','CZ':'🇨🇿','SK':'🇸🇰','PL':'🇵🇱','NZ':'🇳🇿','SE':'🇸🇪','NO':'🇳🇴','DK':'🇩🇰','FI':'🇫🇮','IE':'🇮🇪'};
        return flags[isoCode] || isoCode;
    }
    async function fetchAndDisplayWatchProviders(itemId, itemType, containerId) {
        try {
            const url = `https://api.themoviedb.org/3/${itemType}/${itemId}/watch/providers`;
            const response = await secureFetch(url);
            if (!response.ok) return;
            const data = await response.json();

            const container = document.getElementById(containerId);
            if (!container) return;

            const providers = data.results?.CZ?.flatrate;
            if (providers && providers.length > 0) {
                const providersHtml = providers
                    .slice(0, 5) // Omezíme na max 5 log
                    .map(p => `<img src="${BASE_IMAGE_URL}${p.logo_path}" alt="${p.provider_name}" title="${p.provider_name}" class="h-6 w-6 rounded-md object-cover">`)
                    .join('');

                if (providersHtml) {
                    container.innerHTML = `<div class="flex items-center gap-2 flex-wrap justify-center">${providersHtml}</div>`;
                }
            }
        } catch (error) {
            console.error("Nepodařilo se načíst poskytovatele sledování:", error);
        }
    }
     async function fetchAndFillDetails(item, itemElementId, itemType) {
        try {
            const url = `https://api.themoviedb.org/3/${itemType}/${item.id}?language=cs-CZ`;
            const response = await secureFetch(url);
            if (!response.ok) return;
            const details = await response.json();
            const genres = details.genres?.map(g => g.name).slice(0, 3).join(', ') || '';
            const duration = itemType === 'movie' ? formatTime(details.runtime) : (details.episode_run_time?.[0] ? `${details.episode_run_time[0]}m/ep.` : '');
            const flag = details.production_countries?.[0] ? getFlagEmoji(details.production_countries[0].iso_3166_1) : '';
            const detailsContainer = document.getElementById(`details-container-${itemElementId}`);
            if (detailsContainer) {
                let html = '';
                if (genres) html += `<span>${genres}</span>`;
                if (duration) html += `<span class="mx-2 text-gray-500">•</span><span>${duration}</span>`;
                if (flag) html += `<span class="mx-2 text-gray-500">•</span><span>${flag}</span>`;
                detailsContainer.innerHTML = html;
            }
             fetchAndDisplayWatchProviders(item.id, itemType, `providers-container-${itemElementId}`);
        } catch (error) { console.error("Nepodařilo se načíst doplňující detaily:", error); }
    }
    async function getGenreIds(genreNames, type) {
        if (!genreCache[type]) {
            try {
                const response = await secureFetch(`https://api.themoviedb.org/3/genre/${type}/list?language=cs-CZ`);
                const data = await response.json();
                genreCache[type] = data.genres;
            } catch (e) { console.error(`Nepodařilo se načíst žánry pro ${type}`, e); return []; }
        }
        const lowerCaseGenreNames = genreNames.map(n => n.toLowerCase());
        return genreCache[type].filter(z => lowerCaseGenreNames.includes(z.name.toLowerCase())).map(z => z.id);
    }
    function setLoadingState(isLoading) {
        loadingMessage.classList.toggle('hidden', !isLoading);
        errorMessage.classList.toggle('hidden', true);
        resultsDiv.classList.toggle('hidden', isLoading);
        [sendButton, randomTipButton, userInput].forEach(el => el.disabled = isLoading);
    }
    function disableAllAiButtons() {
        showGlobalError("AI funkce jsou nedostupné z důvodu chyby v konfiguraci API klíče.");
        document.querySelectorAll('.ai-review-btn, .next-reco-btn, .news-btn, .trivia-btn, #movie-night-btn, #trivia-btn-main, #generate-movie-night-btn').forEach(btn => {
            btn.disabled = true;
            btn.title = "AI funkce nedostupné";
        });
        if (chatToggleButton) {
            chatToggleButton.style.display = 'none';
        }
        if (chatForm) {
            chatInput.disabled = true;
            chatInput.placeholder = "Chat je nedostupný.";
            document.getElementById('chat-send-btn').disabled = true;
        }
    }
    async function fetchWithRetry(url, options, retries = MAX_RETRIES, delay = INITIAL_RETRY_DELAY) {
        let lastError;
        for (let i = 0; i < retries; i++) {
            try {
                const response = await secureFetch(url, options);
                if (response.ok) return response;
                
                lastError = new Error(`Chyba serveru: ${response.status} ${response.statusText}`);
                
                if (response.status === 401) {
                    if (areAiFeaturesEnabled) { // Spustit pouze jednou
                        console.error("Chyba 401: Neoprávněný přístup. Pravděpodobně chybí platný API klíč. AI funkce byly deaktivovány.");
                        areAiFeaturesEnabled = false;
                        disableAllAiButtons();
                    }
                    throw lastError; // Neopakovat pokus při chybě autorizace
                }
            } catch (error) {
                console.error(`Pokus ${i + 1} selhal:`, error);
                lastError = error;
                // Pokud chyba obsahuje 401, ukončit opakování
                if (error.message && error.message.includes('401')) {
                    throw lastError;
                }
            }
            if (i < retries - 1) await new Promise(res => setTimeout(res, delay *= 2));
        }
        throw lastError;
    }
    async function executeMainSearch(query) {
        const type = currentSearchType;
        if (type === 'person') { await searchByPerson(query); return; }
        const directSearchUrl = `https://api.themoviedb.org/3/search/${type}?query=${encodeURIComponent(query)}&language=cs-CZ&page=1`;
        try {
            const response = await secureFetch(directSearchUrl);
            if (!response.ok) throw new Error('Odpověď z přímého vyhledávání není v pořádku');
            const data = await response.json();
            if (data.results && data.results.length > 0) {
                lastSearch = { type: 'title', params: { query, type } };
                currentPage = data.page || 1;
                totalPages = data.total_pages || 1;
                await displayItems(data.results, "Výsledky vyhledávání:", true);
            } else { await startSmartSearch(query); }
        } catch (error) {
            console.error("Došlo k chybě během přímého vyhledávání, spouštím jako zálohu chytré vyhledávání s AI.", error);
            await startSmartSearch(query);
        }
    }
    async function handleSearch() {
        rankingsOptionsDiv.classList.remove('show-rankings');
        showRankingsButton.classList.remove('active');
        const query = userInput.value.trim();
        if (query === "") { if (currentSearchType === 'movie' || currentSearchType === 'tv') fetchRankings(currentSearchType === 'movie' ? 'popular_movies' : 'popular_tv'); return; }
        setLoadingState(true);
        try { await executeMainSearch(query); } 
        catch (error) { console.error("Celkové vyhledávání selhalo:", error); showGlobalError("Při vyhledávání došlo k chybě. Zkuste to prosím znovu."); } 
        finally { setLoadingState(false); }
    }
    async function handlePagination(page) {
        setLoadingState(true);
        try {
            const { type, params } = lastSearch;
            if (type === 'title') { await searchByTitle(params.query, page, params.type); } 
            else if (type === 'discover') { await searchByParameters(params, page, params.type); } 
            else if (type === 'person') {
                const itemsPerPage = 10;
                totalPages = Math.ceil(params.items.length / itemsPerPage);
                currentPage = page;
                const start = (page - 1) * itemsPerPage, end = start + itemsPerPage;
                const itemsForPage = params.items.slice(start, end);
                await displayItems(itemsForPage, `Filmografie: ${params.personName}`, true);
            } else if (type === 'rankings') { await fetchRankings(params.rankingType, page); }
        } catch (error) { showGlobalError("Při načítání další stránky došlo k chybě."); } 
        finally { setLoadingState(false); }
    }
    function updatePaginationControls() {
        if (totalPages > 1 && lastSearch.type !== 'none') {
            paginationDiv.classList.remove('hidden');
            pageInfo.innerText = `Stránka ${currentPage} z ${totalPages}`;
            prevButton.disabled = currentPage === 1;
            nextButton.disabled = currentPage >= totalPages;
        } else { paginationDiv.classList.add('hidden'); }
    }
    function copyToClipboard(text) {
        const textArea = document.createElement("textarea");
        textArea.value = text;
        textArea.style.position = "fixed"; textArea.style.opacity = "0";
        document.body.appendChild(textArea);
        textArea.focus(); textArea.select();
        try {
            if (document.execCommand('copy')) showMessage('Zkopírováno!', 'success');
            else showMessage('Kopírování selhalo.', 'error');
        } catch (err) { console.error('Nepodařilo se zkopírovat text: ', err); showMessage('Kopírování selhalo.', 'error'); }
        document.body.removeChild(textArea);
    }
    function showMessage(text, type = 'success') {
        messageBox.innerText = text;
        messageBox.style.backgroundColor = type === 'success' ? '#16a34a' : '#dc2626';
        messageBox.classList.add('show');
        setTimeout(() => messageBox.classList.remove('show'), 2500);
    }
    function showGlobalError(message) {
        clearTimeout(errorTimeout);
        errorMessage.innerText = message;
        errorMessage.classList.remove('hidden');
        resultsDiv.classList.add('hidden');
        paginationDiv.classList.add('hidden');
        errorTimeout = setTimeout(() => { errorMessage.classList.add('hidden'); }, 5000);
    }
    function openTrailer(key) {
        trailerIframe.src = `https://www.youtube.com/embed/${key}?autoplay=1&rel=0`;
        trailerModal.classList.remove('hidden');
    }
    function closeAllModals() {
    [trailerModal, aiWebModal, nextRecoModal, itemDetailModal, movieNightModal, triviaModal].forEach(m => m.classList.add('hidden'));
    
    // KLÍČOVÝ KROK: Resetování iframe, aby se nepřekrývaly trailery.
    if (trailerIframe) {
        trailerIframe.src = ''; 
    }

    }
    function updateSearchTypeUI(newType) {
        currentSearchType = newType;
        [searchTypeMovieButton, searchTypeTvButton, searchTypePersonButton, showRankingsButton].forEach(t => t.classList.remove('active'));
        if (newType === 'movie') { searchTypeMovieButton.classList.add('active'); userInput.placeholder = 'Hledejte filmy...'; } 
        else if (newType === 'tv') { searchTypeTvButton.classList.add('active'); userInput.placeholder = 'Hledejte seriály...'; } 
        else if (newType === 'person') { searchTypePersonButton.classList.add('active'); userInput.placeholder = 'Hledejte herce nebo herečky...'; }
        else { userInput.placeholder = 'Vyberte z žebříčků...'; }
    }
    async function fetchAutocomplete(query) {
        if (query.length < 2) { autocompleteContainer.classList.add('hidden'); return; }
        try {
            const url = `https://api.themoviedb.org/3/search/multi?query=${encodeURIComponent(query)}&language=cs-CZ&page=1`;
            const response = await secureFetch(url);
            const data = await response.json();
            if (data.results && data.results.length > 0) {
                const filtered = data.results.filter(p => ['movie', 'person', 'tv'].includes(p.media_type)).slice(0, 5);
                renderAutocomplete(filtered);
            } else { autocompleteContainer.classList.add('hidden'); }
        } catch(e) { console.error("Chyba našeptávače:", e); autocompleteContainer.classList.add('hidden'); }
    }
    function renderAutocomplete(items) {
        autocompleteContainer.innerHTML = '';
        items.forEach(item => {
            const div = document.createElement('div');
            div.className = 'suggestion-item';
            const name = item.media_type === 'person' ? item.name : (item.title || item.name);
            const type = {'movie': 'film', 'tv': 'seriál', 'person': 'herec/herečka'}[item.media_type];
            div.innerHTML = `<strong>${name}</strong> <span class="text-sm text-gray-500">(${type})</span>`;
            div.addEventListener('mousedown', () => {
                userInput.value = name;
                autocompleteContainer.classList.add('hidden');
                if (item.media_type === 'person') { searchByPerson(name); } 
                else { updateSearchTypeUI(item.media_type); handleSearch(); }
            });
            autocompleteContainer.appendChild(div);
        });
        autocompleteContainer.classList.remove('hidden');
    }
    
    async function handleMovieNightGeneration() {
        const theme = movieNightInput.value.trim();
        if (!theme) return;
        
        generateMovieNightButton.disabled = true;
        generateMovieNightButton.innerHTML = `<svg class="animate-spin h-5 w-5 mx-auto" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>`;
        movieNightContent.innerHTML = '';

        try {
            const prompt = `Jsi kreativní filmový kurátor. Vytvoř "filmový večer" na téma: "${theme}". Tvůj výstup musí být POUZE JSON objekt bez jakéhokoli dalšího textu nebo markdown formátování. Struktura JSON musí být:
            {
              "theme_title": "Název pro filmový večer v češtině",
              "introduction": "Krátký, lákavý úvod (2-3 věty) k tématu v češtině.",
              "movies": [
                { "title": "Originální název filmu 1", "year": 1999, "reason": "Jedna věta v češtině, proč se tento film hodí k tématu." },
                { "title": "Originální název filmu 2", "year": 2008, "reason": "Jedna věta v češtině, proč se tento film hodí k tématu." },
                { "title": "Originální název filmu 3", "year": 2010, "reason": "Jedna věta v češtině, proč se tento film hodí k tématu." }
              ]
            }
            Navrhni 3 filmy. Ujisti se, že názvy filmů jsou správně a jsou to skutečné, známé filmy.`;

            const jsonResponse = await callGeminiApi(prompt, { responseMimeType: "application/json" });
            const data = JSON.parse(jsonResponse);

            let html = `<h4 class="text-2xl font-bold text-purple-300 mb-2">${data.theme_title}</h4>`;
            html += `<p class="mb-6">${data.introduction}</p>`;
            html += '<div class="space-y-4">';
            data.movies.forEach(movie => {
                html += `
                    <div class="reco-item p-4 bg-slate-700 rounded-lg" onclick="handleRecommendationClick('${movie.title.replace(/'/g, "\\'")}')">
                        <h5 class="font-bold text-lg text-white">${movie.title} (${movie.year})</h5>
                        <p class="text-sm text-slate-300 mt-1">${movie.reason}</p>
                    </div>
                `;
            });
            html += '</div>';
            movieNightContent.innerHTML = html;

        } catch (error) {
            console.error("Chyba při generování filmového večera:", error);
            movieNightContent.innerHTML = `<p class="text-red-400">Při generování došlo k chybě: ${error.message}</p>`;
        } finally {
            generateMovieNightButton.disabled = false;
            generateMovieNightButton.innerText = "Generovat";
        }
    }

    // --- Funkce pro Kvíz Milionář ---
    function startTriviaGame() {
        triviaQuestionNumber = 0;
        triviaQuestionsCache = [];
        triviaHistory = []; 
    
        triviaStartScreen.classList.add('hidden');
        triviaEndScreen.classList.add('hidden');
        triviaGameScreen.classList.remove('hidden');
        
        displayNextTriviaQuestion();
    }
    

    async function fetchTriviaQuestionBatch() {
        triviaQuestionEl.innerHTML = `
            <div class="text-center">
                <svg class="animate-spin h-8 w-8 text-amber-400 mx-auto" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
                <div id="loading-fact-container" class="text-lg text-slate-400 mt-4 italic">
                    <p>Připravuji další sadu otázek...</p>
                </div>
            </div>`;
        triviaAnswersEl.innerHTML = '';
        triviaFeedbackEl.innerHTML = '';

        const factPromise = callGeminiApi(`Jsi filmový nadšenec. Napiš jednu krátkou (maximálně 2 věty), zajímavou a méně známou perličku nebo fakt ze světa filmu nebo seriálů v češtině. Vrať POUZE text samotného faktu bez jakéhokoli úvodu nebo formátování.`)
            .catch(e => {
                console.error("Nepodařilo se načíst filmový fakt:", e);
                return "Načítání zajímavosti selhalo, ale otázky jsou na cestě!";
            });
        
        let difficulty;
        if (triviaQuestionNumber < 5) {
            difficulty = "velmi lehké až lehké";
        } else if (triviaQuestionNumber < 10) {
            difficulty = "středně těžké";
        } else {
            difficulty = "těžké až velmi těžké";
        }

        try {
            const historyPrompt = triviaHistory.length > 0 ? `Důležité: Vyhni se tématům, která se týkají těchto filmů/seriálů: ${triviaHistory.join(', ')}.` : '';
            const prompt = `Jsi tvůrce vědomostní soutěže "Chcete být milionářem?". Vytvoř sadu PĚTI filmových a seriálových otázek v češtině. Témata musí být velmi rozmanitá: zahrň moderní i klasické filmy, populární seriály (včetně těch ze streamovacích služeb jako Netflix nebo HBO), české i zahraniční pohádky a animované filmy. Pokryj období od 90. let po současnost. Zahrň alespoň jednu otázku na téma fantasy/sci-fi seriálů a jednu na téma českých filmů nebo pohádek. Obtížnost otázek by měla být ${difficulty}. ${historyPrompt} Vrať POUZE JSON pole 5 objektů bez jakéhokoli markdownu. Struktura: { "question": "Text otázky?", "answers": ["Odpověď A", "Odpověď B", "Odpověď C", "Odpověď D"], "correct_answer": "Správná odpověď", "topic": "Název filmu/seriálu" }. Správná odpověď musí být jednou z možností.`;
            
            const jsonResponse = await callGeminiApi(prompt, { responseMimeType: "application/json" });
            const data = JSON.parse(jsonResponse);
            
            if (Array.isArray(data) && data.length > 0) {
                 triviaQuestionsCache.push(...data);
                 const factText = await factPromise;
                 const factContainer = document.getElementById('loading-fact-container');
                 if (factContainer) {
                     factContainer.innerHTML = `<p class="font-bold text-amber-400">Věděli jste, že...</p><p class="mt-2">${factText}</p>`;
                 }
                 setTimeout(displayNextTriviaQuestion, 2500); // Automaticky pokračovat po 2.5s
            } else {
                throw new Error("API vrátilo neplatný formát nebo žádné otázky.");
            }

        } catch (error) {
            console.error("Chyba při generování sady kvízových otázek:", error);
            triviaQuestionEl.innerHTML = `<p class="text-red-400">Chyba při načítání otázek. Hra bude ukončena.</p>`;
            setTimeout(() => endTriviaGame(false), 2000);
        }
    }

    async function displayNextTriviaQuestion() {
        if (triviaQuestionNumber >= 15) {
            endTriviaGame(true); // Hráč vyhrál
            return;
        }
        
        if (triviaQuestionsCache.length === 0) {
            await fetchTriviaQuestionBatch();
            return; 
        }

        const data = triviaQuestionsCache.shift();
        triviaHistory.push(data.topic);

        triviaQuestionNumberEl.innerText = triviaQuestionNumber + 1;
        updatePrizeLadder();

        triviaQuestionEl.innerText = data.question;
        triviaAnswersEl.innerHTML = '';
        triviaFeedbackEl.innerHTML = '';
        
        const shuffledAnswers = data.answers.sort(() => Math.random() - 0.5);

        shuffledAnswers.forEach(answer => {
            const button = document.createElement('button');
            button.className = 'trivia-answer-btn';
            button.innerHTML = `<div class="audience-bar"></div><span class="answer-text">${answer}</span>`;
            button.onclick = () => selectTriviaAnswer(button, answer, data.correct_answer);
            triviaAnswersEl.appendChild(button);
        });
        
        triviaQuestionNumber++;
    }

    function selectTriviaAnswer(selectedButton, selectedAnswer, correctAnswer) {
        const buttons = triviaAnswersEl.querySelectorAll('button');
        buttons.forEach(btn => btn.disabled = true);
        selectedButton.classList.add('selected');

        setTimeout(() => checkTriviaAnswer(selectedButton, selectedAnswer, correctAnswer), 2000);
    }
    
    function checkTriviaAnswer(selectedButton, selectedAnswer, correctAnswer) {
        const isCorrect = selectedAnswer === correctAnswer;
        
        const buttons = triviaAnswersEl.querySelectorAll('button');
        buttons.forEach(button => {
            button.classList.remove('selected');
            if (button.querySelector('.answer-text').innerText === correctAnswer) {
                button.classList.add('correct');
            }
        });

        if (isCorrect) {
            triviaFeedbackEl.innerText = "Správně!";
            triviaFeedbackEl.className = 'mt-4 text-center font-bold text-xl text-green-400';
            setTimeout(displayNextTriviaQuestion, 2000);
        } else {
            selectedButton.classList.add('incorrect');
            triviaFeedbackEl.innerText = `Špatně! Správná odpověď byla "${correctAnswer}".`;
            triviaFeedbackEl.className = 'mt-4 text-center font-bold text-xl text-red-400';
            setTimeout(() => endTriviaGame(false), 3000);
        }
    }
    
    function endTriviaGame(isWinner) {
        triviaGameScreen.classList.add('hidden');
        triviaEndScreen.classList.remove('hidden');

        if(isWinner) {
            triviaEndTitle.innerText = "Gratulujeme, milionáři!";
            triviaEndMessage.innerText = `Dokázali jste to! Odpověděli jste správně na všech 15 otázek a vyhráváte hlavní cenu ${triviaPrizeLevels[14]}!`;
        } else {
             triviaEndTitle.innerText = "Konec hry!";
             const lastCompletedLevel = triviaQuestionNumber - 1;
             let prize = "0 Kč";

             if (lastCompletedLevel >= 10) {
                 prize = triviaPrizeLevels[9]; // Záchytný bod na 32 000 Kč
             } else if (lastCompletedLevel >= 5) {
                 prize = triviaPrizeLevels[4]; // Záchytný bod na 1 000 Kč
             }

             triviaEndMessage.innerText = `Bohužel to nevyšlo. Odnášíte si zaručenou výhru ${prize}.`;
        }
    }
    
    function updatePrizeLadder() {
        let html = '';
        triviaPrizeLevels.slice().reverse().forEach((prize, index) => {
            const levelIndex = 14 - index;
            let classes = 'prize-level';
            if (levelIndex < triviaQuestionNumber) {
                // Already answered
            } else if (levelIndex === triviaQuestionNumber) {
                classes += ' current';
            }
            if (levelIndex === 4 || levelIndex === 9 || levelIndex === 14) {
                 classes += ' guarantee';
            }
            html += `<div class="${classes}">${15 - index}. ${prize}</div>`;
        });
        triviaPrizeLadderEl.innerHTML = html;
    }

    // --- Posluchače událostí ---
    document.addEventListener('DOMContentLoaded', () => {
        // Přiřazení DOM prvků až po načtení stránky
        userInput = document.getElementById('user-input');
        sendButton = document.getElementById('send-btn');
        randomTipButton = document.getElementById('random-tip-btn');
        loadingMessage = document.getElementById('loading');
        errorMessage = document.getElementById('error-message');
        resultsDiv = document.getElementById('results');
        resultsTitle = document.getElementById('results-title');
        itemListDiv = document.getElementById('item-list');
        messageBox = document.getElementById('message-box');
        paginationDiv = document.getElementById('pagination');
        prevButton = document.getElementById('prev-btn');
        nextButton = document.getElementById('next-btn');
        pageInfo = document.getElementById('page-info');
        autocompleteContainer = document.getElementById('autocomplete-suggestions');
        searchTypeMovieButton = document.getElementById('search-type-movie');
        searchTypeTvButton = document.getElementById('search-type-tv');
        searchTypePersonButton = document.getElementById('search-type-person');
        showRankingsButton = document.getElementById('show-rankings-btn');
        rankingsOptionsDiv = document.getElementById('rankings-options');
        trailerModal = document.getElementById('trailer-modal');
        closeTrailerModalButton = document.getElementById('close-trailer-modal-btn');
        trailerIframe = document.getElementById('trailer-iframe');
        aiWebModal = document.getElementById('ai-web-modal');
        closeAiWebModalButton = document.getElementById('close-ai-web-modal-btn');
        aiWebModalTitle = document.getElementById('ai-web-modal-title');
        aiWebModalContent = document.getElementById('ai-web-modal-content');
        nextRecoModal = document.getElementById('next-reco-modal');
        closeNextRecoModalButton = document.getElementById('close-next-reco-modal-btn');
        nextRecoModalTitle = document.getElementById('next-reco-modal-title');
        nextRecoModalContent = document.getElementById('next-reco-modal-content');
        itemDetailModal = document.getElementById('item-detail-modal');
        itemDetailContent = document.getElementById('item-detail-content');
        chatToggleButton = document.getElementById('chat-toggle-btn');
        chatWidget = document.getElementById('chat-widget');
        chatMessagesContainer = document.getElementById('chat-messages');
        chatForm = document.getElementById('chat-form');
        chatInput = document.getElementById('chat-input');
        movieNightButton = document.getElementById('movie-night-btn');
        movieNightModal = document.getElementById('movie-night-modal');
        closeMovieNightModalButton = document.getElementById('close-movie-night-modal-btn');
        movieNightInput = document.getElementById('movie-night-input');
        generateMovieNightButton = document.getElementById('generate-movie-night-btn');
        movieNightContent = document.getElementById('movie-night-content');
        
        // Kvíz
        triviaMainButton = document.getElementById('trivia-btn-main');
        triviaModal = document.getElementById('trivia-modal');
        closeTriviaModalButton = document.getElementById('close-trivia-modal-btn');
        triviaStartScreen = document.getElementById('trivia-start-screen');
        triviaGameScreen = document.getElementById('trivia-game-screen');
        startTriviaButton = document.getElementById('start-trivia-btn');
        triviaQuestionEl = document.getElementById('trivia-question');
        triviaAnswersEl = document.getElementById('trivia-answers');
        triviaFeedbackEl = document.getElementById('trivia-feedback');
        triviaEndScreen = document.getElementById('trivia-end-screen');
        triviaEndTitle = document.getElementById('trivia-end-title');
        triviaEndMessage = document.getElementById('trivia-end-message');
        restartTriviaButton = document.getElementById('restart-trivia-btn');
        triviaQuestionNumberEl = document.getElementById('trivia-question-number');
        triviaPrizeLadderEl = document.getElementById('trivia-prize-ladder');


        // Spuštění a nastavení posluchačů
        updateSearchTypeUI('movie');
        try {
            fetchRankings('popular_movies', 1);
        } catch (error) {
            console.error("Nepodařilo se načíst úvodní data:", error);
            showGlobalError("Chyba při startu aplikace. Zkuste prosím obnovit stránku.");
        }

        chatToggleButton.addEventListener('click', toggleChat);
        chatForm.addEventListener('submit', handleChatSubmit);
        sendButton.addEventListener('click', handleSearch);
        randomTipButton.addEventListener('click', fetchRandomItems);
        userInput.addEventListener('keypress', (e) => { if (e.key === 'Enter') handleSearch(); });
        prevButton.addEventListener('click', () => { if (currentPage > 1) handlePagination(currentPage - 1); });
        nextButton.addEventListener('click', () => { if (currentPage < totalPages) handlePagination(currentPage + 1); });
        
        searchTypeMovieButton.addEventListener('click', () => {
            updateSearchTypeUI('movie');
            rankingsOptionsDiv.classList.remove('show-rankings');
            if (userInput.value === "") fetchRankings('popular_movies', 1);
        });
        searchTypeTvButton.addEventListener('click', () => {
            updateSearchTypeUI('tv');
            rankingsOptionsDiv.classList.remove('show-rankings');
            if (userInput.value === "") fetchRankings('popular_tv', 1);
        });
        
        searchTypePersonButton.addEventListener('click', () => { updateSearchTypeUI('person'); rankingsOptionsDiv.classList.remove('show-rankings'); resultsDiv.classList.add('hidden'); paginationDiv.classList.add('hidden'); });
        showRankingsButton.addEventListener('click', () => {
            userInput.value = '';
            resultsDiv.classList.add('hidden');
            paginationDiv.classList.add('hidden');
            updateSearchTypeUI('rankings');
            rankingsOptionsDiv.classList.toggle('show-rankings');
            showRankingsButton.classList.toggle('active');
        });
        rankingsOptionsDiv.addEventListener('click', (e) => {
            const button = e.target.closest('button');
            if (!button) return;
            const rankingType = button.dataset.ranking;
            fetchRankings(rankingType, 1);
            rankingsOptionsDiv.querySelectorAll('.rankings-btn').forEach(btn => btn.classList.remove('active'));
            button.classList.add('active');
        });
        
        movieNightButton.addEventListener('click', () => movieNightModal.classList.remove('hidden'));
        generateMovieNightButton.addEventListener('click', handleMovieNightGeneration);
        movieNightInput.addEventListener('keypress', (e) => { if (e.key === 'Enter') handleMovieNightGeneration(); });

        // Kvíz Listeners
        triviaMainButton.addEventListener('click', () => triviaModal.classList.remove('hidden'));
        startTriviaButton.addEventListener('click', startTriviaGame);
        restartTriviaButton.addEventListener('click', startTriviaGame);

        userInput.addEventListener('input', () => { clearTimeout(autocompleteTimeout); autocompleteTimeout = setTimeout(() => fetchAutocomplete(userInput.value), 300); });
        userInput.addEventListener('blur', () => { setTimeout(() => autocompleteContainer.classList.add('hidden'), 150); });
        [closeTrailerModalButton, closeAiWebModalButton, closeNextRecoModalButton, closeMovieNightModalButton, closeTriviaModalButton].forEach(btn => btn.addEventListener('click', closeAllModals));
        [trailerModal, aiWebModal, nextRecoModal, itemDetailModal, movieNightModal, triviaModal].forEach(modal => modal.addEventListener('click', (e) => { if (e.target === modal) closeAllModals(); }));
        
        document.addEventListener('keydown', (e) => {
            if (e.key === 'Escape') {
                closeAllModals();
            }
        });
    });
</script>

</body>
</html>

