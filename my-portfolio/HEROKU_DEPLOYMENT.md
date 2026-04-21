# Heroku Deployment Guide

This guide will help you deploy your Django portfolio to Heroku.

## Prerequisites

1. A Heroku account (free at https://www.heroku.com)
2. Heroku CLI installed (https://devcenter.heroku.com/articles/heroku-cli)
3. Git repository initialized
4. Changes committed to git

## Deployment Steps

### 1. Login to Heroku

```bash
heroku login
```

### 2. Create a new Heroku app

```bash
heroku create your-app-name
```

Or if you want Heroku to auto-generate a name:
```bash
heroku create
```

### 3. Set Environment Variables

```bash
# Set Django settings module
heroku config:set DJANGO_SETTINGS_MODULE=myportfolio.settings.production

# Set SECRET_KEY (generate a new one at https://djecrety.ir/)
heroku config:set SECRET_KEY='your-secret-key-here'

# Set ALLOWED_HOSTS (replace with your domain)
heroku config:set ALLOWED_HOSTS='yourdomain.herokuapp.com,www.yourdomain.com'

# Set CSRF_TRUSTED_ORIGINS
heroku config:set CSRF_TRUSTED_ORIGINS='https://yourdomain.herokuapp.com,https://www.yourdomain.com'

# DEBUG should be False for production
heroku config:set DEBUG=False
```

### 4. Add PostgreSQL Database (Optional but Recommended)

```bash
heroku addons:create heroku-postgresql:essential-0
```

This will automatically set the `DATABASE_URL` environment variable.

### 5. Deploy

```bash
git push heroku main
```

(Or `git push heroku master` if your main branch is called master)

### 6. Run Migrations (if not run automatically)

```bash
heroku run python manage.py migrate
```

### 7. Create Superuser (if needed)

```bash
heroku run python manage.py createsuperuser
```

### 8. View Logs

```bash
heroku logs --tail
```

## How It Works

- **Procfile**: Tells Heroku how to run your app. The `release` process runs migrations and collects static files before the `web` process starts.
- **runtime.txt**: Specifies Python version
- **requirements.txt**: All Python dependencies
- **settings/production.py**: Uses `dj_database_url` to read `DATABASE_URL` and PostgreSQL if available

## Static Files

Heroku has an ephemeral file system, so static files are collected during deployment. Make sure you have:
- `STATIC_ROOT` set in production.py ✓
- `python manage.py collectstatic` in your Procfile release phase ✓

## Media Files

For user uploads, consider using AWS S3 or similar cloud storage. Currently configured to use local `media/` folder.

## Troubleshooting

- Check logs: `heroku logs --tail`
- Check config: `heroku config`
- SSH into dyno: `heroku ps:exec`
- Open app in browser: `heroku open`

## Custom Domain

After deployment, you can add a custom domain:

```bash
heroku domains:add yourdomain.com
```

Then update your domain's DNS records to point to Heroku.

## References

- [Heroku Django Documentation](https://devcenter.heroku.com/articles/django-app-configuration)
- [Heroku PostgreSQL](https://devcenter.heroku.com/articles/heroku-postgresql)
