# MAGNUS-ai-image-generator
Magnus is an AI-powered bot that lets users create custom images through text prompts. It uses state-of-the-art models like Stable Diffusion for image generation and OpenAI (ChatGPT) for intelligent interaction. Built as both a public web app and a cross-platform Electron app, it is accessible worldwide on any PC or modern mobile device.
your-bot/
├── .env
├── package.json
├── server.js

npm init -y
npm install express axios dotenv
# Replace with your actual API keys
OPENAI_API_KEY=your_openai_or_openrouter_api_key_here
STABLE_DIFFUSION_API_KEY=your_stable_horde_api_key_here
require('dotenv').config();
const express = require('express');
const axios = require('axios');

const app = express();
const port = 3000;

app.use(express.json());

// Root test route
app.get('/', (req, res) => {
  res.send('AI Bot Server is running 🚀');
});

// Chat route using OpenAI (or OpenRouter)
app.post('/chat', async (req, res) => {
  const messages = req.body.messages;

  try {
    const response = await axios.post('https://openrouter.ai/api/v1/chat/completions', {
      model: 'openai/gpt-4',
      messages: messages,
    }, {
      headers: {
        Authorization: `Bearer ${process.env.OPENAI_API_KEY}`,
        'Content-Type': 'application/json',
      }
    });

    res.json(response.data);
  } catch (error) {
    console.error(error.response?.data || error.message);
    res.status(500).json({ error: 'Chat generation failed' });
  }
});

// Image route using Stable Horde
app.post('/image', async (req, res) => {
  const prompt = req.body.prompt;

  try {
    const response = await axios.post('https://stablehorde.net/api/v2/generate/async', {
      prompt: prompt,
      params: {
        n: 1,
        width: 512,
        height: 512,
        sampler_name: "k_euler",
        steps: 20,
      }
    }, {
      headers: {
        'apikey': process.env.STABLE_DIFFUSION_API_KEY,
        'Content-Type': 'application/json',
      }
    });

    res.json(response.data);
  } catch (error) {
    console.error(error.response?.data || error.message);
    res.status(500).json({ error: 'Image generation failed' });
  }
});

app.listen(port, () => {
  console.log(`✅ AI Bot Server running at http://localhost:${port}`);
});
node server.js
POST http://localhost:3000/chat
Content-Type: application/json

{
  "messages": [
    { "role": "user", "content": "Tell me a joke about goats" }
  ]
}
POST http://localhost:3000/image
Content-Type: application/json

{
  "prompt": "A goat playing a flute in a magical forest"
}
___________________________________

npm install express axios dotenv
your-bot/
├── .env
├── server.jsOPENAI_API_KEY=your_openai_or_openrouter_api_key_here
STABLE_DIFFUSION_API_KEY=your_stable_horde_api_key_hererequire('dotenv').config();
const express = require('express');
const axios = require('axios');

const app = express();
const port = 3000;

app.use(express.json());

// Root route
app.get('/', (req, res) => {
  res.send('🤖 AI Bot Server is up!');
});

// 1️⃣ Text Generation Endpoint
app.post('/chat', async (req, res) => {
  try {
    const response = await axios.post('https://openrouter.ai/api/v1/chat/completions', {
      model: 'openai/gpt-4',
      messages: req.body.messages,
    }, {
      headers: {
        Authorization: `Bearer ${process.env.OPENAI_API_KEY}`,
        'Content-Type': 'application/json',
      }
    });

    res.json(response.data);
  } catch (error) {
    console.error(error.response?.data || error.message);
    res.status(500).json({ error: 'Chat generation failed' });
  }
});

// 2️⃣ Image Generation Request (returns job ID)
app.post('/image', async (req, res) => {
  try {
    const response = await axios.post('https://stablehorde.net/api/v2/generate/async', {
      prompt: req.body.prompt,
      params: {
        width: 512,
        height: 512,
        steps: 20,
        sampler_name: "k_euler",
        n: 1,
      }
    }, {
      headers: {
        'apikey': process.env.STABLE_DIFFUSION_API_KEY,
        'Content-Type': 'application/json'
      }
    });

    res.json({ message: 'Image generation started', id: response.data.id });
  } catch (error) {
    console.error(error.response?.data || error.message);
    res.status(500).json({ error: 'Image request failed' });
  }
});

