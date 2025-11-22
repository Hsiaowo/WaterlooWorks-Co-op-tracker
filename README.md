## Co-op Job Tracker

Fast, resume-ready web app to track internship/co-op applications with charts and pipeline stats.

### Features
- Dashboard cards with interviews/offers/response rate, plus Chart.js pipeline + velocity charts.
- CRUD for applications (company, role, status, dates, notes, source, URL).
- SQLite persistence with starter seed data for a nice first impression.
- CSV export of your pipeline from the UI.
- Status normalization: `unfilled/filled/closed/rejected` are treated as rejected; `ranked/shortlist` collapse into a unified status.
- One-click “Import from WaterlooWorks” form that logs in with your credentials and pulls applications automatically (headless Playwright).
- Bootstrap 5 styling, responsive layout, and toasts for feedback.

### Quickstart
```bash
python3 -m venv venv
source venv/bin/activate          # Windows: venv\Scripts\activate
pip install -r requirements.txt
python -m playwright install chromium  # one-time browser download for the scraper
python app.py                     # opens http://localhost:5050 (default port)
```
The first run creates `jobtracker.db` and seeds a handful of sample applications. Add/edit/delete entries from the UI.

### Notable files
- `app.py` – Flask app, routes, SQLite schema, dashboard queries, and automatic seeding.
- `templates/` – HTML views (dashboard, list, form) using Bootstrap + Chart.js.
- `static/js/app.js` – Chart rendering.
- `static/css/main.css` – Light branding and status badge colors.
- `crawler.py` – Optional Playwright-based scraper to log into a board, pull rows, and auto-normalize statuses.

### Import from WaterlooWorks (UI)
1) From the app nav, open `Import` and enter your WaterlooWorks username/password.  
2) Click Import; a headless Playwright browser logs in and scrapes the postings page using the built-in WaterlooWorks preset.  
3) Status labels like `unfilled/filled/closed` map to `Rejected`; ranked/shortlist map to `Ranked`. Company+role rows are upserted.

### Crawler import (optional)
If a job board is slow to mark rejections (e.g., shows “unfilled/filled”), use the headless scraper to pull and normalize statuses:
```bash
source venv/bin/activate
python -m playwright install chromium         # one-time
python crawler.py \
  --login-url "https://example.com/login" \
  --target-url "https://example.com/applications" \
  --username "$WW_USERNAME" --password "$WW_PASSWORD" \
  --row-selector "table#jobs tr" \
  --company-selector "td:nth-child(1)" \
  --role-selector "td:nth-child(2)" \
  --status-selector "td:nth-child(3)"
```
Selectors will vary per site; the script maps `unfilled/filled/closed` to `Rejected`, and `ranked/shortlist` to `Ranked`, then upserts into SQLite.

### Portfolio angle
- Shows full-stack comfort: Python/Flask backend, SQL queries, front-end charts.
- Demonstrates product thinking: pipeline stats, response rate, recent activity.
- Easy to deploy to Render/Fly/Heroku or run locally for demos.

### Ideas to extend
- Add authentication and multi-user accounts.
- CSV import/export for applications.
- Email reminders for follow-ups via APScheduler/cron.
- Tagging and filters for industries/locations.
# WaterlooWorks-Co-op-tracker
