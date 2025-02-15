# GeminiAI

GeminiAI is a simple web application that interacts with Google's Generative AI to answer user questions. This project consists of a **backend** built with **Node.js and Express** and a **frontend** using **React.js and Material-UI**.

<img src="https://github.com/user-attachments/assets/708cba51-3e38-4bc9-9afa-b2b518cf6eaa" width="200" />

Link to Github Profile: <a href="https://github.com/BasitJawad/GeminiSDKUsage">Github</a>

[![GitHub Repo stars](https://img.shields.io/github/stars/BasitJawad/GeminiAI?style=social)](https://github.com/BasitJawad/GeminiAI/stargazers)
---

## 📌 Features
- **Ask AI-powered questions**
- **Styled with Material-UI**
- **Backend with Express.js**
- **Frontend with React.js**
- **Supports Markdown Formatting**

---

## 🛠️ Installation

### 1️⃣ Clone the Repository
```sh
$ git clone https://github.com/BasitJawad/GeminiSDKUsage
$ cd GeminiSDKUsage
```

### 2️⃣ Install Dependencies
#### Backend:
```sh
$ cd backend
$ npm install
```
#### Frontend:
```sh
$ cd frontend
$ npm install
```

---

## 🚀 Running the Application

### Start Backend Server:
```sh
$ cd backend
$ node index.js
```

### Start Frontend:
```sh
$ cd frontend
$ npm start
```

Your application will now be running locally.

---

## 📌 Backend Code (Express.js)

```javascript
const express = require('express');
const messageRoute = express.Router();
require("dotenv").config()
const { GoogleGenerativeAI } = require('@google/generative-ai');

const apiKey = process.env.TestKey1;
const genAI = new GoogleGenerativeAI(apiKey);
const model = genAI.getGenerativeModel({ model: 'gemini-1.5-flash' });

const generationConfig = {
  temperature: 1,
  topP: 0.95,
  topK: 64,
  maxOutputTokens: 8192,
  responseMimeType: 'text/plain',
};

messageRoute.use(express.json());

messageRoute.post('/api/Question', (req, res) => {
  const { Question } = req.body;
  const chatSession = model.startChat({ generationConfig, history: [] });

  async function Datasend() {
    try {
      const result = await chatSession.sendMessage(Question + " Also write the question at the top too");
      res.json({ response: result.response });
    } catch (error) {
      console.error('Error in Datasend:', error);
      res.status(500).send('Error in processing the request');
    }
  }
  Datasend();
});

module.exports = messageRoute;
```

---

## 📌 Frontend Code (React.js)
{% raw %}
```
javascriptimport React, { useState } from 'react';
import TextField from '@mui/material/TextField';
import Button from '@mui/material/Button';
import axios from 'axios';
import ReactMarkdown from 'react-markdown';
import remarkGfm from 'remark-gfm';
import { SnackbarProvider, enqueueSnackbar } from 'notistack';
import Skeleton from '@mui/material/Skeleton';
import Box from '@mui/material/Box';

const App = () => {
  const [Question, setQuestion] = useState('');
  const [Output, setOutput] = useState('');
  const [loading, setLoading] = useState(false);

  const SendToBackend = (e) => {
    e.preventDefault();
    setOutput("");
    setQuestion('');
    setLoading(true);
    axios
      .post('/api/Question', { Question })
      .then((res) => {
        setOutput(res.data.response.candidates[0].content.parts[0].text);
        setLoading(false);
      })
      .catch((err) => {
        console.log(err.message);
        setLoading(false);
      });
  };

  return (
    <>
      <SnackbarProvider />
      <div className="wrapper bg-black w-screen h-screen flex justify-between flex-col items-center">
        <div className="entry flex justify-center items-center">
          <form onSubmit={SendToBackend}>
            <TextField
              label="What do you want to learn about?"
              multiline
              rows={3}
              value={Question}
              onChange={(e) => setQuestion(e.target.value)}
              InputProps={{ style: { color: 'white' } }} 
              InputLabelProps={{ style: { color: 'gray' } }}
            />
            <Button variant="contained" type="submit">Submit</Button>
          </form>
        </div>

        <div className="output relative flex overflow-y-auto">
          {Output && <Button variant="outlined" onClick={() => navigator.clipboard.writeText(Output)}>Copy</Button>}
          <div className="para text-white relative overflow-y-auto p-10">
            {loading ? (
              <Box sx={{ width: 900 }}>
                <Skeleton variant="text" sx={{ fontSize: '1rem' }} animation="wave" />
              </Box>
            ) : (
              <ReactMarkdown 
                components={{ 
                  code: ({ node, inline, className, children, ...props }) => 
                    !inline ? (
                      <pre><code {...props}>{children}</code></pre>
                    ) : (
                      <code {...props}>{children}</code>
                    )
                }} 
                remarkPlugins={[[remarkGfm, { singleTilde: false }]]}
              >
                {Output}
              </ReactMarkdown>
            )}
          </div>
        </div>
      </div>
    </>
  );
};

export default App;
```
---
{% endraw %}
