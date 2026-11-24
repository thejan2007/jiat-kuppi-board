# Merlin Kuppi Board

A campus peer-help, study board, and lost-and-found web app built for Java Institute (JIAT) students during **JIAT Hack Days Gampaha 2026**.

## Why we built this

Anyone who has studied for a Java viva or debugged a broken Laravel migration at midnight knows the struggle: you drop a question in the batch WhatsApp group and wait around hoping a senior is still awake to do a quick *Kuppi* session. 

We built **Merlin Kuppi Board** so students have one clean place to post coding doubts, exam prep questions, and campus lost-and-found notices—while getting an immediate AI-generated study guide in the meantime. Instead of replying in stiff textbook English or messy Singlish, the AI tutor is configured to explain concepts the way a senior batchmate actually talks: natural Sinhala mixed with standard English technical terms.

## What it does

* **Campus Help & Lost-and-Found Feed:** Post questions or notices under categories like *Coding Help*, *Kuppi Request / Session*, *Exam Prep*, and *Lost & Found*, complete with comma-separated tags.
* **Instant AI Kuppi Guide:** Every time a post is created, the backend calls the Google Gemini API and attaches a short, bulleted explanation directly to the post.
* **Bilingual Output (Sinhala + English Tech Terms):** Uses Gemini's `system_instruction` to respond in proper Sinhala Unicode while keeping programming terms (`Class`, `Migration`, `API`, `SQLite`, etc.) in English—even if the student types their question in Singlish.
* **Quick Actions:** Copy generated guides with one click and toggle posts between **Open** and **Resolved**.
* **Terminal Mode (Artisan CLI):** If you don't want to leave VS Code, you can ask the Kuppi tutor questions straight from your terminal.

## Tech Stack

* **Backend:** Laravel (PHP 8.x)
* **Frontend:** Laravel Blade, Tailwind CSS, Vanilla JavaScript
* **Database:** SQLite (Eloquent ORM)
* **AI Model:** Google Gemini API (`gemini-3.5-flash-lite`)
* **Local Dev Environment:** Laravel Herd

## Technical Notes

A couple of implementation details from the build:

1. **Shared Service Layer (`app/Services/GeminiService.php`):** We kept all Gemini API logic out of the controllers and placed it in a dedicated service class. Both `PostController` (Web UI) and `KuppiAskCommand` (CLI) call `GeminiService::generateKuppiHelp()`.
2. **Prompt Control:** Rather than stuffing rules into the user prompt where the model might mirror a student's Singlish input, we pass strict language and formatting rules inside the API payload's `system_instruction` block.
3. **Network & Error Handling:** During local testing on Windows/Herd, cURL occasionally timed out over HTTP/2. We forced HTTP/1.1 (`'version' => 1.1`) in Laravel's HTTP client and wrapped the request in a `try/catch` block so API or network issues log cleanly to `storage/logs/laravel.log` without breaking the page.

## Running Locally

### Prerequisites
* PHP 8.2+ and Composer (or [Laravel Herd](https://herd.laravel.com/))
* A Google Gemini API key from [Google AI Studio](https://aistudio.google.com/)

### Setup Steps

1. Clone the repository:
   ```bash
   git clone https://github.com/JustXSade/jiat-kuppi-board.git
   cd jiat-kuppi-board
   ```

2. Install PHP dependencies:
   ```bash
   composer install
   ```

3. Set up your environment file:
   ```bash
   cp .env.example .env
   php artisan key:generate
   ```

4. Open `.env` and add your Gemini API key (and make sure SQLite is enabled):
   ```env
   DB_CONNECTION=sqlite
   GEMINI_API_KEY=your_api_key_here
   ```

5. Create the SQLite database file and run migrations:
   ```bash
   touch database/database.sqlite
   php artisan migrate
   ```
   *(On Windows PowerShell, run `New-Item database/database.sqlite -ItemType File` instead of `touch`.)*

6. Start the app:
   * If you use **Laravel Herd**, just visit `http://jiat-kuppi-board.test`.
   * Otherwise, run:
     ```bash
     php artisan serve
     ```

## Using the CLI Tutor

You can query the AI Kuppi Tutor directly from the project root without opening a browser:

```bash
php artisan kuppi:ask "Java OOP kiyanne mokakda?"
```

## Project Structure

* `app/Http/Controllers/PostController.php` — Handles board display, post creation, and Open/Resolved status updates.
* `app/Models/Post.php` — Eloquent model for board posts (`author_name`, `title`, `content`, `category`, `tags`, `status`, `ai_summary`).
* `app/Services/GeminiService.php` — Google Gemini REST integration and bilingual system prompt.
* `app/Console/Commands/KuppiAskCommand.php` — Custom `kuppi:ask` Artisan command.
* `resources/views/board.blade.php` — Main dark-mode UI for the Kuppi board.

## Team

Built by **Team Merlin** for JIAT Hack Days Gampaha 2026.
## My Contribution (Hackathon)
- Connected external/backend **API** endpoints and handled data rendering in the UI.
- Designed and built key **UI sections/components** for a clean user experience.
- Built during **JIAT FOSS Hack Day** with **Team Merlin** (Original Repo: `JustXSade/jiat-kuppi-board`).