// 3️⃣ Poll Result from Image Job ID
app.get('/image/result/:id', async (req, res) => {
  const jobId = req.params.id;

  try {
    const status = await axios.get(`https://stablehorde.net/api/v2/generate/status/${jobId}`);
    
    if (status.data.done) {
      const images = status.data.generations;

      if (!images || images.length === 0) {
        return res.status(404).json({ error: 'No images returned' });
      }

      const imageURL = images[0].img;
      const imageBase64 = images[0].img_base64;

      res.json({ imageURL, imageBase64 });
    } else {
      res.json({ status: 'pending', message: 'Image still generating...' });
    }
  } catch (error) {
    console.error(error.response?.data || error.message);
    res.status(500).json({ error: 'Failed to fetch image result' });
  }
});

app.listen(port, () => {
  console.log(`🚀 Server running at http://localhost:${port}`);
});{
  "prompt": "A goat playing a flute in a magical forest"
}{
  {
  "message": "Image generation started",
  "id": "abc1234-xyz"
}{
  "imageURL": "https://images.stablehorde.net/generations/...png",
  "imageBase64": "/9j/4AAQSkZJRgABAQAAAQABAAD..."
}const cors = require('cors');
app.use(cors());npm install cors

_______________________________________________

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>AI Bot Interface</title>
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', sans-serif;
      background: #0f0f1a;
      color: #f0f0f0;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 2rem;
    }

    h1 {
      color: #fff;
      margin-bottom: 1rem;
      text-shadow: 0 0 10px #6c00ff;
    }

    .container {
      background: #1a1a2f;
      border-radius: 15px;
      padding: 2rem;
      width: 90%;
      max-width: 800px;
      box-shadow: 0 0 20px #222;
    }

    textarea, input {
      width: 100%;
      background: #2b2b3d;
      color: #eee;
      border: 1px solid #444;
      border-radius: 8px;
      padding: 10px;
      margin: 10px 0;
      resize: vertical;
    }

    button {
      background: #111;
      color: #fff;
      border: 1px solid #333;
      border-radius: 10px;
      padding: 10px 20px;
      margin: 5px;
      cursor: pointer;
      transition: all 0.2s ease;
    }

    button:hover {
      background: #333;
      box-shadow: 0 0 15px #ff0080;
    }

    .glow-yellow { box-shadow: 0 0 10px #ff0; }
    .glow-red { box-shadow: 0 0 10px #f00; }
    .glow-green { box-shadow: 0 0 10px #0f0; }
    .glow-purple { box-shadow: 0 0 10px #a0f; }

    .response-box {
      background: #202030;
      border: 1px solid #333;
      border-radius: 10px;
      padding: 1rem;
      margin-top: 1rem;
      max-height: 400px;
      overflow-y: auto;
    }

    img.generated {
      width: 100%;
      margin-top: 1rem;
      border-radius: 10px;
      border: 2px solid #444;
      box-shadow: 0 0 12px #a0f;
    }
  </style>
</head>
<body>
  <h1>🤖 AI Bot Control Room</h1>
  <div class="container">
    <textarea id="chatInput" rows="4" placeholder="Say something to the AI..."></textarea>
    <button onclick="sendChat()" class="glow-yellow">Send Text</button>

    <input type="text" id="imagePrompt" placeholder="Describe an image..." />
    <button onclick="generateImage()" class="glow-purple">Generate Image</button>

    <div class="response-box" id="responseBox"></div>
    <img id="generatedImage" class="generated" style="display:none;" />
  </div>

  <script>
    async function sendChat() {
      const input = document.getElementById('chatInput').value;
      const responseBox = document.getElementById('responseBox');

      const res = await fetch('/chat', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ messages: [{ role: "user", content: input }] })
      });

      const data = await res.json();
      const text = data.choices?.[0]?.message?.content || 'No response';
      responseBox.innerHTML = `<b>AI:</b> ${text}`;
    }

    async function generateImage() {
      const prompt = document.getElementById('imagePrompt').value;
      const responseBox = document.getElementById('responseBox');
      const imageEl = document.getElementById('generatedImage');

      responseBox.innerHTML = "<i>Generating image...</i>";
      imageEl.style.display = 'none';

      const res = await fetch('/image', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ prompt })
      });

      const { id } = await res.json();

      // Poll every 3 seconds
      const poll = setInterval(async () => {
        const result = await fetch(`/image/result/${id}`);
        const data = await result.json();

        if (data.imageURL) {
          clearInterval(poll);
          responseBox.innerHTML = `<b>Image generated!</b>`;
          imageEl.src = data.imageURL;
          imageEl.style.display = 'block';
        } else {
          responseBox.innerHTML = `<i>Still working on it...</i>`;
        }
      }, 3000);
    }
  </script>
