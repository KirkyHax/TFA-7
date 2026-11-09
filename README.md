# Laravel POS TFA7

Laravel POS is a small account-management module for the IT0049 Technical Formative Assessment 7. It demonstrates validated create forms, old-input redisplay, edit/update workflows, and safe avatar upload handling.

## Assessment coverage

| Required criterion | Implementation |
| --- | --- |
| Create customer form | `GET /customers/create` and `POST /customers`; `full_name` and valid `email` are required. |
| Create user form | `GET /users/create` and `POST /users`; `username` is required and unique, and `full_name` is required. |
| Edit and update workflows | `GET /customers/{customer}/edit`, `PUT /customers/{customer}`, `GET /users/{user}/edit`, and `PUT /users/{user}`. |
| Avatar migration | `database/migrations/2026_11_09_000002_add_avatar_to_users_table.php`. |
| Avatar validation | User edit accepts only JPG, JPEG, PNG, or WEBP images up to 2 MB. |
| Display-ready preparation | `app/Services/AvatarService.php` uses Intervention Image v3 and GD to crop uploads to a 256 × 256 WEBP thumbnail. |
| Public storage | Prepared files are saved through `Storage::disk('public')` and only the relative path is saved in `users.avatar`. |
| Placeholder fallback | `public/images/avatar-placeholder.svg` is used when the user has no stored avatar. |
| Database submission files | Migrations, factories, seeders, and `database/pos_database.sql` are included. |

## Requirements

- PHP 8.2 or newer
- Composer
- SQLite or MySQL/MariaDB
- PHP GD extension
- PHP Fileinfo extension

## Local setup

From the project root:

```bash
composer install
cp .env.example .env
php artisan key:generate
```

For Windows PowerShell, use this instead of `cp`:

```powershell
Copy-Item .env.example .env
```

The default configuration uses SQLite. Create the database file and run the migrations with sample data:

```bash
touch database/database.sqlite
php artisan migrate --seed
php artisan storage:link
php artisan serve
```

For Windows PowerShell, use this instead of `touch`:

```powershell
New-Item database/database.sqlite -ItemType File
```

Open `http://127.0.0.1:8000` after the server starts.

## MySQL setup

Create an empty MySQL database, then update these values in `.env`:

```dotenv
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=your_database_name
DB_USERNAME=your_database_user
DB_PASSWORD=your_database_password
```

Run:

```bash
php artisan migrate --seed
php artisan storage:link
```

The SQL reference export is available at `database/pos_database.sql`, but a normal Laravel installation should use the migrations and seeders.

## Test the project

```bash
php artisan test
```

The feature tests cover customer validation, customer updates, unique usernames, avatar validation, avatar storage, and placeholder rendering.

## Routes

| URL | Purpose |
| --- | --- |
| `/` | Dashboard |
| `/customers` | Customer listing |
| `/customers/create` | Create customer form |
| `/customers/{customer}/edit` | Edit customer form |
| `/users` | User listing with avatars |
| `/users/create` | Create user form |
| `/users/{user}/edit` | Edit user form with avatar upload |

## Avatar handling

The user edit request validates the upload before any file is written. `AvatarService` reads the validated image, crops it to a square 256 × 256 thumbnail, converts it to WEBP, generates a random filename, and writes it to the public disk. The database stores only a path such as `avatars/uuid.webp`; it does not store image binary data.

After a successful upload, make sure the public storage link exists:

```bash
php artisan storage:link
```

## Submission checklist

- [ ] Push the extracted raw project files to GitHub; do not upload only the ZIP.
- [ ] Keep `.env` and `vendor/` out of GitHub. The included `.gitignore` handles both.
- [ ] Confirm the GitHub repository contains `composer.json`, `app/`, `database/migrations/`, `database/seeders/`, `resources/views/`, `tests/`, and `README.md`.
- [ ] Test `/customers/create`, `/users/create`, both edit pages, and an avatar upload.
- [ ] Confirm the hosted URL is live and matches the repository code.
- [ ] Submit the GitHub repository link and hosted application link.

See `GITHUB_UPLOAD_INSTRUCTIONS.md` for click-by-click upload instructions.
