# AI Lab Report Generator Documentation

## Overview
The AI Lab Report Generator is a web application that generates structured lab reports from uploaded manuals or pasted text. It uses Tesseract.js for OCR, OpenRouter’s `thudm/glm-z1-9b:free` model for report generation, and jsPDF for PDF downloads. The app is a single `index.html` file, deployable on GitHub Pages.

## Features
- **Step 1**: Upload a PDF/image or paste text (OCR with Tesseract.js).
- **Step 2**: Enter experiment name.
- **Step 3**: Customize report sections (e.g., Objective, Theory, Result).
- **Step 4**: Preview and download as PDF/HTML.
- **Tech Stack**: HTML, JavaScript, Tailwind CSS, Tesseract.js, jsPDF, OpenRouter API.

## Prerequisites
- **Git**: Installed for version control.
- **Node.js**: For local testing with `http-server`.
- **GitHub Account**: For hosting on GitHub Pages.
- **OpenRouter API Key**: Obtain from [openrouter.ai/keys](https://openrouter.ai/keys).

## Setup
1. **Clone Repository**:
   ```bash
   git clone https://github.com/YOUR_USERNAME/lab-report-generator.git
   cd lab-report-generator
   ```
2. **Add `index.html`**:
   - Copy the `index.html` file (provided in the project).
   - Insert your OpenRouter API key:
     ```javascript
     'Authorization': 'Bearer YOUR_OPENROUTER_API_KEY'
     ```
     **Note**: Remove the key before committing to public repos.
3. **Install Local Server**:
   ```bash
   npm install -g http-server
   ```

## Local Testing
1. **Run Server**:
   ```bash
   http-server
   ```
   - Open `http://localhost:8080`.
2. **Test Flow**:
   - **Step 1**: Paste:
     ```
     Name of Experiment: - To Examine the Measurements of Wave-Length with a Slotted Wave Guide using the
     ```
   - **Step 2**: Enter “Measurements of Wavelength with a Slotted Waveguide.”
   - **Step 3**: Select options (e.g., Objective, Result: Success).
   - **Step 4**: Verify report and download PDF/HTML.
3. **Debug**:
   - Open Developer Tools (F12 → Console).
   - Check for:
     - `Script loaded`
     - `Tesseract.js loaded successfully`
     - `generatePrompt called`
     - API response (`200 OK`).
   - Common errors:
     - `401/403`: Invalid API key. Verify at [openrouter.ai/keys](https://openrouter.ai/keys).
     - `429`: Rate limit. Wait 60 seconds.
     - `ERR_NAME_NOT_RESOLVED`: Check DNS (use `8.8.8.8`).

## Deployment on GitHub Pages
1. **Create Repository**:
   - Name: `lab-report-generator`.
   - Visibility: Public.
   - Initialize with README.
2. **Add Files**:
   ```bash
   git add index.html
   git commit -m "Add AI Lab Report Generator"
   git push origin main
   ```
3. **Enable GitHub Pages**:
   - Go to repo → Settings → Pages.
   - Source: `main`, `/ (root)`.
   - Save.
4. **Access**:
   - URL: `https://YOUR_USERNAME.github.io/lab-report-generator`.
   - Wait ~1-5 minutes for deployment.
5. **Test Deployed App**:
   - Verify UI and report generation.
   - Check console for errors.

## Security
- **API Key**:
  - **Problem**: Hardcoding the OpenRouter API key in `index.html` exposes it in public repos.
  - **Solution**: Use a backend proxy.
    - **Example (Node.js)**:
      ```javascript
      // server.js
      const express = require('express');
      const fetch = require('node-fetch');
      const app = express();

      app.use(express.json());
      app.use(express.static('.'));

      app.post('/api/openrouter', async (req, res) => {
        try {
          const response = await fetch('https://openrouter.ai/api/v1/chat/completions', {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json',
              'Authorization': 'Bearer YOUR_OPENROUTER_API_KEY'
            },
            body: JSON.stringify(req.body)
          });
          const data = await response.json();
          res.json(data);
        } catch (error) {
          res.status(500).json({ error: error.message });
        }
      });

      app.listen(3000, () => console.log('Server running on port 3000'));
      ```
    - Update `index.html`:
      ```javascript
      const response = await fetch('/api/openrouter', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(requestBody)
      });
      ```
    - Dependencies:
      ```bash
      npm init -y
      npm install express node-fetch
      ```
    - Deploy on a server (e.g., Heroku, Render).
- **Commit Safely**:
  ```bash
  git add index.html
  git commit -m "Remove API key"
  git push origin main
  ```

## Improving Report Quality
The current output is suboptimal. To enhance it:
1. **Refine Prompt**:
   - Add explicit instructions:
     ```javascript
     prompt += `. Format the report with clear headings, bullet points for equipment/procedure, and a table for results. Ensure scientific tone and avoid repetition.`;
     ```
2. **Switch Model**:
   - Try `meta-ai/llama3-8b-instruct` if `thudm/glm-z1-9b:free` lacks quality.
   - Check [openrouter.ai/models](https://openrouter.ai/models).
3. **Add Formatting**:
   - Parse API response to add HTML styling (e.g., `<h2>`, `<ul>`) before displaying.
4. **Increase `max_tokens`**:
   - Set to 6000 for longer, detailed reports:
     ```javascript
     max_tokens: 6000
     ```
5. **Provide More Input**:
   - Use detailed manual text or a precise `expName`.

## Troubleshooting
- **DNS Error**:
  - Run: `ping openrouter.ai`.
  - Fix: Use DNS `8.8.8.8`. Disable VPNs.
- **400 Bad Request**:
  - Check `Request body` log.
  - Fix: Verify model at [openrouter.ai/models](https://openrouter.ai/models).
- **401/403**:
  - Fix: Regenerate API key.
- **429 Rate Limit**:
  - Fix: Wait 60 seconds. Monitor at [openrouter.ai](https://openrouter.ai).
- **Poor Report Quality**:
  - Fix: Refine prompt, switch model, or add formatting.

## Future Enhancements
- **Backend Proxy**: Hide API key and handle rate limits.
- **Rich Text Preview**: Use a WYSIWYG editor (e.g., Quill.js) for formatted previews.
- **Caching**: Store reports in `localStorage` with a hash (e.g., MD5).
- **Multi-Model Support**: Allow users to select models (e.g., `thudm/glm-z1-9b:free`, `meta-ai/llama3-8b-instruct`).

## Support
- **Contact**: For issues, share console logs, `curl` results, and experiment name.
- **Resources**:
  - [OpenRouter Docs](https://openrouter.ai/docs)
  - [GitHub Pages Docs](https://docs.github.com/en/pages)
  - [Tesseract.js](https://github.com/naptha/tesseract.js)
  - [jsPDF](https://github.com/parallax/jsPDF)