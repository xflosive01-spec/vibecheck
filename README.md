<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vibe Check</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js"></script>
    <script src="https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/howler/2.2.3/howler.min.js"></script>
    <script src="https://www.paypalobjects.com/api/checkout.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@700&display=swap');
        .vibe-box {
            background-color: #ffc43a;
            border-radius: 12px;
            padding: 4px 12px;
            display: inline-flex;
            align-items: center;
        }
        .vibe-text {
            font-family: 'Poppins', sans-serif;
            font-size: 2rem;
            color: black;
            font-weight: 700;
        }
        .vibe-checkmark {
            position: absolute;
            bottom: -5px;
            right: -5px;
            width: 20px;
            height: 20px;
        }
        #paypal-button-container-starter, #paypal-button-container-unlimited {
            margin-top: 1rem;
        }
    </style>
</head>
<body class="bg-gray-900 text-white font-sans transition-colors duration-500">
    <!-- Main App Container - Optimized for mobile-first layout -->
    <div id="app-container" class="min-h-screen flex flex-col items-center justify-center p-4 sm:p-6 md:p-8 lg:p-12 transition-colors duration-500">
        <!-- Header -->
        <header class="w-full flex justify-between items-center p-4 max-w-4xl mx-auto">
            <div class="flex items-center space-x-4">
                <div class="relative w-24 h-12 flex items-center justify-center">
                    <div class="vibe-box">
                        <span class="vibe-text">vibe</span>
                    </div>
                    <svg class="vibe-checkmark" viewBox="0 0 512 512" fill="black">
                        <path d="M470.6 105.4c12.5 12.5 12.5 32.8 0 45.3l-256 256c-12.5 12.5-32.8 12.5-45.3 0l-128-128c-12.5-12.5-12.5-32.8 0-45.3s32.8-12.5 45.3 0L192 338.7 425.4 105.4c12.5-12.5 32.8-12.5 45.3 0z"/>
                    </svg>
                </div>
            </div>
            <div class="flex items-center space-x-2">
                <span class="text-xs sm:text-sm">User ID: <span id="user-id"></span></span>
                <button id="settings-btn" class="p-2 rounded-full hover:bg-gray-700 transition-colors">
                    <svg class="w-6 h-6 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.942 3.318.815 2.376 2.376a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.942 1.543-.815 3.318-2.376 2.376a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.942-3.318-.815-2.376-2.376a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.942-1.543.815-3.318 2.376-2.376a1.724 1.724 0 002.572-1.065z"></path>
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"></path>
                    </svg>
                </button>
            </div>
        </header>

        <!-- Main Content Area -->
        <main id="main-content" class="flex flex-col items-center justify-center flex-grow w-full max-w-4xl p-4 sm:p-6 md:p-8">
            <!-- Vibe Check Display -->
            <div id="vibe-container" class="relative w-full text-center rounded-lg p-6 md:p-10 shadow-lg min-h-[300px] flex flex-col justify-center items-center transition-all duration-300">
                <div id="vibe-message" class="text-2xl sm:text-3xl font-bold mb-4">
                    The only bad vibe is the one you ignore.
                </div>
                <div id="vibe-content" class="text-lg sm:text-xl text-gray-300 mb-6">
                    Ready to vibe check?
                </div>
                <div id="visual-note-container" class="w-full max-w-xs mb-4 hidden">
                    <video id="vibe-video" controls class="w-full rounded-lg shadow-md"></video>
                    <audio id="vibe-audio" controls class="w-full"></audio>
                </div>
                <button id="vibe-check-btn" class="px-8 py-4 bg-yellow-500 text-gray-900 font-bold text-lg rounded-full shadow-xl hover:bg-yellow-400 transition-colors transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-yellow-500 focus:ring-opacity-50">
                    Vibe Check
                </button>
                <div id="daily-check-count" class="text-sm mt-4 text-gray-400">Vibe Checks Today: 0/2</div>

                <div id="vibe-controls" class="mt-8 flex flex-col items-center space-y-4 hidden">
                    <div class="text-md">Rate this Vibe:</div>
                    <div class="flex space-x-4">
                        <button class="vibe-rating-btn" data-rating="good">👍</button>
                        <button class="vibe-rating-btn" data-rating="bad">👎</button>
                    </div>
                    <div id="social-share" class="flex space-x-4 mt-4">
                        <button id="share-btn" class="p-3 bg-blue-600 rounded-full text-white hover:bg-blue-500 transition-colors">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" viewBox="0 0 24 24" fill="currentColor"><path d="M18 16a2 2 0 0 1-2 2H8a2 2 0 0 1-2-2v-3a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h3a2 2 0 0 1 2 2v3a2 2 0 0 1 2 2v3zm-4-3v-3a2 2 0 0 1 2-2h3a2 2 0 0 1 2 2v3a2 2 0 0 1-2 2h-3a2 2 0 0 1-2-2zm-6 0v-3a2 2 0 0 1 2-2h3a2 2 0 0 1 2 2v3a2 2 0 0 1-2 2H8a2 2 0 0 1-2-2z"/></svg>
                        </button>
                        <button id="tweet-btn" class="p-3 bg-blue-400 rounded-full text-white hover:bg-blue-300 transition-colors">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" viewBox="0 0 24 24" fill="currentColor"><path d="M23.953 4.57a10 10 0 0 1-2.825.775 4.958 4.958 0 0 0 2.163-2.723c-.951.565-2.005.976-3.127 1.192a4.916 4.916 0 0 0-8.358 4.482A13.923 13.923 0 0 1 3.28 2.296a4.918 4.918 0 0 0 1.523 6.574 4.903 4.903 0 0 1-2.229-.616v.06a4.92 4.92 0 0 0 3.947 4.827 4.995 4.995 0 0 1-2.227.085 4.92 4.92 0 0 0 4.6 3.406 9.839 9.839 0 0 1-6.102 2.105c-.39-.023-.777-.047-1.164-.084a13.93 13.93 0 0 0 7.548 2.212c9.052 0 13.992-7.496 13.992-13.984l-.01-.679a9.92 9.92 0 0 0 2.45-2.544z"/></svg>
                        </button>
                    </div>
                </div>
            </div>

            <!-- Dashboard Section -->
            <div id="dashboard" class="mt-8 w-full max-w-4xl bg-gray-800 p-6 rounded-lg shadow-lg hidden">
                <h2 class="text-2xl font-bold mb-4">Vibe Dashboard</h2>
                <div id="vibe-trends" class="h-48 bg-gray-700 rounded-lg p-4">
                    <!-- Simple Bar Chart Placeholder -->
                    <h3 class="text-xl text-gray-300 mb-2">Vibe Trends (Last 10 Days)</h3>
                    <div id="chart-container" class="h-32 flex items-end space-x-2"></div>
                </div>
            </div>

            <!-- Recorder Section -->
            <div id="recorder-section" class="mt-8 w-full max-w-4xl bg-gray-800 p-6 rounded-lg shadow-lg">
                <h2 class="text-2xl font-bold mb-4">Record a Personal Vibe Note</h2>
                <div class="flex flex-col md:flex-row space-y-4 md:space-y-0 md:space-x-4">
                    <button id="video-start-btn" class="flex-1 px-6 py-3 bg-red-600 text-white rounded-lg font-bold hover:bg-red-500 transition-colors">Record Video Note</button>
                    <button id="audio-start-btn" class="flex-1 px-6 py-3 bg-blue-600 text-white rounded-lg font-bold hover:bg-blue-500 transition-colors">Record Audio Note</button>
                </div>
                <div id="media-recorder-container" class="mt-4 hidden flex flex-col items-center">
                    <video id="preview-video" class="w-full max-w-md rounded-lg shadow-md mb-2"></video>
                    <div class="flex space-x-2">
                        <button id="record-stop-btn" class="px-4 py-2 bg-red-500 rounded-lg text-white font-bold">Stop Recording</button>
                        <button id="record-save-btn" class="px-4 py-2 bg-green-500 rounded-lg text-white font-bold hidden">Save Note</button>
                    </div>
                </div>
            </div>
        </main>
    </div>

    <!-- Modals -->
    <!-- Subscription Modal -->
    <div id="subscription-modal" class="fixed inset-0 bg-gray-900 bg-opacity-75 flex items-center justify-center p-4 hidden">
        <div class="bg-gray-800 p-6 rounded-lg shadow-xl w-full max-w-lg text-center relative">
            <button id="close-sub-modal" class="absolute top-3 right-3 text-gray-400 hover:text-white transition-colors">
                <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path></svg>
            </button>
            <h3 class="text-2xl font-bold mb-4">Choose Your Vibe Tier</h3>
            <div class="flex flex-col md:flex-row justify-center space-y-4 md:space-y-0 md:space-x-4">
                <!-- Starter Tier -->
                <div class="bg-gray-900 p-6 rounded-lg border-2 border-yellow-500 flex-1 flex flex-col">
                    <h4 class="text-xl font-bold mb-2">Starter</h4>
                    <p class="text-3xl font-extrabold mb-2">$0.99<span class="text-sm font-normal">/week</span></p>
                    <p class="text-sm text-gray-400 mb-4">Unlimited Vibe Checks</p>
                    <div id="paypal-button-container-starter"></div>
                </div>
                <!-- Unlimited Tier -->
                <div class="bg-gray-900 p-6 rounded-lg border-2 border-yellow-500 flex-1 flex flex-col">
                    <h4 class="text-xl font-bold mb-2">Unlimited</h4>
                    <p class="text-3xl font-extrabold mb-2">$3.00<span class="text-sm font-normal">/week</span></p>
                    <p class="text-sm text-gray-400 mb-4">Unlimited Vibe Checks & Exclusive Content</p>
                    <div id="paypal-button-container-unlimited"></div>
                </div>
            </div>
        </div>
    </div>
    <!-- Settings Modal -->
    <div id="settings-modal" class="fixed inset-0 bg-gray-900 bg-opacity-75 flex items-center justify-center p-4 hidden">
        <div class="bg-gray-800 p-6 rounded-lg shadow-xl w-full max-w-lg text-center relative">
            <button id="close-settings-modal" class="absolute top-3 right-3 text-gray-400 hover:text-white transition-colors">
                <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path></svg>
            </button>
            <h3 class="text-2xl font-bold mb-4">App Settings</h3>
            <h4 class="text-xl font-bold mb-2">Choose Your Color Scheme</h4>
            <div class="flex justify-center space-x-4 mt-4">
                <button data-theme="indigo" class="theme-btn w-12 h-12 rounded-full bg-indigo-600 shadow-lg hover:ring-4 hover:ring-indigo-500 focus:outline-none"></button>
                <button data-theme="sunrise" class="theme-btn w-12 h-12 rounded-full bg-orange-500 shadow-lg hover:ring-4 hover:ring-orange-400 focus:outline-none"></button>
                <button data-theme="aqua" class="theme-btn w-12 h-12 rounded-full bg-teal-500 shadow-lg hover:ring-4 hover:ring-teal-400 focus:outline-none"></button>
            </div>
            <div class="mt-8">
                <h4 class="text-xl font-bold mb-2">Current Subscription</h4>
                <div id="subscription-status" class="text-lg text-gray-300">Free Tier</div>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, onAuthStateChanged, signInWithCustomToken } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, getDoc, setDoc, onSnapshot } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
        import { getFunctions, httpsCallable } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-functions.js";

        // Global variables provided by the environment
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {
            projectId: "vibecheck-app-123",
            apiKey: "vibecheck-api-key",
            authDomain: "vibecheck-app-123.firebaseapp.com"
        };
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        // Firebase initialization
        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);
        const auth = getAuth(app);
        const functions = getFunctions(app);

        // --- DOM Elements ---
        const vibeCheckBtn = document.getElementById('vibe-check-btn');
        const vibeContainer = document.getElementById('vibe-container');
        const vibeMessage = document.getElementById('vibe-message');
        const vibeContent = document.getElementById('vibe-content');
        const dailyCheckCount = document.getElementById('daily-check-count');
        const dashboard = document.getElementById('dashboard');
        const chartContainer = document.getElementById('chart-container');
        const subscriptionModal = document.getElementById('subscription-modal');
        const settingsModal = document.getElementById('settings-modal');
        const closeSubModalBtn = document.getElementById('close-sub-modal');
        const closeSettingsModalBtn = document.getElementById('close-settings-modal');
        const settingsBtn = document.getElementById('settings-btn');
        const subscriptionStatus = document.getElementById('subscription-status');
        const vibeRatingBtns = document.querySelectorAll('.vibe-rating-btn');
        const vibeControls = document.getElementById('vibe-controls');
        const shareBtn = document.getElementById('share-btn');
        const tweetBtn = document.getElementById('tweet-btn');
        const videoStartBtn = document.getElementById('video-start-btn');
        const audioStartBtn = document.getElementById('audio-start-btn');
        const mediaRecorderContainer = document.getElementById('media-recorder-container');
        const previewVideo = document.getElementById('preview-video');
        const vibeVideo = document.getElementById('vibe-video');
        const vibeAudio = document.getElementById('vibe-audio');
        const visualNoteContainer = document.getElementById('visual-note-container');
        const recordStopBtn = document.getElementById('record-stop-btn');
        const recordSaveBtn = document.getElementById('record-save-btn');
        const userIdSpan = document.getElementById('user-id');

        let mediaRecorder;
        let recordedChunks = [];
        let mediaType = '';

        // --- App State ---
        let userProfile = null;
        let isAuthReady = false;

        // --- AI Content Generation and Data ---
        async function getAIGeneratedContent(prompt) {
            const apiUrl = 'YOUR_RENDER_URL/generate-content';
            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ prompt: prompt })
                });
                const data = await response.json();
                return data.text;
            } catch (error) {
                console.error("Error fetching AI content:", error);
                return "Failed to get AI content. Try again later.";
            }
        }

        const jokes = [
            "Why don't scientists trust atoms? Because they make up everything!",
            "I'm reading a book on anti-gravity. It's impossible to put down!",
            "Why did the scarecrow win an award? Because he was outstanding in his field!"
        ];

        const quotes = [
            "The only way to do great work is to love what you do. - Steve Jobs",
            "Believe you can and you're halfway there. - Theodore Roosevelt",
            "The future belongs to those who believe in the beauty of their dreams. - Eleanor Roosevelt"
        ];

        const music = [
            { title: "Good Day", artist: "Nappy Roots", url: "https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3" },
            { title: "Walking on Sunshine", artist: "Katrina & The Waves", url: "https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3" },
            { title: "Don't Stop Me Now", artist: "Queen", url: "https://www.soundhelix.com/examples/mp3/SoundHelix-Song-3.mp3" }
        ];

        // --- Core Functions ---
        const getTodaysDate = () => {
            const today = new Date();
            return `${today.getFullYear()}-${today.getMonth() + 1}-${today.getDate()}`;
        };

        const updateDailyCount = async () => {
            if (!userProfile) return;

            const today = getTodaysDate();
            if (userProfile.lastCheckDate !== today) {
                userProfile.dailyCheckCount = 0;
                userProfile.lastCheckDate = today;
                await saveUserProfile();
            }
            dailyCheckCount.textContent = `Vibe Checks Today: ${userProfile.dailyCheckCount}/${userProfile.subscriptionStatus === 'free' ? '2' : '∞'}`;
        };

        const saveUserProfile = async () => {
            if (!userProfile) return;
            const userDocRef = doc(db, `artifacts/${appId}/users/${auth.currentUser.uid}/vibe_profile/profile`);
            await setDoc(userDocRef, userProfile, { merge: true });
        };

        const applyTheme = (theme) => {
            const body = document.body;
            body.className = `min-h-screen flex flex-col items-center justify-center p-4 transition-colors duration-500 font-sans`;
            vibeContainer.className = `relative w-full text-center rounded-lg p-6 md:p-10 shadow-lg min-h-[300px] flex flex-col justify-center items-center transition-all duration-300`;
            switch (theme) {
                case 'indigo':
                    body.classList.add('bg-indigo-900', 'text-white');
                    vibeContainer.classList.add('bg-indigo-800');
                    break;
                case 'sunrise':
                    body.classList.add('bg-orange-400', 'text-gray-900');
                    vibeContainer.classList.add('bg-orange-200');
                    break;
                case 'aqua':
                    body.classList.add('bg-teal-700', 'text-white');
                    vibeContainer.classList.add('bg-teal-600');
                    break;
                default:
                    body.classList.add('bg-gray-900', 'text-white');
                    vibeContainer.classList.add('bg-gray-800');
            }
        };

        const updateDashboardChart = () => {
            if (!userProfile || !userProfile.vibeHistory) return;

            chartContainer.innerHTML = '';
            const history = userProfile.vibeHistory.slice(-10); // Get last 10 days
            const vibeMap = { 'good': 1, 'bad': -1, 'neutral': 0 };

            history.forEach(vibe => {
                const bar = document.createElement('div');
                const height = (vibeMap[vibe.rating] + 1.5) * 20; // Scale height
                const color = vibe.rating === 'good' ? 'bg-green-500' : (vibe.rating === 'bad' ? 'bg-red-500' : 'bg-yellow-500');
                
                bar.className = `relative flex-1 rounded-t-lg transition-all duration-500 ${color}`;
                bar.style.height = `${height}px`;
                bar.style.minWidth = '20px';
                
                const dateLabel = document.createElement('div');
                dateLabel.className = 'absolute bottom-0 text-xs text-gray-400 -mb-6';
                dateLabel.textContent = new Date(vibe.date).toLocaleDateString('en-US', { month: 'numeric', day: 'numeric' });
                
                bar.appendChild(dateLabel);
                chartContainer.appendChild(bar);
            });
        };
        
        // --- Event Listeners ---
        settingsBtn.addEventListener('click', () => {
            settingsModal.classList.remove('hidden');
        });

        closeSettingsModalBtn.addEventListener('click', () => {
            settingsModal.classList.add('hidden');
        });

        document.querySelectorAll('.theme-btn').forEach(button => {
            button.addEventListener('click', async () => {
                const theme = button.getAttribute('data-theme');
                applyTheme(theme);
                if (userProfile) {
                    userProfile.theme = theme;
                    await saveUserProfile();
                }
            });
        });

        vibeCheckBtn.addEventListener('click', async () => {
            if (!userProfile) return;

            const isFreeTier = userProfile.subscriptionStatus === 'free';
            const isAtLimit = userProfile.dailyCheckCount >= 2;

            if (isFreeTier && isAtLimit) {
                subscriptionModal.classList.remove('hidden');
                return;
            }

            vibeCheckBtn.textContent = 'Checking Vibe...';
            vibeCheckBtn.disabled = true;

            let vibeType = "quote";
            let vibe = "";

            if (userProfile.personalNotes && userProfile.personalNotes.length > 0 && Math.random() < 0.2) {
                const randomNote = userProfile.personalNotes[Math.floor(Math.random() * userProfile.personalNotes.length)];
                vibeType = randomNote.type;
                vibe = randomNote.data;
            } else {
                const vibeTypes = ['quote', 'joke', 'music'];
                vibeType = vibeTypes[Math.floor(Math.random() * vibeTypes.length)];
                
                switch(vibeType) {
                    case 'joke':
                        vibe = await getAIGeneratedContent("Tell me a funny, clean joke to lift my spirits.");
                        break;
                    case 'quote':
                        vibe = await getAIGeneratedContent("Give me a short, inspiring quote for the day.");
                        break;
                    case 'music':
                        const song = music[Math.floor(Math.random() * music.length)];
                        vibe = `Song: ${song.title} by ${song.artist}`;
                        vibeContent.innerHTML = `<p class="mb-2">${vibe}</p><audio controls src="${song.url}" class="w-full mt-2"></audio>`;
                        break;
                }
            }

            // Update UI with new vibe
            vibeMessage.textContent = 'Vibe Check Complete!';
            vibeContent.textContent = vibe;

            if (vibeType === 'video' || vibeType === 'audio') {
                visualNoteContainer.classList.remove('hidden');
                if (vibeType === 'video') {
                    vibeVideo.src = vibe;
                    vibeVideo.classList.remove('hidden');
                    vibeAudio.classList.add('hidden');
                    vibeVideo.play();
                } else {
                    vibeAudio.src = vibe;
                    vibeAudio.classList.remove('hidden');
                    vibeVideo.classList.add('hidden');
                    vibeAudio.play();
                }
            } else {
                visualNoteContainer.classList.add('hidden');
            }

            if (isFreeTier) {
                userProfile.dailyCheckCount++;
                await saveUserProfile();
                updateDailyCount();
            }

            const currentVibe = { date: new Date().toISOString().split('T')[0], content: vibe, type: vibeType, rating: 'unrated' };
            userProfile.vibeHistory.push(currentVibe);
            await saveUserProfile();

            vibeCheckBtn.textContent = 'Vibe Check Again';
            vibeCheckBtn.disabled = false;
            vibeControls.classList.remove('hidden');
            dashboard.classList.remove('hidden');
            updateDashboardChart();
        });

        vibeRatingBtns.forEach(btn => {
            btn.addEventListener('click', async () => {
                const rating = btn.getAttribute('data-rating');
                const lastVibe = userProfile.vibeHistory[userProfile.vibeHistory.length - 1];
                if (lastVibe) {
                    lastVibe.rating = rating;
                    await saveUserProfile();
                    updateDashboardChart();
                    alert(`Thanks for the feedback! You rated this vibe as ${rating}.`);
                }
            });
        });

        shareBtn.addEventListener('click', () => {
            const content = vibeContent.textContent;
            navigator.clipboard.writeText(`My Vibe Check: "${content}" #VibeCheckApp`)
                .then(() => alert('Vibe copied to clipboard! Share it with your friends.'))
                .catch(err => console.error('Could not copy text: ', err));
        });

        tweetBtn.addEventListener('click', () => {
            const content = vibeContent.textContent;
            const text = `My Vibe Check: "${content}" #VibeCheckApp`;
            window.open(`https://twitter.com/intent/tweet?text=${encodeURIComponent(text)}`);
        });

        // --- Media Recorder Logic ---
        videoStartBtn.addEventListener('click', async () => {
            mediaType = 'video';
            const stream = await navigator.mediaDevices.getUserMedia({ video: true, audio: true });
            previewVideo.srcObject = stream;
            previewVideo.classList.remove('hidden');
            mediaRecorderContainer.classList.remove('hidden');
            recordSaveBtn.classList.add('hidden');
            recordStopBtn.classList.remove('hidden');

            mediaRecorder = new MediaRecorder(stream);
            mediaRecorder.ondataavailable = (event) => {
                recordedChunks.push(event.data);
            };
            mediaRecorder.onstop = () => {
                const blob = new Blob(recordedChunks, { type: 'video/mp4' });
                const reader = new FileReader();
                reader.onloadend = async () => {
                    userProfile.personalNotes.push({ type: 'video', data: reader.result, date: new Date().toISOString() });
                    await saveUserProfile();
                    alert("Video note saved!");
                };
                reader.readAsDataURL(blob);
                recordedChunks = [];
                stream.getTracks().forEach(track => track.stop());
                previewVideo.srcObject = null;
                recordSaveBtn.classList.remove('hidden');
            };
            mediaRecorder.start();
        });

        audioStartBtn.addEventListener('click', async () => {
            mediaType = 'audio';
            const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
            previewVideo.classList.add('hidden');
            mediaRecorderContainer.classList.remove('hidden');
            recordSaveBtn.classList.add('hidden');
            recordStopBtn.classList.remove('hidden');

            mediaRecorder = new MediaRecorder(stream);
            mediaRecorder.ondataavailable = (event) => {
                recordedChunks.push(event.data);
            };
            mediaRecorder.onstop = () => {
                const blob = new Blob(recordedChunks, { type: 'audio/mp4' });
                const reader = new FileReader();
                reader.onloadend = async () => {
                    userProfile.personalNotes.push({ type: 'audio', data: reader.result, date: new Date().toISOString() });
                    await saveUserProfile();
                    alert("Audio note saved!");
                };
                reader.readAsDataURL(blob);
                recordedChunks = [];
                stream.getTracks().forEach(track => track.stop());
                recordSaveBtn.classList.remove('hidden');
            };
            mediaRecorder.start();
        });

        recordStopBtn.addEventListener('click', () => {
            if (mediaRecorder && mediaRecorder.state !== 'inactive') {
                mediaRecorder.stop();
            }
        });

        recordSaveBtn.addEventListener('click', () => {
            mediaRecorderContainer.classList.add('hidden');
        });

        // --- Firestore and User Authentication ---
        const initFirebase = async () => {
            let userId = null;
            if (initialAuthToken) {
                await signInWithCustomToken(auth, initialAuthToken);
            } else {
                await signInAnonymously(auth);
            }

            onAuthStateChanged(auth, async (user) => {
                if (user) {
                    userId = user.uid;
                    userIdSpan.textContent = userId;
                    const userDocRef = doc(db, `artifacts/${appId}/users/${userId}/vibe_profile/profile`);
                    const docSnap = await getDoc(userDocRef);

                    if (docSnap.exists()) {
                        userProfile = docSnap.data();
                    } else {
                        userProfile = {
                            subscriptionStatus: 'free',
                            dailyCheckCount: 0,
                            lastCheckDate: getTodaysDate(),
                            theme: 'indigo',
                            vibeHistory: [],
                            personalNotes: []
                        };
                        await setDoc(userDocRef, userProfile);
                    }

                    applyTheme(userProfile.theme);
                    updateDailyCount();
                    dashboard.classList.remove('hidden');
                    updateDashboardChart();

                    // Listen for real-time updates to the profile
                    onSnapshot(userDocRef, (doc) => {
                        if (doc.exists()) {
                            userProfile = doc.data();
                            applyTheme(userProfile.theme);
                            updateDailyCount();
                            updateDashboardChart();
                            subscriptionStatus.textContent = userProfile.subscriptionStatus === 'free' ? 'Free Tier' : `${userProfile.subscriptionStatus.charAt(0).toUpperCase() + userProfile.subscriptionStatus.slice(1)} Tier`;
                        }
                    });

                    // PayPal button setup
                    const paypalButtonsStarter = paypal.Buttons({
                        env: 'sandbox', // Change to 'production' for live payments
                        client: {
                            sandbox: 'AdodMyMMKGdr5pM5B6tEGzoaZ-UWjUuOfGiusWWiQks-ylw0nHSg-uX-LO-sPd4Jq2CrkToJZyicL3h2' // Use your sandbox client ID
                        },
                        commit: true,
                        style: {
                            label: 'paypal',
                            size: 'responsive',
                            shape: 'pill',
                            color: 'gold',
                        },
                        createOrder: (data, actions) => {
                            const tier = 'starter';
                            return fetch('YOUR_RENDER_URL/create-paypal-order', {
                                method: 'POST',
                                headers: { 'Content-Type': 'application/json' },
                                body: JSON.stringify({ tier: tier })
                            })
                            .then(response => response.json())
                            .then(order => order.id)
                            .catch(error => {
                                console.error("Error creating PayPal order:", error);
                                return null;
                            });
                        },
                        onApprove: (data, actions) => {
                            const tier = 'starter';
                            return fetch('YOUR_RENDER_URL/capture-paypal-order', {
                                method: 'POST',
                                headers: { 'Content-Type': 'application/json' },
                                body: JSON.stringify({ orderID: data.orderID, tier: tier, userId: user.uid })
                            })
                            .then(response => response.json())
                            .then(async (capture) => {
                                if (capture.success) {
                                    userProfile.subscriptionStatus = tier;
                                    await saveUserProfile();
                                    alert('Payment successful! Your subscription is now active.');
                                    subscriptionModal.classList.add('hidden');
                                } else {
                                    alert('Payment failed. Please try again.');
                                }
                            });
                        },
                        onCancel: (data) => {
                            alert('Payment cancelled.');
                        }
                    }).render('#paypal-button-container-starter');

                    const paypalButtonsUnlimited = paypal.Buttons({
                        env: 'sandbox', // Change to 'production' for live payments
                        client: {
                            sandbox: 'AdodMyMMKGdr5pM5B6tEGzoaZ-UWjUuOfGiusWWiQks-ylw0nHSg-uX-LO-sPd4Jq2CrkToJZyicL3h2' // Use your sandbox client ID
                        },
                        commit: true,
                        style: {
                            label: 'paypal',
                            size: 'responsive',
                            shape: 'pill',
                            color: 'gold',
                        },
                        createOrder: (data, actions) => {
                            const tier = 'unlimited';
                            return fetch('YOUR_RENDER_URL/create-paypal-order', {
                                method: 'POST',
                                headers: { 'Content-Type': 'application/json' },
                                body: JSON.stringify({ tier: tier })
                            })
                            .then(response => response.json())
                            .then(order => order.id)
                            .catch(error => {
                                console.error("Error creating PayPal order:", error);
                                return null;
                            });
                        },
                        onApprove: (data, actions) => {
                            const tier = 'unlimited';
                            return fetch('YOUR_RENDER_URL/capture-paypal-order', {
                                method: 'POST',
                                headers: { 'Content-Type': 'application/json' },
                                body: JSON.stringify({ orderID: data.orderID, tier: tier, userId: user.uid })
                            })
                            .then(response => response.json())
                            .then(async (capture) => {
                                if (capture.success) {
                                    userProfile.subscriptionStatus = tier;
                                    await saveUserProfile();
                                    alert('Payment successful! Your subscription is now active.');
                                    subscriptionModal.classList.add('hidden');
                                } else {
                                    alert('Payment failed. Please try again.');
                                }
                            });
                        },
                        onCancel: (data) => {
                            alert('Payment cancelled.');
                        }
                    }).render('#paypal-button-container-unlimited');
                }
            });
        };

        initFirebase();
    </script>
</body>
</html>
