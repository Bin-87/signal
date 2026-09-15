# Signal

Signal turns a rough but honest project description into one concise resume bullet for an SDE or PM lens. It is designed to preserve the evidence supplied by the user instead of manufacturing results.

## Safeguards

- Project descriptions are limited to 2,000 characters and are not written to files or a database. Do not enter confidential, personal, or proprietary information: descriptions are sent to Sarvam to generate the bullet.
- The model receives descriptions as delimited untrusted content. Reserved delimiters and control characters are neutralized, and embedded instructions are explicitly ignored.
- The model is instructed to use `[X users]`-style placeholders where a useful metric is missing. A final application check rejects any digit-based figure that does not literally appear in the source description.
- Model responses are displayed as plain text, limited to one short line, and never rendered as HTML or Markdown.
- Calls time out after 15 seconds, use TLS certificate verification, and do not follow redirects. Errors are logged server-side only as an error type and HTTP status; the UI shows no provider details, stack traces, keys, or project content.
- A Streamlit session can make five generation attempts. This is a UX and cost guard only, not a security boundary: a determined visitor can reset a session. Set a hard billing cap in the provider dashboard before publishing.
- Streamlit's XSRF and same-origin protections are explicitly enabled, detailed client errors are disabled, and anonymous Streamlit telemetry is disabled in `.streamlit/config.toml`.
- Dependency versions are exact pins. Dependabot opens reviewable GitHub pull requests for available dependency updates; review and test them before merging.

## Run locally

1. Create and activate a virtual environment.
2. Install the pinned dependencies:

   ```bash
   pip install --upgrade -r requirements.txt
   ```

3. Copy `.streamlit/secrets.toml.example` to `.streamlit/secrets.toml` and replace the placeholder `SARVAM_API_KEY` with a real Sarvam API key. Keep that file local; it is ignored by Git.
4. Start the app:

   ```bash
   streamlit run app.py
   ```

`SARVAM_MODEL` is optional; the default is `sarvam-105b`. Select a model that is enabled for your Sarvam account.

## Deploy to Streamlit Community Cloud

1. Push this repository without `.streamlit/secrets.toml` or any `.env` file.
2. Create a new app in Streamlit Community Cloud, selecting `app.py` as the entry point.
3. In the deployed app's **Settings → Secrets**, add the following (with your real key):

   ```toml
   SARVAM_API_KEY = "sk_your_real_key"
   SARVAM_MODEL = "sarvam-105b"
   ```

4. Set a provider-side hard billing cap in Sarvam before making the app public.

## Pre-launch checklist

- [ ] Real API key added only via Streamlit Cloud's secrets manager, never committed to the repo
- [ ] Hard billing cap set in the API provider's dashboard
- [ ] Tested against at least 10 varied real project descriptions with zero fabricated metrics in the output
- [ ] Tested with at least one adversarial input attempting prompt injection, confirmed the model does not follow embedded instructions
- [ ] Confirmed no stack trace or internal error ever surfaces in the UI on a forced API failure
- [ ] Confirmed `.streamlit/secrets.toml`, `.venv/`, and any key/certificate files are absent from `git status --short` before every push
- [ ] Reviewed and tested any dependency-update pull request before merging
