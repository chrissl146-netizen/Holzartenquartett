# Holzartenquartett
Holzartenquatett
<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Holzarten-Eigenschaften-Quartett</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        
        body {
            font-family: 'Inter', sans-serif;
            font-weight: 300;
        }
        
        .card-3d {
            transform-style: preserve-3d;
            transition: transform 0.6s;
        }
        
        .card-flipped {
            transform: rotateY(180deg);
        }
        
        .card-front, .card-back {
            backface-visibility: hidden;
            position: absolute;
            width: 100%;
            height: 100%;
        }
        
        .card-back {
            transform: rotateY(180deg);
        }
        
        .wood-texture {
            background: linear-gradient(45deg, #8B4513 25%, transparent 25%), 
                        linear-gradient(-45deg, #8B4513 25%, transparent 25%), 
                        linear-gradient(45deg, transparent 75%, #8B4513 75%), 
                        linear-gradient(-45deg, transparent 75%, #8B4513 75%);
            background-size: 20px 20px;
            background-position: 0 0, 0 10px, 10px -10px, -10px 0px;
            background-color: #D2B48C;
            opacity: 0.1;
        }
        
        .pulse-win {
            animation: pulse-green 0.8s ease-in-out;
        }
        
        .pulse-lose {
            animation: pulse-red 0.8s ease-in-out;
        }
        
        @keyframes pulse-green {
            0%, 100% { transform: scale(1); box-shadow: 0 0 0 0 rgba(34, 197, 94, 0.7); }
            50% { transform: scale(1.05); box-shadow: 0 0 0 10px rgba(34, 197, 94, 0); }
        }
        
        @keyframes pulse-red {
            0%, 100% { transform: scale(1); box-shadow: 0 0 0 0 rgba(239, 68, 68, 0.7); }
            50% { transform: scale(1.05); box-shadow: 0 0 0 10px rgba(239, 68, 68, 0); }
        }
        
        .hover-lift:hover {
            transform: translateY(-4px);
            box-shadow: 0 10px 25px rgba(0,0,0,0.15);
        }
    </style>
</head>
<body class="bg-gradient-to-br from-amber-50 to-orange-100 min-h-screen">
    <!-- Header -->
    <header class="bg-white shadow-sm border-b-2 border-amber-200">
        <div class="max-w-6xl mx-auto px-4 py-4">
            <h1 class="text-2xl font-bold text-amber-800 text-center">🌳 Holzarten-Eigenschaften-Quartett</h1>
        </div>
    </header>

    <!-- Game Setup Modal -->
    <div id="setupModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
        <div class="bg-white rounded-xl p-8 max-w-md w-full mx-4 shadow-2xl">
            <h2 class="text-2xl font-bold text-amber-800 mb-6 text-center">Spiel starten</h2>
            <div class="space-y-4">
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">Dein Name:</label>
                    <input type="text" id="playerName" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-amber-500 focus:border-transparent" placeholder="Spielername eingeben">
                </div>
                <button onclick="startGame()" class="w-full bg-amber-600 hover:bg-amber-700 text-white font-semibold py-3 px-6 rounded-lg transition-colors">
                    🎮 Spiel starten
                </button>
            </div>
        </div>
    </div>

    <!-- Game Container -->
    <div id="gameContainer" class="hidden max-w-6xl mx-auto px-4 py-6">
        <!-- Score Panel -->
        <div class="bg-white rounded-xl shadow-lg p-4 mb-6 mx-auto" style="max-width: 435px;">
            <div class="flex justify-between items-center">
                <div class="text-center">
                    <p id="playerNameDisplay" class="text-xl font-bold text-amber-600"></p>
                    <p class="text-sm text-gray-500">Karten: <span id="playerCards" class="font-semibold">15</span></p>
                </div>
                <div class="text-center">
                    <p class="text-xl font-bold text-red-600">Computer</p>
                    <p class="text-sm text-gray-500">Karten: <span id="aiCards" class="font-semibold">15</span></p>
                </div>
            </div>
        </div>

        <!-- Game Board -->
        <div class="flex justify-center gap-4 mb-6">
            <!-- Player Card -->
            <div class="text-center">
                <h3 class="text-xl font-semibold text-gray-700 mb-4">Deine Karte</h3>
                <div id="playerCard" class="relative w-64 h-80 perspective-1000">
                    <!-- Card content will be inserted here -->
                </div>
            </div>

            <!-- AI Card -->
            <div class="text-center">
                <h3 class="text-xl font-semibold text-gray-700 mb-4">KI Karte</h3>
                <div id="aiCard" class="relative w-40 h-80 perspective-1000">
                    <!-- Card content will be inserted here -->
                </div>
            </div>
        </div>

        <!-- Game Controls -->
        <div id="gameControls" class="bg-white rounded-xl shadow-lg p-6 text-center mx-auto" style="max-width: 435px;">
            <div id="nextRoundButton" class="hidden mb-4">
                <button onclick="nextRound()" class="bg-amber-600 hover:bg-amber-700 text-white font-bold py-2 px-8 rounded-lg transition-colors">
                    ➡️ Nächster Zug
                </button>
            </div>
            <div id="attributeSelection" class="space-y-4">
                <h3 class="text-sm font-semibold text-gray-700 mb-4">Klicke auf eine Eigenschaft deiner Karte!</h3>
            </div>
        </div>

        <!-- Result Display -->
        <div id="resultDisplay" class="hidden bg-white rounded-xl shadow-lg p-3 mt-6 text-center mx-auto" style="max-width: 435px;">
            <h3 id="resultText" class="text-xl font-bold mb-2"></h3>
            <p id="resultDetails" class="text-gray-600"></p>
        </div>
    </div>

    <!-- Game Over Modal -->
    <div id="gameOverModal" class="hidden fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
        <div class="bg-white rounded-xl p-8 max-w-md w-full mx-4 shadow-2xl text-center">
            <h2 id="gameOverTitle" class="text-3xl font-bold mb-4"></h2>
            <p id="gameOverText" class="text-gray-600 mb-6"></p>
            <div class="space-y-3">
                <button onclick="showLeaderboard()" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-semibold py-3 px-6 rounded-lg transition-colors">
                    🏆 Bestenliste anzeigen
                </button>
                <button onclick="resetGame()" class="w-full bg-amber-600 hover:bg-amber-700 text-white font-semibold py-3 px-6 rounded-lg transition-colors">
                    🔄 Neues Spiel
                </button>
            </div>
        </div>
    </div>

    <!-- Leaderboard Modal -->
    <div id="leaderboardModal" class="hidden fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
        <div class="bg-white rounded-xl p-8 max-w-md w-full mx-4 shadow-2xl">
            <h2 class="text-2xl font-bold text-amber-800 mb-6 text-center">🏆 Bestenliste</h2>
            <div id="leaderboardList" class="space-y-2 mb-6">
                <!-- Leaderboard entries will be inserted here -->
            </div>
            <button onclick="closeLeaderboard()" class="w-full bg-gray-600 hover:bg-gray-700 text-white font-semibold py-3 px-6 rounded-lg transition-colors">
                Schließen
            </button>
        </div>
    </div>

    <script>
        // Game data
        const woodCards = [
            {name: "Eiche", dk: 2, haerte: 85, quellenSchwindenRadial: 4.0, dichte: 650, gewicht: 12000},
            {name: "Buche", dk: 5, haerte: 80, quellenSchwindenRadial: 5.8, dichte: 720, gewicht: 14000},
            {name: "Kiefer", dk: 3, haerte: 40, quellenSchwindenRadial: 3.5, dichte: 520, gewicht: 11000},
            {name: "Fichte", dk: 4, haerte: 35, quellenSchwindenRadial: 3.6, dichte: 450, gewicht: 11000},
            {name: "Tanne", dk: 4, haerte: 38, quellenSchwindenRadial: 3.5, dichte: 460, gewicht: 10000},
            {name: "Lärche", dk: 3, haerte: 55, quellenSchwindenRadial: 3.7, dichte: 590, gewicht: 12000},
            {name: "Birke", dk: 5, haerte: 60, quellenSchwindenRadial: 5.4, dichte: 650, gewicht: 13000},
            {name: "Ahorn", dk: 5, haerte: 75, quellenSchwindenRadial: 4.8, dichte: 630, gewicht: 12500},
            {name: "Esche", dk: 5, haerte: 90, quellenSchwindenRadial: 4.9, dichte: 700, gewicht: 13000},
            {name: "Kirsche", dk: 3, haerte: 70, quellenSchwindenRadial: 4.0, dichte: 600, gewicht: 11500},
            {name: "Nussbaum", dk: 3, haerte: 65, quellenSchwindenRadial: 4.0, dichte: 640, gewicht: 12000},
            {name: "Douglasie", dk: 3, haerte: 45, quellenSchwindenRadial: 3.5, dichte: 510, gewicht: 12000},
            {name: "Zeder", dk: 2, haerte: 42, quellenSchwindenRadial: 2.8, dichte: 480, gewicht: 9500},
            {name: "Mahagoni", dk: 2, haerte: 55, quellenSchwindenRadial: 3.0, dichte: 550, gewicht: 10500},
            {name: "Teak", dk: 1, haerte: 60, quellenSchwindenRadial: 2.5, dichte: 650, gewicht: 11000},
            {name: "Bambus", dk: 4, haerte: 50, quellenSchwindenRadial: 3.0, dichte: 400, gewicht: 18000},
            {name: "Pappel", dk: 5, haerte: 25, quellenSchwindenRadial: 4.5, dichte: 380, gewicht: 9000},
            {name: "Weide", dk: 5, haerte: 30, quellenSchwindenRadial: 4.0, dichte: 420, gewicht: 8000},
            {name: "Linde", dk: 5, haerte: 35, quellenSchwindenRadial: 3.8, dichte: 530, gewicht: 9000},
            {name: "Ulme", dk: 3, haerte: 75, quellenSchwindenRadial: 4.5, dichte: 680, gewicht: 11500},
            {name: "Kastanie", dk: 2, haerte: 50, quellenSchwindenRadial: 3.5, dichte: 600, gewicht: 10000},
            {name: "Robinie", dk: 1, haerte: 80, quellenSchwindenRadial: 3.0, dichte: 730, gewicht: 15000},
            {name: "Hemlock", dk: 4, haerte: 40, quellenSchwindenRadial: 3.6, dichte: 500, gewicht: 10000},
            {name: "Zypresse", dk: 2, haerte: 48, quellenSchwindenRadial: 3.0, dichte: 520, gewicht: 9000},
            {name: "Redwood", dk: 2, haerte: 35, quellenSchwindenRadial: 2.8, dichte: 400, gewicht: 8500},
            {name: "Hickory", dk: 3, haerte: 95, quellenSchwindenRadial: 5.5, dichte: 750, gewicht: 14000},
            {name: "Wenge", dk: 2, haerte: 88, quellenSchwindenRadial: 3.8, dichte: 880, gewicht: 16000},
            {name: "Zebrano", dk: 3, haerte: 70, quellenSchwindenRadial: 4.0, dichte: 750, gewicht: 13000},
            {name: "Palisander", dk: 2, haerte: 82, quellenSchwindenRadial: 3.0, dichte: 850, gewicht: 16000},
            {name: "Ebony", dk: 1, haerte: 98, quellenSchwindenRadial: 3.2, dichte: 1200, gewicht: 16500}
        ];

        // Game state
        let gameState = {
            playerName: '',
            playerCards: [],
            aiCards: [],
            currentPlayerCard: null,
            currentAiCard: null,
            playerTurn: true,
            gameOver: false,
            roundResult: null,
            roundInProgress: false
        };

        // Attribute icons and names
        const attributeInfo = {
            dk: {icon: '🛡️', name: 'Dauerhaftigkeit (DK)', color: 'blue'},
            haerte: {icon: '💎', name: 'Druckfestigkeit (N/mm²)', color: 'blue'},
            quellenSchwindenRadial: {icon: '↔️', name: 'Quellen/Schwinden radial', color: 'blue'},
            dichte: {icon: '⚖️', name: 'Rohdichte (kg/m³)', color: 'blue'},
            gewicht: {icon: '🏋️', name: 'E-Modul (N/mm²)', color: 'blue'}
        };

        function shuffleArray(array) {
            const shuffled = [...array];
            for (let i = shuffled.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]];
            }
            return shuffled;
        }

        function startGame() {
            const playerName = document.getElementById('playerName').value.trim();
            if (!playerName) {
                alert('Bitte gib deinen Namen ein!');
                return;
            }

            gameState.playerName = playerName;
            document.getElementById('playerNameDisplay').textContent = playerName;

            // Shuffle and distribute cards
            const shuffledCards = shuffleArray(woodCards);
            gameState.playerCards = shuffledCards.slice(0, 15);
            gameState.aiCards = shuffledCards.slice(15, 30);

            // Set current cards
            gameState.currentPlayerCard = gameState.playerCards[0];
            gameState.currentAiCard = gameState.aiCards[0];

            // Show game
            document.getElementById('setupModal').classList.add('hidden');
            document.getElementById('gameContainer').classList.remove('hidden');

            updateDisplay();
            displayCards();
        }

        function createCard(cardData, isRevealed = true, isPlayer = true) {
            const card = document.createElement('div');
            card.className = `card-3d w-full h-full relative cursor-pointer transition-all duration-300 ${isPlayer ? 'hover-lift' : ''}`;
            
            if (!isRevealed) {
                card.classList.add('card-flipped');
            }

            // Card front (revealed)
            const cardFront = document.createElement('div');
            cardFront.className = `card-front bg-white rounded-xl shadow-lg border-2 border-amber-200 ${isPlayer ? 'p-4' : 'p-2'}`;
            
            const attributeButtons = isPlayer ? `
                <div class="space-y-3">
                    <button onclick="selectAttribute('dk')" class="w-full flex justify-between items-center py-1 px-2 bg-blue-50 hover:bg-blue-100 rounded-lg transition-colors cursor-pointer">
                        <span class="flex items-center text-left"><span class="mr-2">🛡️</span><span class="text-sm">Dauerhaftigkeit (DK)</span></span>
                        <span class="font-bold text-blue-600">${cardData.dk}</span>
                    </button>
                    <button onclick="selectAttribute('haerte')" class="w-full flex justify-between items-center py-1 px-2 bg-blue-50 hover:bg-blue-100 rounded-lg transition-colors cursor-pointer">
                        <span class="flex items-center text-left"><span class="mr-2">💎</span><span class="text-sm">Druckfestigkeit (N/mm²)</span></span>
                        <span class="font-bold text-blue-600">${cardData.haerte}</span>
                    </button>
                    <button onclick="selectAttribute('quellenSchwindenRadial')" class="w-full flex justify-between items-center py-1 px-2 bg-blue-50 hover:bg-blue-100 rounded-lg transition-colors cursor-pointer">
                        <span class="flex items-center text-left"><span class="mr-2">↔️</span><span class="text-xs">Quellen/Schwinden r.</span></span>
                        <span class="font-bold text-blue-600">${cardData.quellenSchwindenRadial}</span>
                    </button>
                    <button onclick="selectAttribute('dichte')" class="w-full flex justify-between items-center py-1 px-2 bg-blue-50 hover:bg-blue-100 rounded-lg transition-colors cursor-pointer">
                        <span class="flex items-center text-left"><span class="mr-2">⚖️</span><span class="text-sm">Rohdichte (kg/m³)</span></span>
                        <span class="font-bold text-blue-600">${cardData.dichte}</span>
                    </button>
                    <button onclick="selectAttribute('gewicht')" class="w-full flex justify-between items-center py-1 px-2 bg-blue-50 hover:bg-blue-100 rounded-lg transition-colors cursor-pointer">
                        <span class="flex items-center text-left"><span class="mr-2">🏋️</span><span class="text-sm">E-Modul (N/mm²)</span></span>
                        <span class="font-bold text-blue-600">${cardData.gewicht}</span>
                    </button>
                </div>
            ` : `
                <div class="space-y-1">
                    <div class="flex justify-between items-center py-1 px-2 bg-blue-50 rounded text-xs">
                        <span class="flex items-center"><span class="mr-1">🛡️</span>Dauerhaftigkeit (DK)</span>
                        <span class="font-bold text-blue-600">${cardData.dk}</span>
                    </div>
                    <div class="flex justify-between items-center py-1 px-2 bg-blue-50 rounded text-xs">
                        <span class="flex items-center"><span class="mr-1">💎</span>Druckfestigkeit</span>
                        <span class="font-bold text-blue-600">${cardData.haerte}</span>
                    </div>
                    <div class="flex justify-between items-center py-1 px-2 bg-blue-50 rounded text-xs">
                        <span class="flex items-center"><span class="mr-1">↔️</span><span style="font-size: 0.8em;">Quellen/Schwinden r.</span></span>
                        <span class="font-bold text-blue-600">${cardData.quellenSchwindenRadial}</span>
                    </div>
                    <div class="flex justify-between items-center py-1 px-2 bg-blue-50 rounded text-xs">
                        <span class="flex items-center"><span class="mr-1">⚖️</span>Rohdichte</span>
                        <span class="font-bold text-blue-600">${cardData.dichte}</span>
                    </div>
                    <div class="flex justify-between items-center py-1 px-2 bg-blue-50 rounded text-xs">
                        <span class="flex items-center"><span class="mr-1">🏋️</span>E-Modul</span>
                        <span class="font-bold text-blue-600">${cardData.gewicht}</span>
                    </div>
                </div>
            `;
            
            cardFront.innerHTML = `
                <div class="text-center mb-2">
                    <div class="${isPlayer ? 'text-4xl mb-2' : 'text-2xl mb-1'}">🌳</div>
                    <h3 class="${isPlayer ? 'text-xl' : 'text-sm'} font-bold text-amber-800">${cardData.name}</h3>
                </div>
                ${attributeButtons}
            `;

            // Card back (hidden)
            const cardBack = document.createElement('div');
            cardBack.className = 'card-back bg-gradient-to-br from-amber-600 to-orange-700 rounded-xl shadow-lg border-2 border-amber-300 p-4 flex items-center justify-center relative overflow-hidden';
            cardBack.innerHTML = `
                <div class="wood-texture absolute inset-0"></div>
                <div class="text-center z-10">
                    <div class="text-6xl mb-4">🌳</div>
                    <h3 class="text-xl font-bold text-white opacity-90">Holzquartett</h3>
                </div>
            `;

            card.appendChild(cardFront);
            card.appendChild(cardBack);
            
            return card;
        }

        function displayCards() {
            const playerCardContainer = document.getElementById('playerCard');
            const aiCardContainer = document.getElementById('aiCard');

            playerCardContainer.innerHTML = '';
            aiCardContainer.innerHTML = '';

            const playerCard = createCard(gameState.currentPlayerCard, true, true);
            const aiCard = createCard(gameState.currentAiCard, false, false);

            playerCardContainer.appendChild(playerCard);
            aiCardContainer.appendChild(aiCard);
        }

        function selectAttribute(attribute) {
            // Prevent multiple selections
            if (gameState.roundInProgress) return;
            gameState.roundInProgress = true;

            const playerValue = gameState.currentPlayerCard[attribute];
            const aiValue = gameState.currentAiCard[attribute];

            // Reveal AI card immediately
            const aiCardElement = document.getElementById('aiCard').querySelector('.card-3d');
            aiCardElement.classList.remove('card-flipped');

            // Disable all attribute buttons
            const attributeButtons = document.querySelectorAll('#playerCard button');
            attributeButtons.forEach(button => {
                button.disabled = true;
                button.classList.remove('hover:bg-blue-100', 'cursor-pointer');
                button.classList.add('cursor-not-allowed', 'opacity-50');
            });

            // Determine winner and highlight winning attribute
            let winner = '';
            let resultText = '';
            let resultClass = '';

            let playerWins;
            let comparisonChar = '>';
            if (attribute === 'quellenSchwindenRadial' || attribute === 'dk') {
                // For these attributes, lower is better
                comparisonChar = '<';
                if (playerValue < aiValue) playerWins = true;
                else if (aiValue < playerValue) playerWins = false;
                else playerWins = 'tie';
            } else {
                // For all other attributes, higher is better
                if (playerValue > aiValue) playerWins = true;
                else if (aiValue > playerValue) playerWins = false;
                else playerWins = 'tie';
            }

            if (playerWins === true) {
                winner = 'player';
                resultText = `🎉 Du gewinnst! ${attributeInfo[attribute].name}: ${playerValue} ${comparisonChar} ${aiValue}`;
                resultClass = 'text-green-600';
                
                // Highlight winning attribute in green on both cards
                highlightAttribute(attribute, 'win');
                
                // Remove current cards from top of stacks
                gameState.playerCards.shift(); // Remove player's current card
                gameState.aiCards.shift(); // Remove AI's current card
                
                // Add both cards to bottom of player's stack (player's card first, then AI's card)
                gameState.playerCards.push(gameState.currentPlayerCard);
                gameState.playerCards.push(gameState.currentAiCard);
                
                // Add win animation
                document.getElementById('playerCard').querySelector('.card-3d').classList.add('pulse-win');
            } else if (playerWins === false) {
                winner = 'ai';
                resultText = `😞 KI gewinnt! ${attributeInfo[attribute].name}: ${aiValue} ${comparisonChar} ${playerValue}`;
                resultClass = 'text-red-600';
                
                // Highlight winning attribute in red on both cards
                highlightAttribute(attribute, 'lose');
                
                // Remove current cards from top of stacks
                gameState.playerCards.shift(); // Remove player's current card
                gameState.aiCards.shift(); // Remove AI's current card
                
                // Add both cards to bottom of AI's stack (AI's card first, then player's card)
                gameState.aiCards.push(gameState.currentAiCard);
                gameState.aiCards.push(gameState.currentPlayerCard);
                
                // Add lose animation
                document.getElementById('playerCard').querySelector('.card-3d').classList.add('pulse-lose');
            } else {
                winner = 'tie';
                resultText = `🤝 Unentschieden! ${attributeInfo[attribute].name}: ${playerValue} = ${aiValue}`;
                resultClass = 'text-yellow-600';
                
                // Highlight tie attribute in yellow on both cards
                highlightAttribute(attribute, 'tie');
                
                // In case of tie, cards stay in their respective stacks but move to bottom
                gameState.playerCards.shift();
                gameState.aiCards.shift();
                gameState.playerCards.push(gameState.currentPlayerCard);
                gameState.aiCards.push(gameState.currentAiCard);
            }

            // Show result
            document.getElementById('resultText').textContent = resultText;
            document.getElementById('resultText').className = `text-2xl font-bold mb-4 ${resultClass}`;
            document.getElementById('resultDetails').textContent = `${gameState.currentPlayerCard.name} vs ${gameState.currentAiCard.name}`;
            document.getElementById('resultDisplay').classList.remove('hidden');

            // Show next round button
            document.getElementById('nextRoundButton').classList.remove('hidden');

            gameState.roundResult = winner;
            gameState.selectedAttribute = attribute;
            updateDisplay();
            checkGameOver();
        }

        function highlightAttribute(attribute, result) {
            const playerCard = document.getElementById('playerCard');
            const aiCard = document.getElementById('aiCard');
            
            // Define colors based on result for player and AI cards
            let playerBgColor = '';
            let aiBgColor = '';
            
            if (result === 'win') {
                playerBgColor = 'bg-green-200';  // Player wins - green
                aiBgColor = 'bg-gray-200';       // AI loses - gray
            } else if (result === 'lose') {
                playerBgColor = 'bg-red-200';    // Player loses - red
                aiBgColor = 'bg-green-200';      // AI wins - green
            } else {
                playerBgColor = 'bg-yellow-200'; // Tie - yellow for both
                aiBgColor = 'bg-yellow-200';
            }
            
            // Find and highlight the attribute buttons/divs
            const attributeSelectors = {
                'dk': 0,
                'haerte': 1,
                'quellenSchwindenRadial': 2,
                'dichte': 3,
                'gewicht': 4
            };
            
            const attributeIndex = attributeSelectors[attribute];
            
            // Highlight in player card
            const playerAttributes = playerCard.querySelectorAll('.space-y-3 > *');
            if (playerAttributes[attributeIndex]) {
                playerAttributes[attributeIndex].className = playerAttributes[attributeIndex].className.replace('bg-blue-50', playerBgColor);
            }
            
            // Highlight in AI card
            const aiAttributes = aiCard.querySelectorAll('.space-y-1 > *');
            if (aiAttributes[attributeIndex]) {
                aiAttributes[attributeIndex].className = aiAttributes[attributeIndex].className.replace('bg-blue-50', aiBgColor);
            }
        }

        function nextRound() {
            // Remove animations
            document.getElementById('playerCard').querySelector('.card-3d').classList.remove('pulse-win', 'pulse-lose');

            // Check if game is over
            if (gameState.playerCards.length === 0 || gameState.aiCards.length === 0) {
                endGame();
                return;
            }

            // Set new current cards
            gameState.currentPlayerCard = gameState.playerCards[0];
            gameState.currentAiCard = gameState.aiCards[0];

            // Reset round state
            gameState.roundInProgress = false;

            // Reset UI
            document.getElementById('attributeSelection').classList.remove('hidden');
            document.getElementById('nextRoundButton').classList.add('hidden');
            document.getElementById('resultDisplay').classList.add('hidden');

            displayCards();
            updateDisplay();
        }

        function updateDisplay() {
            document.getElementById('playerCards').textContent = gameState.playerCards.length;
            document.getElementById('aiCards').textContent = gameState.aiCards.length;
        }

        function checkGameOver() {
            if (gameState.playerCards.length === 0 || gameState.aiCards.length === 0) {
                setTimeout(endGame, 2000);
            }
        }

        function endGame() {
            const playerWon = gameState.playerCards.length > 0;
            
            document.getElementById('gameOverTitle').textContent = playerWon ? '🎉 Glückwunsch!' : '😞 Spiel verloren';
            document.getElementById('gameOverTitle').className = `text-3xl font-bold mb-4 ${playerWon ? 'text-green-600' : 'text-red-600'}`;
            document.getElementById('gameOverText').textContent = playerWon ? 
                `Du hast gewonnen, ${gameState.playerName}! Du hast alle Karten gesammelt.` :
                `Die KI hat gewonnen. Versuche es nochmal!`;

            // Save to leaderboard if player won
            if (playerWon) {
                saveToLeaderboard(gameState.playerName, gameState.playerCards.length);
            }

            document.getElementById('gameOverModal').classList.remove('hidden');
        }

        function saveToLeaderboard(playerName, score) {
            let leaderboard = JSON.parse(localStorage.getItem('holzquartett-leaderboard') || '[]');
            leaderboard.push({
                name: playerName,
                score: score,
                date: new Date().toLocaleDateString('de-DE')
            });
            leaderboard.sort((a, b) => b.score - a.score);
            leaderboard = leaderboard.slice(0, 10); // Keep top 10
            localStorage.setItem('holzquartett-leaderboard', JSON.stringify(leaderboard));
        }

        function showLeaderboard() {
            const leaderboard = JSON.parse(localStorage.getItem('holzquartett-leaderboard') || '[]');
            const leaderboardList = document.getElementById('leaderboardList');
            
            if (leaderboard.length === 0) {
                leaderboardList.innerHTML = '<p class="text-gray-500 text-center">Noch keine Einträge</p>';
            } else {
                leaderboardList.innerHTML = leaderboard.map((entry, index) => `
                    <div class="flex justify-between items-center p-3 bg-gray-50 rounded-lg">
                        <span class="font-semibold">${index + 1}. ${entry.name}</span>
                        <span class="text-sm text-gray-600">${entry.score} Karten - ${entry.date}</span>
                    </div>
                `).join('');
            }
            
            document.getElementById('gameOverModal').classList.add('hidden');
            document.getElementById('leaderboardModal').classList.remove('hidden');
        }

        function closeLeaderboard() {
            document.getElementById('leaderboardModal').classList.add('hidden');
            document.getElementById('gameOverModal').classList.remove('hidden');
        }

        function resetGame() {
            // Reset game state
            gameState = {
                playerName: '',
                playerCards: [],
                aiCards: [],
                currentPlayerCard: null,
                currentAiCard: null,
                playerTurn: true,
                gameOver: false,
                roundResult: null,
                roundInProgress: false
            };

            // Reset UI
            document.getElementById('gameOverModal').classList.add('hidden');
            document.getElementById('leaderboardModal').classList.add('hidden');
            document.getElementById('gameContainer').classList.add('hidden');
            document.getElementById('setupModal').classList.remove('hidden');
            document.getElementById('playerName').value = '';
        }
    </script>
</body>
</html>