</body>
</html>const path = require('path');
app.use(express.static(path.join(__dirname, '.')));
_______________________________________________

import zipfile
import os

# Set up paths
project_dir = "/mnt/data/Magnus_AI_Bot"
os.makedirs(project_dir, exist_ok=True)

# Create the .env file
env_content = """OPENAI_API_KEY=your_openai_or_openrouter_api_key_here
STABLE_DIFFUSION_API_KEY=your_stable_horde_api_key_here
"""
with open(f"{project_dir}/.env", "w") as f:
    f.write(env_content)

# Create the server.js file
server_js_content = """require('dotenv').config();
const express = require('express');
const axios = require('axios');
const path = require('path');
const app = express();
const port = 3000;

app.use(express.json());
app.use(express.static(path.join(__dirname, '.')));

app.get('/', (req, res) => {
  res.sendFile(path.join(__dirname, 'index.html'));
});

app.post('/chat', async (req, res) => {
  try {
    const response = await axios.post('https://openrouter.ai/api/v1/chat/completions', {
      model: 'openai/gpt-4',
      messages: req.body.messages,
    }, {
      headers: {
        Authorization: `Bearer ${process.env.OPENAI_API_KEY}`,
        'Content-Type': 'application/json',
      }
    });
    res.json(response.data);
  } catch (error) {
    res.status(500).json({ error: 'Chat generation failed' });
  }
});

app.post('/image', async (req, res) => {
  try {
    const response = await axios.post('https://stablehorde.net/api/v2/generate/async', {
      prompt: req.body.prompt,
      params: {
        width: 512,
        height: 512,
        steps: 20,
        sampler_name: "k_euler",
        n: 1,
      }
    }, {
      headers: {
        'apikey': process.env.STABLE_DIFFUSION_API_KEY,
        'Content-Type': 'application/json'
      }
    });
    res.json({ message: 'Image generation started', id: response.data.id });
  } catch (error) {
    res.status(500).json({ error: 'Image request failed' });
  }
});

app.get('/image/result/:id', async (req, res) => {
  const jobId = req.params.id;
  try {
    const status = await axios.get(`https://stablehorde.net/api/v2/generate/status/${jobId}`);
    if (status.data.done) {
      const images = status.data.generations;
      if (!images || images.length === 0) return res.status(404).json({ error: 'No images returned' });
      const imageURL = images[0].img;
      const imageBase64 = images[0].img_base64;
      res.json({ imageURL, imageBase64 });
    } else {
      res.json({ status: 'pending', message: 'Image still generating...' });
    }
  } catch (error) {
    res.status(500).json({ error: 'Failed to fetch image result' });
  }
});

app.listen(port, () => {
  console.log(`🚀 Server running at http://localhost:${port}`);
});
"""
with open(f"{project_dir}/server.js", "w") as f:
    f.write(server_js_content)

