# PythonAnywhere Deployment Guide

## Step 1: Create a Web App on PythonAnywhere

1. Go to https://www.pythonanywhere.com/user/
2. Click **"Web"** menu → **"Add a new web app"**
3. Choose **"Manual configuration"** (NOT the Django template, since we'll use our repo)
4. Select **Python 3.12** (or latest available)
5. Click **"Next"** and your domain will be created: `yourusername.pythonanywhere.com`

---

## Step 2: Access the Bash Console

1. Go to **Consoles** → Click **Bash** to open a terminal
2. You'll be in `/home/yourusername/`

---

## Step 3: Clone Your Repository

```bash
git clone https://github.com/Himanshu6191/My_Website.git
cd My_Website/my-portfolio
```

---

## Step 4: Create Virtual Environment

```bash
python3.12 -m venv venv
source venv/bin/activate
```

---

## Step 5: Install Dependencies

```bash
pip install -r requirements.txt
```

---

## Step 6: Create Environment Variables File

```bash
nano .env
```

Paste this and modify accordingly:
```
SECRET_KEY='your-secret-key-here'
DEBUG=False
ALLOWED_HOSTS=yourusername.pythonanywhere.com
CSRF_TRUSTED_ORIGINS=https://yourusername.pythonanywhere.com
DATABASE_URL=
```

Press `Ctrl+O`, then `Enter`, then `Ctrl+X` to save.

**To generate a SECRET_KEY, run:**
```bash
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

---

## Step 7: Run Migrations

```bash
python manage.py migrate --settings=myportfolio.settings.production
```

---

## Step 8: Collect Static Files

```bash
python manage.py collectstatic --noinput --settings=myportfolio.settings.production
```

---

## Step 9: Configure WSGI File on PythonAnywhere

1. Go to **Web** menu
2. Scroll down and click on your web app
3. Click **"WSGI configuration file"** under "Code" section
4. Find the section for your Python version and replace it with:

```python
import os
import sys

# Add your project directory to the sys.path
path = '/home/yourusername/My_Website/my-portfolio'
if path not in sys.path:
    sys.path.append(path)

# Set Django settings module
os.environ['DJANGO_SETTINGS_MODULE'] = 'myportfolio.settings.production'

# Import Django WSGI application
from django.core.wsgi import get_wsgi_application
application = get_wsgi_application()
```

Replace `yourusername` with your actual PythonAnywhere username.

---

## Step 10: Configure Web App Settings

1. Go back to **Web** page
2. Under "Source code" section, set:
   - **Source code**: `/home/yourusername/My_Website/my-portfolio`
   - **Working directory**: `/home/yourusername/My_Website/my-portfolio`

3. Under "Virtualenv" section:
   - **Virtualenv path**: `/home/yourusername/My_Website/my-portfolio/venv`

4. **Scroll to top and click "Reload"** button

---

## Step 11: Create Superuser (for Django Admin)

Go back to Bash console:
```bash
cd ~/My_Website/my-portfolio
source venv/bin/activate
python manage.py createsuperuser --settings=myportfolio.settings.production
```

Answer the prompts for username, email, and password.

---

## Step 12: Test Your Site

Your portfolio should now be live at: `https://yourusername.pythonanywhere.com`

Admin panel: `https://yourusername.pythonanywhere.com/admin`

---

## Troubleshooting

If you see errors, check the **Error log**:
- Go to **Web** → scroll down → **Log files** → **Error log**

Common issues:
- **Static files not loading**: Run `python manage.py collectstatic --noinput` again
- **Database errors**: Check if migrations ran successfully
- **Import errors**: Verify virtualenv path is correct
- **500 errors**: Check error log for details

---

## Updating Your Code

To push new changes:
```bash
cd ~/My_Website
git pull origin main
# Restart your web app via PythonAnywhere dashboard
```
