# Laravel Real-Time Chat with Reverb and Vue 3

Reverb testing

## Installation:

1. Clone the repository:
```
git clone git@github.com:qirolab/laravel-reverb-chat.git
```

2. Navigate to the project directory:
```
cd laravel-reverb-chat
```

3. Install dependencies:
```
composer install
npm install # or yarn install
```

4. Generate application key:
```
php artisan key:generate
```

5. Configure database connection:

Edit `.env` file according to your database credentials.

6. Migrate database tables
```
php artisan migrate
```

7. Start development server
```
php artisan serve
npm run dev:tailwind
```

Here we used `npm run dev:tailwind` instead of `npm run dev` because we created
a `tailwind` theme using the Laravel Themer package in this project.

