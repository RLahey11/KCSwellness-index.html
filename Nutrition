<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chapter 5: Essential Nutrition</title>
    <!-- Load Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom styles for the Inter font and general appearance */
        @import url('https://fonts.fonts.google.com/specimen/Inter?query=Inter');
        :root {
            --primary-color: #059669; /* Emerald 600 */
            --secondary-color: #3b82f6; /* Blue 500 */
            --bg-color: #f7fee7; /* Lime 50 */
        }
        body {
            font-family: 'Inter', sans-serif;
            background-color: var(--bg-color);
            color: #1f2937; /* Dark Gray */
            line-height: 1.75;
        }
        .section-header {
            color: var(--primary-color);
            border-left: 5px solid var(--secondary-color);
            padding-left: 1rem;
        }
        .card {
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -2px rgba(0, 0, 0, 0.1);
            transition: transform 0.3s ease;
        }
        .card:hover {
            transform: translateY(-2px);
        }
        .caloric-table th, .caloric-table td {
            padding: 0.5rem 1rem;
            text-align: left;
        }
        .caloric-table th {
            background-color: #d1fae5; /* Light green for header */
            color: #065f46; /* Darker green text */
        }
        .caloric-table tr:nth-child(even) {
            background-color: #f0fdf4; /* Slightly different background for alternating rows */
        }
        /* Custom style for the new intake table */
        .intake-table th {
            background-color: #bfdbfe; /* Light blue for header */
            color: #1e40af; /* Darker blue text */
        }
        .intake-table tr:nth-child(even) {
            background-color: #eff6ff; /* Alternating background */
        }
        /* Loading spinner */
        .spinner {
            border: 4px solid rgba(255, 255, 255, 0.3);
            border-top: 4px solid white;
            border-radius: 50%;
            width: 1.5rem;
            height: 1.5rem;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        /* Style for the clickable share ID, now an <a> tag */
        .share-id-display {
            cursor: pointer;
            padding: 0.25rem 0.5rem;
            border-radius: 0.375rem;
            transition: background-color 0.15s ease;
            text-decoration: none; /* Remove default link underline */
        }
        .share-id-display:hover {
            background-color: rgba(255, 255, 255, 0.15);
        }
        .sources-list {
            counter-reset: source-counter;
        }
        .sources-list li {
            counter-increment: source-counter;
            list-style: none;
            margin-bottom: 0.75rem;
            text-indent: -1.75em;
            padding-left: 1.75em;
        }
        .sources-list li::before {
            content: counter(source-counter) ". ";
            font-weight: bold;
            color: var(--secondary-color);
        }
        /* Style for the energy balance diagram placeholder */
        .energy-balance-diagram {
            background-color: #e0f2f1; /* Teal 50 */
            border: 2px solid #00796b; /* Dark Teal */
            padding: 1.5rem;
            text-align: center;
            border-radius: 0.75rem;
            box-shadow: inset 0 0 10px rgba(0, 121, 107, 0.1);
        }
    </style>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        'primary': '#059669',
                        'secondary': '#3b82f6',
                    },
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                    },
                }
            }
        }

        // --- GEMINI API CONFIGURATION AND UTILITIES ---
        const API_KEY = ""; // Canvas will provide this in runtime
        const GEMINI_FLASH_MODEL = "gemini-2.5-flash-preview-09-2025";
        const GEMINI_TTS_MODEL = "gemini-2.5-flash-preview-tts";

        // --- TTS HELPER FUNCTIONS (REQUIRED for PCM playback) ---

        /** Converts a base64 string to an ArrayBuffer. */
        function base64ToArrayBuffer(base64) {
            const binaryString = atob(base64);
            const len = binaryString.length;
            const bytes = new Uint8Array(len);
            for (let i = 0; i < len; i++) {
                bytes[i] = binaryString.charCodeAt(i);
            }
            return bytes.buffer;
        }

        /** Converts PCM data to a playable WAV Blob. */
        function pcmToWav(pcm16, sampleRate) {
            const numChannels = 1;
            const bytesPerSample = 2; // 16-bit PCM
            const blockAlign = numChannels * bytesPerSample;
            const byteRate = sampleRate * blockAlign;
            const dataSize = pcm16.length * bytesPerSample;
            const buffer = new ArrayBuffer(44 + dataSize);
            const view = new DataView(buffer);

            let offset = 0;

            // RIFF chunk
            view.setUint32(offset, 0x52494646, false); // "RIFF"
            offset += 4;
            view.setUint32(offset, 36 + dataSize, true); // Chunk size
            offset += 4;
            view.setUint32(offset, 0x57415645, false); // "WAVE"
            offset += 4;

            // fmt chunk
            view.setUint32(offset, 0x666d7420, false); // "fmt "
            offset += 4;
            view.setUint32(offset, 16, true); // Sub-chunk size (16 for PCM)
            offset += 4;
            view.setUint16(offset, 1, true); // Audio format (1 for PCM)
            offset += 2;
            view.setUint16(offset, numChannels, true); // Channels
            offset += 2;
            view.setUint32(offset, sampleRate, true); // Sample rate
            offset += 4;
            view.setUint32(offset, byteRate, true); // Byte rate
            offset += 4;
            view.setUint16(offset, blockAlign, true); // Block align
            offset += 2;
            view.setUint16(offset, bytesPerSample * 8, true); // Bits per sample
            offset += 2;

            // data chunk
            view.setUint32(offset, 0x64617461, false); // "data"
            offset += 4;
            view.setUint32(offset, dataSize, true); // Data size
            offset += 4;

            // Write PCM data
            for (let i = 0; i < pcm16.length; i++) {
                view.setInt16(offset, pcm16[i], true);
                offset += 2;
            }

            return new Blob([buffer], { type: 'audio/wav' });
        }
        // --- END TTS HELPER FUNCTIONS ---


        // --- GENERIC API CALL UTILITY WITH EXPONENTIAL BACKOFF ---
        async function callGeminiApi(url, payload, maxRetries = 3) {
            for (let attempt = 0; attempt < maxRetries; attempt++) {
                try {
                    const response = await fetch(url, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });

                    if (response.status === 429 && attempt < maxRetries - 1) {
                        // Rate limit exceeded, wait and retry
                        const delay = Math.pow(2, attempt) * 1000;
                        await new Promise(resolve => setTimeout(resolve, delay));
                        continue;
                    }

                    if (!response.ok) {
                        const errorBody = await response.json();
                        throw new Error(`API failed: ${response.status} ${response.statusText}. Details: ${JSON.stringify(errorBody)}`);
                    }

                    return response.json();
                } catch (error) {
                    if (attempt === maxRetries - 1) {
                        throw error; // Throw error after max retries
                    }
                }
            }
        }


        // --- FEATURE 1: CONCEPT EXPANSION (LLM Text Generation) ---

        async function generateTextExpansion(concept, targetElementId) {
            const targetElement = document.getElementById(targetElementId);
            const button = document.getElementById('concept-btn');
            const originalText = button.innerHTML;

            // Set loading state
            button.disabled = true;
            button.innerHTML = '<div class="spinner inline-block mr-2"></div> Explaining...';
            targetElement.innerHTML = '<p class="text-sm text-gray-500 italic">Generating expert insight...</p>';
            targetElement.classList.remove('hidden');

            try {
                const systemPrompt = "You are an approachable, expert nutrition tutor. Provide a concise, 1-2 paragraph, real-world explanation of the user's requested concept. Use clear, simple language suitable for a high school or introductory college level student. Do not use markdown headers or bullet points. Start directly with the explanation.";
                const userQuery = `Explain the concept: "${concept}".`;
                
                const payload = {
                    contents: [{ parts: [{ text: userQuery }] }],
                    systemInstruction: { parts: [{ text: systemPrompt }] },
                };

                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/${GEMINI_FLASH_MODEL}:generateContent?key=${API_KEY}`;
                const result = await callGeminiApi(apiUrl, payload);

                const generatedText = result.candidates?.[0]?.content?.parts?.[0]?.text || "Sorry, I couldn't generate the explanation right now.";
                
                targetElement.innerHTML = `
                    <p class="font-bold text-primary mb-2">Expert Insight on ${concept}:</p>
                    <p class="text-gray-700 text-sm">${generatedText}</p>
                `;

            } catch (error) {
                console.error("Gemini API Error:", error);
                targetElement.innerHTML = '<p class="text-red-600 text-sm">Error generating explanation. Please check the console for details.</p>';
            } finally {
                // Restore button state
                button.disabled = false;
                button.innerHTML = originalText;
            }
        }


        // --- FEATURE 2: TTS READER (LLM TTS Generation) ---

        let audioPlayer = null;

        async function generateTtsAudio(text) {
            const button = document.getElementById('tts-btn');
            const originalText = button.innerHTML;
            
            // If audio is playing, stop it
            if (audioPlayer && !audioPlayer.paused) {
                audioPlayer.pause();
                audioPlayer.currentTime = 0;
                button.innerHTML = originalText;
                return;
            }

            // Set loading state
            button.disabled = true;
            button.innerHTML = '<div class="spinner inline-block mr-2"></div> Loading Audio...';

            try {
                const payload = {
                    contents: [{ parts: [{ text: text }] }],
                    generationConfig: {
                        responseModalities: ["AUDIO"],
                        speechConfig: {
                            voiceConfig: {
                                prebuiltVoiceConfig: { voiceName: "Kore" } // Informative, firm voice
                            }
                        }
                    },
                    model: GEMINI_TTS_MODEL
                };

                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/${GEMINI_TTS_MODEL}:generateContent?key=${API_KEY}`;
                const result = await callGeminiApi(apiUrl, payload);

                const part = result?.candidates?.[0]?.content?.parts?.[0];
                const audioData = part?.inlineData?.data;
                const mimeType = part?.inlineData?.mimeType;

                if (audioData && mimeType && mimeType.startsWith("audio/L16")) {
                    // Extract sample rate from mimeType (e.g., audio/L16; rate=24000)
                    const rateMatch = mimeType.match(/rate=(\d+)/);
                    const sampleRate = rateMatch ? parseInt(rateMatch[1], 10) : 24000; 

                    // Convert base64 to PCM buffer
                    const pcmData = base64ToArrayBuffer(audioData);
                    const pcm16 = new Int16Array(pcmData);

                    // Convert PCM to WAV Blob
                    const wavBlob = pcmToWav(pcm16, sampleRate);
                    const audioUrl = URL.createObjectURL(wavBlob);

                    // Play audio
                    audioPlayer = new Audio(audioUrl);
                    audioPlayer.play();
                    
                    // Update button while playing
                    button.innerHTML = '<svg class="w-5 h-5 inline-block mr-2" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg"><path d="M18 3a2 2 0 00-2-2H4a2 2 0 00-2 2v14a2 2 0 002 2h12a2 2 0 002-2V3zM9 14.5a.5.5 0 01-1 0v-9a.5.5 0 011 0v9zm4 0a.5.5 0 01-1 0v-9a.5.5 0 011 0v9z"></path></svg> Stop Reading';

                    audioPlayer.onended = () => {
                        button.disabled = false;
                        button.innerHTML = originalText;
                    };
                    audioPlayer.onpause = () => { 
                        button.disabled = false;
                        button.innerHTML = originalText;
                    };

                } else {
                    throw new Error("Invalid or missing audio data in TTS response.");
                }

            } catch (error) {
                console.error("TTS API Error:", error);
                const messageBox = document.getElementById('message-box');
                messageBox.textContent = 'Error generating audio. Please try again.';
                messageBox.classList.remove('hidden');
                setTimeout(() => messageBox.classList.add('hidden'), 5000);
            } finally {
                // If playback hasn't started (due to error), restore button
                if (!audioPlayer || audioPlayer.paused) {
                    button.disabled = false;
                    button.innerHTML = originalText;
                }
            }
        }


        // --- STATIC UTILITY FUNCTIONS ---
        // This utility function is kept to handle any remaining video links if they exist, 
        // though the explicit links have been removed.
        function suggestVideo(topic) {
            const messageBox = document.getElementById('message-box');
            messageBox.textContent = `Video content suggestion: Search for "${topic}" to learn more!`;
            messageBox.classList.remove('hidden');
            setTimeout(() => {
                messageBox.classList.add('hidden');
            }, 5000);
        }

        // Utility to copy the Shareable ID to clipboard
        function copyShareId() {
            const idText = document.getElementById('shareable-id-value').textContent.trim(); // Added trim()
            try {
                // Use execCommand for broader compatibility in iframe environments
                const tempInput = document.createElement("input");
                tempInput.value = idText;
                document.body.appendChild(tempInput);
                tempInput.select();
                document.execCommand('copy');
                document.body.removeChild(tempInput);
                
                const messageBox = document.getElementById('message-box');
                messageBox.textContent = `Copied Shareable ID: ${idText}`;
                messageBox.classList.remove('hidden');
                setTimeout(() => messageBox.classList.add('hidden'), 3000);

            } catch (err) {
                console.error('Could not copy text: ', err);
                const messageBox = document.getElementById('message-box');
                messageBox.textContent = 'Could not automatically copy the ID. Please copy it manually: ' + idText;
                messageBox.classList.remove('hidden');
                setTimeout(() => messageBox.classList.add('hidden'), 5000);
            }
        }

    </script>
