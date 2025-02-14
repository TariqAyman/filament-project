# Configuring Vite as the Sole Asset Bundler in a Filament v4 Project

## **Overview**
This document outlines the steps taken to configure **Vite** as the sole asset bundler in a **Filament v4** project, ensuring that all assets (CSS, JavaScript, and other resources) are built from source files without relying on precompiled or external assets.

---

## **Project Setup**

### **1. Install Laravel and Filament v4**
If not already set up, install Laravel and Filament:
```bash
composer create-project laravel/laravel filament-project
cd filament-project
composer require filament/filament
php artisan filament:install
php artisan make:filament-user  # Create an admin user
```

Verify that the Filament dashboard is accessible at `/admin`.

---

## **Configuring Vite as the Sole Asset Bundler**

### **2. Install and Configure Vite**
Ensure Vite and dependencies are installed:
```bash
npm install
```

Modify or create `vite.config.js`:
```javascript
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel({
            input: [
                'resources/css/app.css',
                'resources/js/app.js',
            ],
            refresh: true,
        }),
    ],
});
```

Update `package.json` to ensure correct scripts:
```json
"scripts": {
    "dev": "vite",
    "build": "vite build"
}
```

---

## **Setting Up Filament's Assets**

### **3. Configure Tailwind CSS**
Install Tailwind and dependencies:
```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

Modify `tailwind.config.js`:
```javascript
export default {
    content: [
        './resources/**/*.blade.php',
        './resources/**/*.js',
        './resources/**/*.vue',
        './vendor/filament/**/*.blade.php',
    ],
    theme: {
        extend: {},
    },
    plugins: [],
};
```

Update `resources/css/app.css`:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

Ensure `resources/js/app.js` includes:
```javascript
import './bootstrap';
import 'tailwindcss/tailwind.css';
```

---

## **Integrating Vite with Laravel Blade Views**
Modify `resources/views/layouts/app.blade.php` (or create it):
```blade
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>{{ config('app.name', 'Laravel') }}</title>
    @vite(['resources/css/app.css', 'resources/js/app.js'])
</head>
<body class="antialiased">
    <div id="app">
        @yield('content')
    </div>
</body>
</html>
```

---

## **Removing Precompiled Assets**
- Delete any `public/css`, `public/js`, or `public/build` directories.
- Ensure Filament loads assets from Vite and not from precompiled files.

---

## **Testing and Validation**

### **4. Run in Development Mode**
```bash
npm run dev
php artisan serve
```
Check `http://127.0.0.1:8000/admin` to verify styles and JavaScript.

### **5. Test Production Build**
```bash
npm run build
```
Ensure assets are generated properly in the `public/build` directory.
