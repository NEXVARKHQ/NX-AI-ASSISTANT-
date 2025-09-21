<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NXVARK AI Business Assistant</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            @apply bg-gray-900 text-gray-100;
        }

        .chat-container {
            height: calc(100vh - 120px);
            overflow-y: auto;
            scroll-behavior: smooth;
        }

        .chat-bubble {
            max-width: 80%;
            padding: 10px 15px;
            border-radius: 20px;
            margin-bottom: 15px;
            position: relative;
        }

        .user-bubble {
            @apply bg-indigo-600 text-white self-end;
            border-bottom-right-radius: 5px;
        }

        .bot-bubble {
            @apply bg-gray-700 text-gray-200 self-start;
            border-bottom-left-radius: 5px;
        }

        .copy-button {
            position: absolute;
            top: 5px;
            right: 5px;
            opacity: 0;
            transition: opacity 0.2s ease-in-out;
            @apply text-gray-400 hover:text-white bg-gray-800 p-1 rounded-full;
        }

        .bot-bubble:hover .copy-button {
            opacity: 1;
        }

        .loader {
            border: 4px solid rgba(255, 255, 255, 0.3);
            border-top: 4px solid #3B82F6;
            border-radius: 50%;
            width: 24px;
            height: 24px;
            animation: spin 1s linear infinite;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .prompt-button {
            @apply bg-gray-800 text-gray-300 py-2 px-4 rounded-full text-sm font-medium transition-colors duration-200;
        }
        .prompt-button:hover {
            @apply bg-gray-700 text-white;
        }
    </style>
</head>
<body class="bg-gray-900 text-gray-100 flex flex-col items-center justify-between min-h-screen">
    <div class="flex flex-col w-full max-w-3xl h-screen bg-gray-800 shadow-xl rounded-lg overflow-hidden my-4 md:my-8">
        <!-- Header -->
        <header class="bg-gray-900 p-4 border-b border-gray-700 flex items-center justify-between">
            <div class="flex items-center space-x-2">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-8 h-8 text-indigo-500">
                  <path fill-rule="evenodd" d="M12 2.25c-5.385 0-9.75 4.365-9.75 9.75s4.365 9.75 9.75 9.75 9.75-4.365 9.75-9.75S17.385 2.25 12 2.25ZM9.742 20.34a7.5 7.5 0 1 0-5.742-5.742v.001c.42.083.856.126 1.288.126a3.75 3.75 0 1 1 0 7.5c-.432 0-.868-.043-1.288-.126Zm6.393-1.968a7.5 7.5 0 1 0 2.238-5.378v.001c-.42-.083-.856-.126-1.288-.126a3.75 3.75 0 1 1 0-7.5c.432 0 .868.043 1.288.126Z" clip-rule="evenodd" />
                </svg>
                <h1 class="text-2xl font-bold text-white">NXVARK</h1>
            </div>
            <p class="text-sm font-medium text-gray-400 hidden sm:block">AI Business Assistant</p>
        </header>

        <!-- Chat messages container -->
        <div id="chat-messages" class="chat-container flex flex-col p-4 space-y-4">
            <!-- Welcome message bubble -->
            <div class="chat-bubble bot-bubble">
                <p>Hello! I'm NXVARK, your AI business assistant. I'm here to help with all things entrepreneurship, startups, AI, and business. How can I assist you today?</p>
                <div class="absolute -bottom-2 -left-2 text-gray-500 text-xs font-semibold">NXVARK</div>
            </div>
        </div>

        <!-- Input and examples container -->
        <div class="p-4 border-t border-gray-700 bg-gray-900">
            <div class="flex flex-wrap gap-2 mb-4">
                <button class="prompt-button" onclick="setPrompt('What are the key steps to starting a tech startup?')">Tech Startup Steps</button>
                <button class="prompt-button" onclick="setPrompt('Explain the concept of a Lean Startup.')">Lean Startup</button>
                <button class="prompt-button" onclick="setPrompt('How can AI be used in business strategy?')">AI in Business</button>
                <button class="prompt-button" onclick="setPrompt('What is a business model canvas?')">Business Model Canvas</button>
            </div>
            <div class="flex items-center space-x-2">
                <input id="user-input" type="text" placeholder="Ask me anything..." class="flex-grow bg-gray-700 text-gray-200 rounded-full p-3 pl-5 focus:outline-none focus:ring-2 focus:ring-indigo-500 transition-colors">
                <button id="send-button" class="bg-indigo-600 text-white rounded-full p-3 hover:bg-indigo-700 transition-colors">
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-6 h-6">
                        <path d="M3.478 2.405a.75.75 0 0 0-.926.94l2.432 7.945a.75.75 0 0 0 1.352.091L13.5 9.431V8.25a.75.75 0 0 1 1.5 0v.784a.75.75 0 0 0 .7.747l.216.035a.75.75 0 0 0 .546-.356l3.87-6.994a.75.75 0 0 0-1.096-1.025L10.352 6.13a.75.75 0 0 0-.71.932l.71 2.969-2.31 1.73a.75.75 0 0 0-.275.464L3.478 2.405Z" />
                        <path d="M11.75 12.381a.75.75 0 0 0-.75.75v7.244H5.25a.75.75 0 0 1 0-1.5h.75v-6a.75.75 0 0 0-1.5 0v6a2.25 2.25 0 0 0 2.25 2.25h5.25a.75.75 0 0 0 0-1.5H11.75Z" />
                        <path d="M12.38 12.75a.75.75 0 0 0 .75-.75v-3.75a.75.75 0 0 1 1.5 0v3.75a.75.75 0 0 0 1.5 0v-4.5h2.25v2.25a.75.75 0 0 0 1.5 0v-2.25a.75.75 0 0 0-.75-.75h-3.75a.75.75 0 0 0-.75.75v.75a.75.75 0 0 1-1.5 0v-.75a.75.75 0 0 0-.75-.75h-3.75a.75.75 0 0 0-.75.75v.75a.75.75 0 0 1-1.5 0v-6a.75.75 0 0 0-.75-.75H5.25a.75.75 0 0 0-.75.75v10.5a.75.75 0 0 0 1.5 0v-9.75h1.5v9.75a2.25 2.25 0 0 0 2.25 2.25h4.5a.75.75 0 0 0 0-1.5h-4.5a.75.75 0 0 1-.75-.75V8.25a.75.75 0 0 0-1.5 0v4.5a.75.75 0 0 0 .75.75Z" />
                    </svg>
                </button>
            </div>
        </div>

        <!-- Custom Modal for Notifications -->
        <div id="modal" class="fixed inset-0 bg-gray-900 bg-opacity-75 flex items-center justify-center hidden z-50">
            <div class="bg-gray-800 p-6 rounded-lg shadow-xl max-w-sm w-full text-center">
                <p id="modal-message" class="text-white text-lg mb-4"></p>
                <button onclick="hideModal()" class="bg-indigo-600 text-white px-6 py-2 rounded-lg hover:bg-indigo-700 transition-colors">OK</button>
            </div>
        </div>
    </div>

    <script>
        const chatMessages = document.getElementById('chat-messages');
        const userInput = document.getElementById('user-input');
        const sendButton = document.getElementById('send-button');
        const modal = document.getElementById('modal');
        const modalMessage = document.getElementById('modal-message');
        const apiKey = ""; // API key is automatically provided by the platform

        async function showModal(message) {
            modalMessage.textContent = message;
            modal.classList.remove('hidden');
        }

        function hideModal() {
            modal.classList.add('hidden');
        }

        function copyToClipboard(text) {
            const textarea = document.createElement('textarea');
            textarea.value = text;
            document.body.appendChild(textarea);
            textarea.select();
            document.execCommand('copy');
            document.body.removeChild(textarea);
            showModal('Text copied to clipboard!');
        }

        function createMessageElement(text, sender) {
            const bubble = document.createElement('div');
            bubble.classList.add('chat-bubble', 'whitespace-pre-wrap');

            const nameElement = document.createElement('div');
            nameElement.classList.add('absolute', '-bottom-2', 'text-xs', 'font-semibold', 'text-gray-500');

            if (sender === 'user') {
                bubble.classList.add('user-bubble', 'self-end');
                nameElement.textContent = 'You';
                nameElement.classList.add('-right-2');
            } else {
                bubble.classList.add('bot-bubble', 'self-start');
                nameElement.textContent = 'NXVARK';
                nameElement.classList.add('-left-2');

                const copyButton = document.createElement('button');
                copyButton.classList.add('copy-button');
                copyButton.innerHTML = `<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-4 h-4"><path d="M7.5 3.375c-.414 0-.75.336-.75.75v.203a.5.5 0 0 1-.22.41L5.5 5.25a.75.75 0 0 0-.25 1.28l.18.22a.75.75 0 0 1 .188.423V7.5c0 .414.336.75.75.75h.203a.5.5 0 0 1 .41.22L7.5 9a.75.75 0 0 0 1.28-.25l.22-.18a.75.75 0 0 1 .423-.188V8.25a.75.75 0 0 0 .75-.75v-.203a.5.5 0 0 1 .22-.41L10.5 6.75a.75.75 0 0 0 .25-1.28l-.18-.22a.75.75 0 0 1-.188-.423V4.125c0-.414-.336-.75-.75-.75h-.203a.5.5 0 0 1-.41-.22L9 3a.75.75 0 0 0-1.28.25l-.22.18a.75.75 0 0 1-.423.188ZM6 12a6 6 0 1 1 12 0 6 6 0 0 1-12 0Zm1.875 3.75a.75.75 0 0 0 0 1.5h6.25a.75.75 0 0 0 0-1.5h-6.25ZM12 8.25a.75.75 0 0 0-.75.75v1.5a.75.75 0 0 0 1.5 0V9a.75.75 0 0 0-.75-.75Z" clip-rule="evenodd" fill-rule="evenodd" /></svg>`;
                copyButton.title = "Copy to clipboard";
                copyButton.onclick = () => copyToClipboard(text);
                bubble.appendChild(copyButton);
            }

            // Create paragraph for the text content
            const p = document.createElement('p');
            p.textContent = text;
            bubble.appendChild(p);
            bubble.appendChild(nameElement);

            chatMessages.appendChild(bubble);
            chatMessages.scrollTop = chatMessages.scrollHeight; // Scroll to bottom
        }

        function showTypingIndicator() {
            const typingIndicator = document.createElement('div');
            typingIndicator.id = 'typing-indicator';
            typingIndicator.classList.add('flex', 'items-center', 'space-x-2', 'self-start', 'mb-4', 'p-2', 'bg-gray-700', 'rounded-full');
            typingIndicator.innerHTML = '<div class="loader"></div><span class="text-sm text-gray-400">NXVARK is typing...</span>';
            chatMessages.appendChild(typingIndicator);
            chatMessages.scrollTop = chatMessages.scrollHeight;
        }

        function hideTypingIndicator() {
            const typingIndicator = document.getElementById('typing-indicator');
            if (typingIndicator) {
                typingIndicator.remove();
            }
        }

        async function sendMessage() {
            const userText = userInput.value.trim();
            if (!userText) return;

            createMessageElement(userText, 'user');
            userInput.value = '';
            showTypingIndicator();

            try {
                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;

                const systemPrompt = "Act as NXVARK, an AI assistant specializing in entrepreneurship, startups, business strategies, and AI in business. Provide concise, actionable, and insightful responses. When providing factual information, always cite your sources if possible. Maintain a professional yet friendly and encouraging tone.";

                const payload = {
                    contents: [{ parts: [{ text: userText }] }],
                    tools: [{ "google_search": {} }],
                    systemInstruction: {
                        parts: [{ text: systemPrompt }]
                    },
                };

                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                const result = await response.json();
                const candidate = result.candidates?.[0];

                if (candidate && candidate.content?.parts?.[0]?.text) {
                    let botText = candidate.content.parts[0].text;
                    let sources = [];
                    const groundingMetadata = candidate.groundingMetadata;
                    if (groundingMetadata && groundingMetadata.groundingAttributions) {
                        sources = groundingMetadata.groundingAttributions
                            .map(attribution => ({
                                uri: attribution.web?.uri,
                                title: attribution.web?.title,
                            }))
                            .filter(source => source.uri && source.title);
                    }

                    if (sources.length > 0) {
                        botText += "\n\n**Sources:**\n";
                        sources.forEach((source, index) => {
                            botText += `- [${source.title}](${source.uri})\n`;
                        });
                    }

                    hideTypingIndicator();
                    createMessageElement(botText, 'bot');
                } else {
                    hideTypingIndicator();
                    createMessageElement("I'm sorry, I couldn't process that request. Please try again.", 'bot');
                }

            } catch (error) {
                console.error("API call failed:", error);
                hideTypingIndicator();
                createMessageElement("An error occurred. Please check the console for details.", 'bot');
            }
        }

        function setPrompt(prompt) {
            userInput.value = prompt;
            sendMessage();
        }

        sendButton.addEventListener('click', sendMessage);

        userInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                sendMessage();
            }
        });
    </script>
</body>
</html>

