/* TEJ AI - Full Starter Code Tech Stack: React + Tailwind CSS, OpenAI API, Replicate, Web APIs */

// src/App.jsx import React from 'react'; import { Chat } from './components/Chat'; import { ImageGen } from './components/ImageGen'; import { VoiceIO } from './components/VoiceIO'; import { FileAnalyzer } from './components/FileAnalyzer';

function App() { return ( <div className="bg-gray-900 text-white min-h-screen p-4"> <h1 className="text-3xl font-bold mb-6 text-center">TEJ AI</h1> <div className="grid gap-6"> <Chat /> <ImageGen /> <VoiceIO /> <FileAnalyzer /> </div> </div> ); }

export default App;

// src/components/Chat.jsx import React, { useState } from 'react';

export function Chat() { const [input, setInput] = useState(''); const [messages, setMessages] = useState([]);

const handleSend = async () => { const userMsg = { role: 'user', content: input }; setMessages([...messages, userMsg]); const response = await fetch('https://api.openai.com/v1/chat/completions', { method: 'POST', headers: { 'Content-Type': 'application/json', 'Authorization': Bearer YOUR_OPENAI_API_KEY }, body: JSON.stringify({ model: 'gpt-4o', messages: [...messages, userMsg] }) }); const data = await response.json(); const botMsg = { role: 'assistant', content: data.choices[0].message.content }; setMessages(prev => [...prev, botMsg]); setInput(''); };

return ( <div className="bg-gray-800 p-4 rounded-xl shadow"> <h2 className="text-xl font-semibold mb-2">Chat</h2> <div className="h-40 overflow-y-auto mb-2"> {messages.map((msg, i) => ( <div key={i} className={msg.role === 'user' ? 'text-blue-400' : 'text-green-400'}> {msg.content} </div> ))} </div> <input className="w-full p-2 rounded bg-gray-700 text-white" placeholder="Type your message..." value={input} onChange={e => setInput(e.target.value)} /> <button
onClick={handleSend}
className="mt-2 bg-blue-600 px-4 py-2 rounded hover:bg-blue-500"
>Send</button> </div> ); }

// src/components/ImageGen.jsx import React, { useState } from 'react';

export function ImageGen() { const [prompt, setPrompt] = useState(''); const [imageUrl, setImageUrl] = useState('');

const generateImage = async () => { const response = await fetch('https://api.replicate.com/v1/predictions', { method: 'POST', headers: { 'Content-Type': 'application/json', 'Authorization': Token YOUR_REPLICATE_API_TOKEN }, body: JSON.stringify({ version: 'sdxl-model-version-id', input: { prompt } }) }); const data = await response.json(); setImageUrl(data?.output?.[0]); };

return ( <div className="bg-gray-800 p-4 rounded-xl shadow"> <h2 className="text-xl font-semibold mb-2">Image Generation</h2> <input className="w-full p-2 rounded bg-gray-700 text-white" placeholder="Enter prompt..." value={prompt} onChange={e => setPrompt(e.target.value)} /> <button
onClick={generateImage}
className="mt-2 bg-purple-600 px-4 py-2 rounded hover:bg-purple-500"
>Generate</button> {imageUrl && <img src={imageUrl} alt="Generated" className="mt-4 rounded" />} </div> ); }

// src/components/VoiceIO.jsx import React, { useState } from 'react';

export function VoiceIO() { const [text, setText] = useState('');

const speak = () => { const utterance = new SpeechSynthesisUtterance(text); speechSynthesis.speak(utterance); };

const listen = () => { const recognition = new (window.SpeechRecognition || window.webkitSpeechRecognition)(); recognition.lang = 'en-US'; recognition.onresult = (event) => { setText(event.results[0][0].transcript); }; recognition.start(); };

return ( <div className="bg-gray-800 p-4 rounded-xl shadow"> <h2 className="text-xl font-semibold mb-2">Voice Input/Output</h2> <textarea className="w-full p-2 rounded bg-gray-700 text-white mb-2" rows="3" value={text} onChange={e => setText(e.target.value)} ></textarea> <div className="flex gap-2"> <button onClick={speak} className="bg-green-600 px-4 py-2 rounded hover:bg-green-500">Speak</button> <button onClick={listen} className="bg-yellow-600 px-4 py-2 rounded hover:bg-yellow-500">Listen</button> </div> </div> ); }

// src/components/FileAnalyzer.jsx import React, { useState } from 'react';

export function FileAnalyzer() { const [file, setFile] = useState(null); const [result, setResult] = useState('');

const handleUpload = async () => { const formData = new FormData(); formData.append('file', file);

const response = await fetch('/api/analyze', {
  method: 'POST',
  body: formData
});
const data = await response.json();
setResult(data.result);

};

return ( <div className="bg-gray-800 p-4 rounded-xl shadow"> <h2 className="text-xl font-semibold mb-2">File Analyzer</h2> <input type="file" className="mb-2 text-white" onChange={e => setFile(e.target.files[0])} /> <button
onClick={handleUpload}
className="bg-indigo-600 px-4 py-2 rounded hover:bg-indigo-500"
>Analyze</button> {result && <pre className="mt-4 bg-gray-700 p-2 rounded">{result}</pre>} </div> ); }

