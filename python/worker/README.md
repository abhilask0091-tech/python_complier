# PyLab AI backend setup

The AI Agent in the notebook sends requests to this Cloudflare Worker. The OpenAI API key belongs in the Worker secret store; do not put it in `index.html`, Git, or a message.

## Deploy from Git Bash

You need a Cloudflare account and an OpenAI API key with API access. API usage is separate from a ChatGPT subscription and may incur charges.

1. Open Git Bash and enter the Worker folder:

   ```bash
   cd /c/Users/pky14/Documents/codex/python/worker
   ```

2. Sign in to Cloudflare:

   ```bash
   npx wrangler@latest login
   ```

   A browser window will open so you can authorize Wrangler.

3. Save your OpenAI API key as a Cloudflare secret. The prompt is interactive; paste the key there, not into a command or file:

   ```bash
   npx wrangler@latest secret put OPENAI_API_KEY
   ```

4. Deploy the Worker:

   ```bash
   npx wrangler@latest deploy
   ```

   Wrangler prints a URL similar to `https://pylab-python-ai.<your-subdomain>.workers.dev`. Copy that URL.

5. Open the published PyLab page, click **AI Agent**, paste the Worker URL into the URL field, and click **Save URL**. Then ask a question.

## Local page preview

From the repository folder, start a local web server:

```bash
cd /c/Users/pky14/Documents/codex
python -m http.server 8000
```

Open `http://localhost:8000/python/` in your browser. The Worker configuration allows this local origin for development.

## Privacy and limits

- When you ask PyLab AI a question, the current notebook's code and text cells are sent with your message to this backend and then to OpenAI to generate a reply. Avoid sending secrets or private data.
- The backend keeps the API key server-side and asks the API not to store the response. Do not commit secrets or change the frontend to contain an API key.
- The Worker has a Cloudflare rate limit configured at 10 requests per minute per IP. Cloudflare documents that this limiter is approximate and can vary by location. It is not a hard spending cap, so set an API budget/usage alert in your API account.
- Chat suggestions are shown for review; **Add code cell** only inserts a code block into the notebook.
- If a Python cell fails, PyLab can send the notebook context and error to the Worker. The AI may replace the failed cell and retry it once; inspect the changed code before using it.

If you later change the GitHub Pages origin, update `ALLOWED_ORIGINS` in `wrangler.toml` and deploy the Worker again.
