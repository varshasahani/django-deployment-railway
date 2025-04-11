# django-deployment-railway
# 🚀 Django Deployment on Railway – Complete Setup Guide

This guide outlines the necessary files and configurations to successfully deploy a Django application on **Railway.app**.

---

## 📁 Required Project Files

### 1. **Procfile**

Railway uses the `Procfile` to determine how to run your web process.

```bash
# Option 1: Standard run command
web: gunicorn project.wsgi --log-file -

# Option 2: Run migrations before starting the server (recommended with external DB)
web: python manage.py migrate && gunicorn project.wsgi
```

---

### 2. **requirements.txt**

Lists all the dependencies your Django app needs. You can generate this with:

```bash
pip freeze > requirements.txt
```

#### Example:

```txt
asgiref==3.8.1  
Django==5.2  
gunicorn==23.0.0  
packaging==24.2  
sqlparse==0.5.3  
whitenoise  
```

> 🔁 Keep this updated anytime you install a new package.

---

### 3. **runtime.txt**

Specifies the Python version Railway should use.

```txt
Python 3.13.2
```

> 📌 Match this with the version you're using locally or in your virtual environment.

---

### 4. **railway_start.sh**

This shell script ensures your migrations, static file collection, and Gunicorn startup are done properly.

```bash
#!/bin/bash

python3 manage.py migrate --noinput
python3 manage.py collectstatic --noinput
gunicorn project.wsgi
```

> 🔐 Make the script executable:
```bash
chmod +x railway_start.sh
```

---

## ⚙️ `settings.py` Configuration

Make sure your `settings.py` is correctly set up for production and Railway hosting.

### ✅ Allowed Hosts

```python
ALLOWED_HOSTS = [
    'railway-deployed-project-url',
    '127.0.0.1',
    'localhost',
]
```

### ✅ CSRF Trusted Origins

```python
CSRF_TRUSTED_ORIGINS = [
    'https://railway-deployed-project-url',
]
```

### ✅ Middleware for Static Files

Make sure `WhiteNoise` is added **after** `SecurityMiddleware`:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

### ✅ Static Files Settings

```python
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```

---

## 📦 Version Control Notes

- Make sure `db.sqlite3` is **not ignored** in `.gitignore` **if you’re using SQLite**.
  ```bash
  # remove this line if present
  db.sqlite3
  ```

> If you’re using **PostgreSQL or another external DB**, keep it ignored.

---

## ✅ Final Checklist

| Task | Done |
|------|------|
| Create `Procfile` | ✅ |
| Create and update `requirements.txt` | ✅ |
| Add `runtime.txt` with correct Python version | ✅ |
| Add and make `railway_start.sh` executable | ✅ |
| Configure `settings.py` (hosts, static files, middleware) | ✅ |
| Push everything to GitHub and connect to Railway | ✅ |

---

## 🔗 Deployment on Railway

1. Push your project to GitHub  
2. Go to [https://railway.app](https://railway.app)  
3. Create a new project and link your GitHub repo  
4. Set your startup command (can be your `railway_start.sh`)  
5. Set environment variables (e.g., `DEBUG=False`, `SECRET_KEY`, `DATABASE_URL`, etc.)  
6. Deploy 🚀
