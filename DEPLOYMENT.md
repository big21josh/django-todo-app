# Render Deployment Guide

## Prerequisites
1. GitHub account with your repository pushed
2. Render account (sign up at render.com)

## Setup Instructions

### Step 1: Prepare Your Repository
- ✅ All required files are already created:
  - `requirements.txt` - Python dependencies
  - `render.yaml` - Render deployment configuration
  - `.env.example` - Environment variables template

### Step 2: Push to GitHub
```bash
git add .
git commit -m "Prepare for Render deployment"
git push origin main
```

### Step 3: Deploy on Render

1. Go to [https://dashboard.render.com](https://dashboard.render.com)
2. Click "New +" and select "Web Service"
3. Connect your GitHub repository
4. Configure the service:
   - **Name**: `django-todo` (or your preferred name)
   - **Environment**: Python 3
   - **Region**: Oregon (free tier available)
   - **Branch**: main
   - **Build Command**: (should be auto-filled from render.yaml)
     ```
     pip install -r requirements.txt && python manage.py migrate && python manage.py collectstatic --noinput
     ```
   - **Start Command**: (should be auto-filled from render.yaml)
     ```
     gunicorn todoproject.wsgi:application
     ```

### Step 4: Configure Environment Variables

In the Render dashboard, add these environment variables:

1. **SECRET_KEY** (required)
   - Generate a new secure key using:
     ```bash
     python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
     ```
   - Paste the generated key

2. **DEBUG** 
   - Value: `False`

3. **ALLOWED_HOSTS**
   - Value: `your-app-name.onrender.com` (will be shown after deployment)

### Step 5: Configure Database

Render will automatically provision a PostgreSQL database if specified in render.yaml. The `DATABASE_URL` environment variable will be automatically set.

### Step 6: Deploy

Click "Create Web Service" to start the deployment. Render will:
1. Install dependencies from requirements.txt
2. Run database migrations
3. Collect static files
4. Start the application with Gunicorn

### Monitoring Your Deployment

- View logs in the Render dashboard
- Check the live URL once deployment completes
- Use the Render dashboard to manage environment variables and restart the service if needed

## Troubleshooting

### 502 Bad Gateway Error
- Check the build logs in Render dashboard
- Ensure all environment variables are set
- Verify SECRET_KEY is provided

### Static Files Not Loading
- Run `python manage.py collectstatic` locally to test
- Ensure STATIC_ROOT is set in settings.py (already configured)
- WhiteNoise middleware handles static files in production

### Database Connection Issues
- Verify DATABASE_URL is auto-populated by Render
- Check if migrations ran during build (should see in logs)

## Local Testing Before Deployment

To test your app locally with production settings:

```bash
# Create .env file with:
# DEBUG=False
# SECRET_KEY=your-test-secret-key
# DATABASE_URL=sqlite:///db.sqlite3

python manage.py collectstatic --noinput
python manage.py runserver
```

## Additional Resources

- [Render Django Documentation](https://render.com/docs/deploy-django)
- [Django Deployment Checklist](https://docs.djangoproject.com/en/6.0/howto/deployment/checklist/)