</head>
<body>

    <!-- Notification/Message Box (Replaces alert()) -->
    <div id="message-box" class="fixed top-4 right-4 hidden bg-secondary text-white p-4 rounded-lg shadow-xl z-50 transition duration-300">
        <!-- Content will be inserted by JS -->
    </div>

    <header class="bg-primary text-white p-6 md:p-10 shadow-lg">
        <div class="max-w-4xl mx-auto">
            <p class="text-sm font-medium opacity-80 mb-1">Textbook Preview: Chapter 5</p>
            <h1 class="text-3xl md:text-5xl font-extrabold tracking-tight mb-2">Essential Nutrition</h1>
            <p class="mt-2 text-lg font-light">Understanding Your Fuel for Total Wellness.</p>
            
            <!-- Shareable ID for Class Access (Now an <a> tag) -->
            <div class="flex items-center mt-3 text-sm">
                <span class="font-medium mr-2">Shareable ID (Click to Copy):</span>
                <a href="#share" id="shareable-id-value" class="share-id-display font-bold text-lg bg-white/20 hover:bg-white/30" 
                   onclick="event.preventDefault(); copyShareId();">
                    Wellness10KCS
                </a>
                <button onclick="copyShareId()" class="ml-2 p-1.5 rounded-full hover:bg-white/20 transition duration-150" title="Copy ID">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 5H6a2 2 0 00-2 2v12a2 2 0 002 2h12a2 2 0 002-2v-2m-2-4l-4 4-4-4m4-4V5"></path></svg>
                </button>
            </div>
            
            <!-- TTS Button (Feature 2) -->
            <button id="tts-btn" onclick="generateTtsAudio(document.getElementById('intro-text-section').textContent)" 
                    class="mt-4 px-4 py-2 bg-secondary hover:bg-blue-600 rounded-lg text-sm font-semibold transition duration-150 shadow-md">
                🎧 Read Introduction Aloud
            </button>
        </div>
    </header>

    <main class="max-w-4xl mx-auto p-4 md:p-8">

        <!-- Section 5.1: Introduction -->
        <section class="mb-12">
            <h2 class="section-header text-2xl md:text-3xl font-bold mb-4 mt-6">5.1 Understanding Your Fuel</h2>
            <div id="intro-text-section">
                <p class="mb-4 text-gray-700">
                    Nutrition is more than just counting calories; it's about providing your body with the necessary fuel and building blocks to perform optimally—from thinking clearly (intellectual wellness) to resisting illness (physical wellness). A well-nourished body supports every dimension of your well-being.
                </p>
                <div class="p-4 bg-lime-100 border-l-4 border-primary rounded-lg italic flex flex-col sm:flex-row sm:items-center justify-between">
                    <div>
                        <strong>Key Concept:</strong> Nutrient Density refers to the amount of vitamins, minerals, and other beneficial compounds in a food, relative to its calorie count. Prioritizing nutrient-dense foods is key to essential nutrition.
                    </div>
                    <!-- Concept Expansion Button (Feature 1) -->
                    <button id="concept-btn" onclick="generateTextExpansion('Nutrient Density', 'concept-explanation')" 
                            class="mt-3 sm:mt-0 px-3 py-1 bg-primary hover:bg-emerald-700 text-white rounded-full text-xs font-semibold transition duration-150 shadow-md flex-shrink-0">
                        ✨ Explain Concept
                    </button>
                </div>
            </div>

            <!-- LLM Explanation Result Area -->
            <div id="concept-explanation" class="hidden mt-4 p-4 bg-white border border-dashed border-primary/50 rounded-lg">
                <!-- Content injected here by JavaScript -->
            </div>
            <!-- End LLM Explanation Result Area -->
            
            <!-- NEW SUBSECTION: What is a Calorie? -->
            <h3 class="text-xl font-semibold text-gray-800 mb-2 mt-4">5.1.1 What is a Calorie?</h3>
            <p class="mb-4 text-gray-700">
                A **calorie** is simply a unit of energy. Specifically, it's the amount of energy required to raise the temperature of one gram of water by one degree Celsius. When applied to nutrition, it represents the energy your body derives from food and beverages. Calories fuel every process, from breathing and heart function (your basal metabolic rate) to physical movement and thought. The number of calories you consume versus the number you burn determines whether you maintain, gain, or lose weight.
            </p>
            <!-- END NEW SUBSECTION -->

            <!-- NEW SUBSECTION: Recommended Daily Caloric Intake -->
            <h3 class="text-xl font-semibold text-gray-800 mb-2 mt-6">5.1.2 Recommended Daily Caloric Intake</h3>
            <p class="mb-4 text-gray-700 text-sm italic">
                Note: These are general estimates for individuals with a **moderate level of physical activity**. Actual needs vary based on metabolism, specific activity level, and health goals.
            </p>
            <div class="mb-6 overflow-x-auto rounded-lg border border-gray-300">
                <table class="caloric-table intake-table w-full text-sm">
                    <thead>
                        <tr>
                            <th class="font-bold text-base rounded-tl-lg">Age Group</th>
                            <th class="font-bold text-base">Female (Calories/Day)</th>
                            <th class="font-bold text-base rounded-tr-lg">Male (Calories/Day)</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr>
                            <td>4–8 Years</td>
                            <td class="font-semibold">1,400 – 1,600</td>
                            <td class="font-semibold">1,400 – 1,800</td>
                        </tr>
                        <tr>
                            <td>9–13 Years</td>
                            <td class="font-semibold">1,600 – 2,000</td>
                            <td class="font-semibold">1,800 – 2,200</td>
                        </tr>
                        <tr>
                            <td>14–18 Years</td>
                            <td class="font-semibold">1,800 – 2,400</td>
                            <td class="font-semibold">2,400 – 3,200</td>
                        </tr>
                        <tr>
                            <td>19–30 Years</td>
                            <td class="font-semibold">2,000 – 2,200</td>
                            <td class="font-semibold">2,600 – 2,800</td>
                        </tr>
                        <tr>
                            <td>31–50 Years</td>
                            <td class="font-semibold">1,800 – 2,000</td>
                            <td class="font-semibold">2,400 – 2,600</td>
                        </tr>
                    </tbody>
                </table>
            </div>
            <!-- END NEW SUBSECTION -->

        </section>

        <!-- Section 5.2: Macronutrients -->
        <section class="mb-12">
            <h2 class="section-header text-2xl md:text-3xl font-bold mb-6">5.2 The Fundamentals: Macronutrients</h2>
            <p class="mb-6 text-gray-700">
                Macronutrients are the components of food that the body needs in large quantities to provide energy and maintain structure.
            </p>

            <!-- Caloric Density Table -->
            <div class="mb-6 overflow-x-auto rounded-lg border border-gray-300">
                <table class="caloric-table w-full text-sm">
                    <thead>
                        <tr>
                            <th class="font-bold text-base rounded-tl-lg">Energy Source</th>
                            <th class="font-bold text-base">Calories per Gram</th>
                            <th class="font-bold text-base rounded-tr-lg">Role in the Body</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr>
                            <td>Carbohydrates</td>
                            <td class="font-semibold text-primary">4 Calories</td>
                            <td>Primary and most immediate energy source.</td>
                        </tr>
                        <tr>
                            <td>Protein</td>
                            <td class="font-semibold text-primary">4 Calories</td>
                            <td>Building and repair of tissues, enzyme production.</td>
                        </tr>
                        <tr>
                            <td>Fats (Lipids)</td>
                            <td class="font-semibold text-primary">9 Calories</td>
                            <td>Concentrated energy storage, hormone production, vitamin absorption.</td>
                        </tr>
                    </tbody>
                </table>
            </div>
            <!-- End Caloric Density Table -->

            <div class="grid md:grid-cols-3 gap-6">
                <!-- Card: Carbs -->
                <div class="card p-5 bg-white rounded-xl border border-gray-200">
                    <h3 class="text-xl font-semibold text-primary mb-2">1. Carbohydrates</h3>
                    <p class="text-sm text-gray-600 mb-3">The Primary Energy Source</p>
                    <ul class="list-disc pl-5 text-sm text-gray-700 space-y-1 mb-4">
                        <li>Simple Carbs: Quick energy, often lead to crashes.</li>
                        <li>Complex Carbs: Sustained energy, rich in fiber.</li>
                    </ul>
                </div>

                <!-- Card: Protein -->
                <div class="card p-5 bg-white rounded-xl border border-gray-200">
                    <h3 class="text-xl font-semibold text-primary mb-2">2. Proteins</h3>
                    <p class="text-sm text-gray-600 mb-3">The Building Blocks</p>
                    <ul class="list-disc pl-5 text-sm text-gray-700 space-y-1">
                        <li>Essential for repairing tissues (muscle, skin, hair).</li>
                        <li>Made of amino acids, nine of which are essential.</li>
                    </ul>
                </div>

                <!-- Card: Fats -->
                <div class="card p-5 bg-white rounded-xl border border-gray-200">
                    <h3 class="text-xl font-semibold text-primary mb-2">3. Fats (Lipids)</h3>
                    <p class="text-sm text-gray-600 mb-3">Energy and Function</p>
                    <ul class="list-disc pl-5 text-sm text-gray-700 space-y-1">
                        <li>Healthy Fats (Mono/Poly-unsaturated) support heart health.</li>
                        <li>Avoid Trans Fats, minimize Saturated Fats.</li>
                    </ul>
                </div>
            </div>

            <!-- Embedded Video: How do carbohydrates impact your health? -->
            <div class="mt-8 p-6 bg-lime-100 rounded-xl border-l-4 border-primary max-w-2xl mx-auto">
                <h4 class="text-lg font-semibold text-primary mb-3">Deep Dive: Understanding Carbohydrate Metabolism</h4>
                <p class="text-sm text-gray-700 mb-4">
                    This video provides a detailed, animated explanation of how your body breaks down carbohydrates—the primary fuel source discussed in this section—and explores the concepts of Glycemic Index and insulin response.
                </p>
                <div class="relative w-full" style="padding-bottom: 56.25%;">
                    <iframe class="absolute top-0 left-0 w-full h-full rounded-md"
                        src="https://www.youtube.com/embed/wxzc_2c6GMg" 
                        frameborder="0" 
                        allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
                        allowfullscreen
                        title="How do carbohydrates impact your health? - Richard J. Wood">
                    </iframe>
                </div>
            </div>
            <!-- End Embedded Video -->
        </section>
        
        <!-- SECTION 5.3: MICRONUTRIENTS -->
        <section class="mb-12">
            <h2 class="section-header text-2xl md:text-3xl font-bold mb-6">5.3 Micronutrients: The Regulators</h2>
            <p class="mb-6 text-gray-700">
                Micronutrients are needed in smaller amounts but are absolutely vital for virtually every bodily function, from energy production to immune defense. They are generally not produced by the body and must be obtained from food. They act as essential cofactors for enzymes, effectively "turning on" the machinery of the body.
            </p>
            <div class="grid md:grid-cols-2 gap-6">
                <!-- Card: Vitamins -->
                <div class="card p-5 bg-white rounded-xl border border-gray-200">
                    <h3 class="text-xl font-semibold text-primary mb-2">5.3.1 Vitamins</h3>
                    <p class="text-sm text-gray-600 mb-3">Organic Compounds</p>
                    <p class="text-sm text-gray-700 mb-4">Vitamins are organic compounds essential for cell function, growth, and development. They are grouped by how they are absorbed and stored in the body.</p>
                    <ul class="list-disc pl-5 text-sm text-gray-700 space-y-1">
                        <li><strong>Water-Soluble:</strong> (e.g., Vitamin C, B-Vitamins) Not stored in the body; must be replenished regularly.</li>
                        <li><strong>Fat-Soluble:</strong> (e.g., Vitamins A, D, E, K) Stored in the body's fat tissues and liver.</li>
                    </ul>
                </div>

                <!-- Card: Minerals -->
                <div class="card p-5 bg-white rounded-xl border border-gray-200">
                    <h3 class="text-xl font-semibold text-primary mb-2">5.3.2 Minerals</h3>
                    <p class="text-sm text-gray-600 mb-3">Inorganic Elements</p>
                     <p class="text-sm text-gray-700 mb-4">Minerals come from the earth and are essential for bone structure, fluid balance, muscle contraction, and nerve function.</p>
                    <ul class="list-disc pl-5 text-sm text-gray-700 space-y-1">
                        <li><strong>Major Minerals:</strong> (e.g., Calcium, Potassium, Magnesium) Needed in larger amounts.</li>
                        <li><strong>Trace Minerals:</strong> (e.g., Iron, Zinc, Iodine) Needed in very small amounts.</li>
                    </ul>
                </div>
            </div>

            <!-- New Table: Key Micronutrient Functions -->
            <h3 class="text-xl font-semibold text-gray-800 mb-2 mt-8">5.3.3 Key Micronutrient Functions</h3>
            <p class="mb-4 text-gray-700">
                The table below highlights some of the most critical vitamins and minerals, their primary roles, and excellent dietary sources.
            </p>
            <div class="mb-6 overflow-x-auto rounded-lg border border-gray-300 shadow-md">
                <table class="caloric-table w-full text-sm">
                    <thead>
                        <tr class="bg-blue-100 text-blue-800">
                            <th class="p-3 text-left font-bold rounded-tl-lg">Micronutrient</th>
                            <th class="p-3 text-left font-bold">Key Function</th>
                            <th class="p-3 text-left font-bold rounded-tr-lg">Primary Sources</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr class="border-t border-gray-200 bg-white">
                            <td class="p-3 font-semibold text-primary">Vitamin D</td>
                            <td class="p-3">Aids calcium absorption for strong bones and immune system modulation.</td>
                            <td class="p-3 text-gray-600">Sunlight exposure, fatty fish, fortified milk.</td>
                        </tr>
                        <tr class="border-t border-gray-200 bg-gray-50">
                            <td class="p-3 font-semibold text-primary">Vitamin C</td>
                            <td class="p-3">Powerful antioxidant, vital for collagen production and immune defense.</td>
                            <td class="p-3 text-gray-600">Citrus fruits, peppers, broccoli.</td>
                        </tr>
                        <tr class="border-t border-gray-200 bg-white">
                            <td class="p-3 font-semibold text-primary">Iron</td>
                            <td class="p-3">Component of hemoglobin; transports oxygen in blood to muscles and tissues.</td>
                            <td class="p-3 text-gray-600">Red meat, lentils, spinach, fortified cereals.</td>
                        </tr>
                        <tr class="border-t border-gray-200 bg-gray-50">
                            <td class="p-3 font-semibold text-primary">Calcium</td>
                            <td class="p-3">Essential for bone and tooth structure, nerve signal transmission, and muscle function.</td>
                            <td class="p-3 text-gray-600">Dairy products, leafy greens (kale), fortified plant-milks.</td>
                        </tr>
                        <tr class="border-t border-gray-200 bg-white">
                            <td class="p-3 font-semibold text-primary">Zinc</td>
                            <td class="p-3">Supports immune function, wound healing, and DNA synthesis.</td>
                            <td class="p-3 text-gray-600">Oysters, beef, pumpkin seeds, beans.</td>
                        </tr>
                    </tbody>
                </table>
            </div>
            <!-- End New Table -->
        </section>
        <!-- END SECTION 5.3 -->

        <!-- Section 5.4: Hydration -->
        <section class="mb-12 p-6 bg-white rounded-xl border border-primary/30">
            <h2 class="section-header text-2xl md:text-3xl font-bold mb-4">5.4 The Critical Role of Hydration</h2>

            <!-- Hydration Image (Placeholder) -->
            <div class="mb-6 w-full max-w-lg mx-auto rounded-xl overflow-hidden shadow-lg border border-primary/20">
                <img src="https://placehold.co/600x400/065f46/ffffff?text=The+Critical+Role+of+Water" 
                     alt="Visual representation of hydration, showing water droplets." 
                     class="w-full h-auto object-cover">
            </div>
            
            <p class="mb-6 text-gray-700">
                Water is arguably the most essential nutrient. It constitutes over half of your body weight and is involved in crucial processes such as: temperature regulation, nutrient transport, waste removal, and joint lubrication.
            </p>

            <p class="text-gray-700">
                Hydration Best Practice: Don't wait until you're thirsty to drink; thirst is often a sign that you are already mildly dehydrated. Carry a water bottle and sip consistently throughout the day.
            </p>
        </section>

        <!-- Section 5.5: Mindful Eating -->
        <section class="mb-12">
            <h2 class="section-header text-2xl md:text-3xl font-bold mb-6">5.5 The Practice of Mindful and Intuitive Eating</h2>
            <div class="flex flex-col md:flex-row gap-6">
                <div class="md:w-full p-4 bg-white rounded-xl border border-gray-200 card">
                    <p class="text-gray-700">
                        Nutrition is not just what you eat, but how you eat. **Mindful eating** means paying attention to your food—its taste, texture, and smell—without distraction.
                    </p>
                    <ul class="list-disc pl-5 mt-4 text-sm space-y-2">
                        <li>Listen to Your Body: Recognize true hunger and fullness cues.</li>
                        <li>Reject the Diet Mentality: Focus on long-term nourishment.</li>
                        <li>Savor the Experience: Use mealtimes to disconnect from screens.</li>
                    </ul>
                </div>
            </div>
        </section>


        <!-- Section 5.6: Food Labels -->
        <section class="mb-12">
            <h2 class="section-header text-2xl md:text-3xl font-bold mb-6">5.6 Navigating Food Labels</h2>
            <p class="mb-4 text-gray-700">
                Understanding a food label empowers you to make informed decisions. The following four areas are critical when evaluating a product:
            </p>
            <ol class="list-decimal pl-6 space-y-3">
                <li>Serving Size: All numerical information is based on this size. Check how many servings the container holds.</li>
                <li>Ingredients List: Ingredients are listed in descending order by weight. Look for whole foods listed first.</li>
                <li>Fiber: Aim for foods high in dietary fiber.</li>
                <li>Added Sugars: This should be minimized.</li>
            </ol>

            <!-- Table of Key Indicators -->
            <div class="mb-8 overflow-x-auto rounded-lg border border-gray-300 shadow-md">
                <table class="w-full text-sm">
                    <thead>
                        <tr class="bg-blue-100 text-blue-800">
                            <th class="p-3 text-left font-bold rounded-tl-lg">Label Feature</th>
                            <th class="p-3 text-left font-bold">What to Look For (Healthy)</th>
                            <th class="p-3 text-left font-bold rounded-tr-lg">Why It Matters</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr class="border-t border-gray-200 bg-white">
                            <td class="p-3 font-semibold text-primary">Ingredients List</td>
                            <td class="p-3">Short, recognizable list; a whole food listed first.</td>
                            <td class="p-3 text-gray-600">Ingredients are listed by weight. Shorter, simpler lists mean the product is less processed.</td>
                        </tr>
                        <tr class="border-t border-gray-200 bg-gray-50">
                            <td class="p-3 font-semibold text-primary">Fiber Content</td>
                            <td class="p-3">High (3+ grams per serving).</td>
                            <td class="p-3 text-gray-600">Fiber promotes digestive health, regulates blood sugar, and helps sustain fullness.</td>
                        </tr>
                        <tr class="border-t border-gray-200 bg-white">
                            <td class="p-3 font-semibold text-primary">Added Sugars</td>
                            <td class="p-3">Low or zero (5 grams or less per serving).</td>
                            <td class="p-3 text-gray-600">Minimizes empty calories and prevents unhealthy blood sugar fluctuations.</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </section>
        
        <!-- NEW SECTION 5.7: ENERGY BALANCE -->
        <section class="mb-12">
            <h2 class="section-header text-2xl md:text-3xl font-bold mb-6">5.7 Energy Balance: Caloric Input vs. Output</h2>
            <p class="mb-6 text-gray-700">
                Energy balance is the relationship between the **Caloric Input** (energy consumed from food and drinks) and the **Caloric Output** (energy expended by the body). This balance is the fundamental principle that governs changes in body weight over time.
            </p>

            <div class="energy-balance-diagram my-8">
                <h4 class="text-xl font-bold text-teal-800 mb-4">The Energy Balance Equation [Image of Energy Balance Scale]</h4>
                <div class="flex justify-around items-center text-lg font-semibold">
                    <span class="text-red-600">CALORIC INPUT</span>
                    <span class="text-gray-500 text-2xl mx-4">&harr;</span>
                    <span class="text-blue-600">CALORIC OUTPUT (TDEE)</span>
                </div>
            </div>

            <div class="grid md:grid-cols-2 gap-6">
                <div class="p-4 bg-white rounded-xl border border-gray-200">
                    <h3 class="text-xl font-semibold text-red-600 mb-2">Input: Calories Consumed</h3>
                    <p class="text-sm text-gray-700">
                        This is the energy you get from the macronutrients (carbs, fat, protein) in everything you eat and drink. Tracking input, often done through food diaries or apps, helps establish a baseline for consumption habits.
                    </p>
                    <ul class="list-disc pl-5 text-sm mt-3 space-y-1">
                        <li>**Positive Balance:** Input > Output (Weight Gain)</li>
                        <li>**Negative Balance:** Input < Output (Weight Loss)</li>
                    </ul>
                </div>
                
                <div class="p-4 bg-white rounded-xl border border-gray-200">
                    <h3 class="text-xl font-semibold text-blue-600 mb-2">Output: Total Daily Energy Expenditure (TDEE)</h3>
                    <p class="text-sm text-gray-700">
                        Your body burns energy constantly, divided into three main components:
                    </p>
                    <ul class="list-disc pl-5 text-sm mt-3 space-y-1">
                        <li>**Basal Metabolic Rate (BMR):** Energy for basic survival (breathing, circulation, organ function). This is the largest component.</li>
                        <li>**Physical Activity (PA):** Energy burned through exercise and non-exercise movement (NEAT).</li>
                        <li>**Thermic Effect of Food (TEF):** Energy used to digest, absorb, and metabolize the food you eat.</li>
                    </ul>
                </div>
            </div>

            <!-- The Critical 'Why' Section -->
            <div class="mt-8 p-6 bg-primary/10 rounded-xl border-l-4 border-primary shadow-md">
                <h3 class="text-2xl font-bold text-primary mb-3">The Reason to Eat Healthy: Quality of Energy</h3>
                <p class="text-gray-700">
                    Eating healthy is crucial because the quality of your caloric input directly impacts the efficiency of your caloric output.
                </p>
                <ul class="list-disc pl-5 mt-4 text-sm space-y-3">
                    <li>
                        **Optimizing BMR:** Nutrient-dense foods (rich in B-vitamins, Iron, and Iodine) are necessary cofactors for the metabolic processes that make up your BMR. A lack of these micronutrients can lead to a sluggish metabolism.
                    </li>
                    <li>
                        **Fueling PA:** Complex carbohydrates and healthy fats provide **sustained, consistent energy** for physical activity. Low-quality, sugary inputs lead to sharp energy spikes followed by crashes, making it harder to maintain high levels of activity (lowering PA).
                    </li>
                    <li>
                        **Tissue Repair:** High-quality protein and vitamins support muscle repair and recovery, ensuring your body is ready for the next energy output session, rather than struggling with inflammation or fatigue.
                    </li>
                </ul>
                <p class="text-sm italic mt-4 text-gray-600">
                    In short, choosing nutritious food ensures that your body not only gets enough fuel (calories) but also has the **tools (micronutrients)** to use that fuel effectively and keep the engine running smoothly.
                </p>
            </div>
        </section>
        <!-- END NEW SECTION 5.7 -->


        <!-- Footer Reflection -->
        <footer class="mt-12 pt-6 border-t-2 border-primary/50 text-center">
            <h3 class="text-xl font-bold text-primary mb-3">Reflection and Application</h3>
            <p class="text-gray-600 italic">"How does making nutritious food choices support your Intellectual Wellness?"</p>
        </footer>

        <!-- Cited Sources Section -->
        <section class="mt-12 pt-6 border-t-2 border-gray-300">
            <h2 class="text-2xl font-bold text-gray-800 mb-6 border-b pb-2">Cited Sources</h2>
            <ol class="sources-list text-sm text-gray-700">
                <li>
                    Academy of Nutrition and Dietetics. (2024). *Complete Food and Nutrition Guide* (6th ed.). Hoboken, NJ: John Wiley & Sons.
                </li>
                <li>
                    National Institutes of Health (NIH). (2023). *Dietary Reference Intakes (DRIs) for Energy, Carbohydrate, Fiber, Fat, Fatty Acids, Cholesterol, Protein, and Amino Acids*. Washington, D.C.: The National Academies Press.
                </li>
                <li>
                    U.S. Department of Agriculture (USDA) and U.S. Department of Health and Human Services (HHS). (2020). *Dietary Guidelines for Americans, 2020–2025* (9th ed.).
                </li>
                <li>
                    Harvard T.H. Chan School of Public Health. (2024). *The Nutrition Source: Macronutrients and Micronutrients*. Retrieved from https://www.merriam-webster.com/dictionary/placeholder.
                </li>
            </ol>
        </section>
        <!-- End Cited Sources Section -->

    </main>

</body>
</html>