# Create the index.html file
index_html_content = """<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Magnus : AI Image Generator</title>
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', sans-serif;
      background: #0f0f1a;
      color: #f0f0f0;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 2rem;
    }

    h1 {
      color: #fff;
      margin-bottom: 0.5rem;
      text-shadow: 0 0 10px #ffcc00;
    }
    h2 {
      color: #888;
      margin-top: 0;
      margin-bottom: 1.5rem;
      text-shadow: 0 0 5px #444;
    }

    .container {
      background: #1a1a2f;
      border-radius: 15px;
      padding: 2rem;
      width: 90%;
      max-width: 800px;
      box-shadow: 0 0 20px #222;
    }

    textarea, input {
      width: 100%;
      background: #2b2b3d;
      color: #eee;
      border: 1px solid #444;
      border-radius: 8px;
      padding: 10px;
      margin: 10px 0;
      resize: vertical;
    }

    button {
      background: #111;
      color: #fff;
      border: 1px solid #333;
      border-radius: 10px;
      padding: 10px 20px;
      margin: 5px;
      cursor: pointer;
      transition: all 0.2s ease;
    }

    button:hover {
      background: #333;
      box-shadow: 0 0 15px #ff0080;
    }

    .glow-yellow { box-shadow: 0 0 10px #ff0; }
    .glow-red { box-shadow: 0 0 10px #f00; }
    .glow-green { box-shadow: 0 0 10px #0f0; }
    .glow-purple { box-shadow: 0 0 10px #a0f; }

    .response-box {
      background: #202030;
      border: 1px solid #333;
      border-radius: 10px;
      padding: 1rem;
      margin-top: 1rem;
      max-height: 400px;
      overflow-y: auto;
    }

    img.generated {
      width: 100%;
      margin-top: 1rem;
      border-radius: 10px;
      border: 2px solid #444;
      box-shadow: 0 0 12px #a0f;
    }
  </style>
</head>
<body>
  <h1>Magnus : AI Image Generator</h1>
  <h2>by Cassime Dion Lingo</h2>
  <div class="container">
    <textarea id="chatInput" rows="4" placeholder="Say something to the AI..."></textarea>
    <button onclick="sendChat()" class="glow-yellow">Send Text</button>

    <input type="text" id="imagePrompt" placeholder="Describe an image..." />
    <button onclick="generateImage()" class="glow-purple">Generate Image</button>

    <div class="response-box" id="responseBox"></div>
    <img id="generatedImage" class="generated" style="display:none;" />
  </div>

  <script>
    async function sendChat() {
      const input = document.getElementById('chatInput').value;
      const responseBox = document.getElementById('responseBox');
      const res = await fetch('/chat', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ messages: [{ role: "user", content: input }] })
      });
      const data = await res.json();
      const text = data.choices?.[0]?.message?.content || 'No response';
      responseBox.innerHTML = `<b>AI:</b> ${text}`;
    }

    async function generateImage() {
      const prompt = document.getElementById('imagePrompt').value;
      const responseBox = document.getElementById('responseBox');
      const imageEl = document.getElementById('generatedImage');
      responseBox.innerHTML = "<i>Generating image...</i>";
      imageEl.style.display = 'none';
      const res = await fetch('/image', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ prompt })
      });
      const { id } = await res.json();
      const poll = setInterval(async () => {
        const result = await fetch(`/image/result/${id}`);
        const data = await result.json();
        if (data.imageURL) {
          clearInterval(poll);
          responseBox.innerHTML = `<b>Image generated!</b>`;
          imageEl.src = data.imageURL;
          imageEl.style.display = 'block';
        } else {
          responseBox.innerHTML = `<i>Still working on it...</i>`;
        }
      }, 3000);
    }
  </script>
</body>
</html>
"""
with open(f"{project_dir}/index.html", "w") as f:
    f.write(index_html_content)

# Add thumbnail to the project folder
thumbnail_path = "/mnt/data/A_user_interface_for_a_software_named_Magnu_displa.png"
os.rename(thumbnail_path, f"{project_dir}/thumbnail.png")

# Create ZIP file
zip_path = "/mnt/data/Magnus_AI_Bot.zip"
with zipfile.ZipFile(zip_path, 'w') as zipf:
    for root, dirs, files in os.walk(project_dir):
        for file in files:
            file_path = os.path.join(root, file)
            zipf.write(file_path, arcname=os.path.relpath(file_path, project_dir))

zip_path

# Retry ZIP creation due to timeout

import zipfile
import os

# Redefine zip path and folder
project_dir = "/mnt/data/Magnus_AI_Bot"
zip_path = "/mnt/data/Magnus_AI_Bot.zip"

# Re-attempt ZIP creation
with zipfile.ZipFile(zip_path, 'w') as zipf:
    for root, dirs, files in os.walk(project_dir):
        for file in files:
            file_path = os.path.join(root, file)
            zipf.write(file_path, arcname=os.path.relpath(file_path, project_dir))

zip_path  

Title: Magnus : AI Image Generator
Creator: Cassime D. Lingo
