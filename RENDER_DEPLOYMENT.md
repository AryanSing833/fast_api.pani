# Render Deployment Guide

This guide will help you deploy the Water Quality FastAPI application to Render.

## Prerequisites

1. A GitHub account with your code pushed to a repository
2. A Render account (sign up at [render.com](https://render.com))

## Step-by-Step Deployment

### Option 1: Using render.yaml (Recommended)

1. **Sign in to Render**
   - Go to [render.com](https://render.com) and sign in with your GitHub account

2. **Create a New Web Service**
   - Click "New +" → "Web Service"
   - Connect your GitHub repository: `AryanSing833/fast_api.water`
   - Render will automatically detect the `render.yaml` file

3. **Configure the Service**
   - Render will use the settings from `render.yaml`:
     - **Name**: water-quality-api
     - **Environment**: Python
     - **Build Command**: `pip install -r requirements.txt`
     - **Start Command**: `uvicorn api.main:app --host 0.0.0.0 --port $PORT`
     - **Health Check Path**: `/health`

4. **Deploy**
   - Click "Create Web Service"
   - Render will automatically build and deploy your application
   - Wait for the build to complete (usually 2-5 minutes)

### Option 2: Manual Configuration

If you prefer to configure manually:

1. **Create a New Web Service**
   - Click "New +" → "Web Service"
   - Connect your GitHub repository

2. **Configure Settings**
   - **Name**: water-quality-api (or your preferred name)
   - **Environment**: Python 3
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `uvicorn api.main:app --host 0.0.0.0 --port $PORT`
   - **Plan**: Free (or choose a paid plan for better performance)

3. **Environment Variables** (Optional)
   - No environment variables are required for basic deployment
   - You can add custom variables if needed

4. **Deploy**
   - Click "Create Web Service"
   - Wait for deployment to complete

## Post-Deployment

### Access Your API

Once deployed, your API will be available at:
```
https://water-quality-api.onrender.com
```

(Your actual URL will be shown in the Render dashboard)

### Test Your Deployment

1. **Health Check**
   ```bash
   curl https://your-app-name.onrender.com/health
   ```
   Expected response:
   ```json
   {
     "status": "ok",
     "model_loaded": true,
     "image_detector_loaded": true
   }
   ```

2. **Test Prediction Endpoint**
   ```bash
   curl -X POST https://your-app-name.onrender.com/predict \
     -H "Content-Type: application/json" \
     -d '{
       "pH": 7.0,
       "Hardness": 150.0,
       "Turbidity": 5.0,
       "Sulfate": 250.0,
       "Chlorine": 1.0
     }'
   ```

3. **View API Documentation**
   - Visit: `https://your-app-name.onrender.com/docs`
   - Interactive Swagger UI for testing all endpoints

## Important Notes

### Free Tier Limitations

- **Sleep Mode**: Free tier services sleep after 15 minutes of inactivity
- **Cold Start**: First request after sleep may take 30-60 seconds
- **Memory**: 512MB RAM limit
- **Build Time**: Limited build minutes per month

### Performance Tips

1. **Upgrade Plan**: For production use, consider upgrading to a paid plan ($7/month) to avoid sleep mode
2. **Health Checks**: Render automatically pings `/health` to keep the service awake
3. **Monitoring**: Use the Render dashboard to monitor logs and performance

### Troubleshooting

#### Build Fails - Python Version Issues

If you see errors like "No matching distribution found for pandas>=2.4.0":

1. **Check Python Version in Render Dashboard**
   - Go to your service settings
   - Under "Environment", ensure Python 3.11 is selected
   - If not available, manually set it in the dashboard

2. **Verify runtime.txt**
   - Should contain: `python-3.11`
   - Make sure there are no extra spaces or characters

3. **Manual Python Version Selection**
   - In Render dashboard: Settings → Environment
   - Add environment variable: `PYTHON_VERSION=3.11`
   - Or use build command: `python3.11 -m pip install -r requirements.txt`

4. **Alternative: Use Compatible Versions**
   - If Python 3.11 isn't available, update `requirements.txt`:
     - Change `pandas>=1.5.0,<3.0.0` (works with Python 3.8+)
     - Ensure `runtime.txt` specifies a compatible version

#### Build Fails - General
- Check that all dependencies are in `requirements.txt`
- Verify Python version in `runtime.txt` (should be `python-3.11`)
- Check build logs in Render dashboard for specific error messages
- Ensure `water_quality_model.pkl` is committed to the repository

#### Service Won't Start
- Verify the start command in `Procfile` is correct
- Check that `api/main.py` exists and is valid
- Review logs in Render dashboard for errors

#### Model Not Loading
- Ensure `water_quality_model.pkl` is committed to the repository
- Check file paths in `predict_cli.py` and `api/main.py`
- Verify model file size is within limits

#### Memory Issues
- Free tier has 512MB RAM limit
- If you hit memory limits, consider:
  - Using a paid plan
  - Optimizing model loading
  - Reducing worker count

## API Endpoints

Once deployed, your API will have these endpoints:

- `GET /health` - Health check
- `GET /docs` - Interactive API documentation
- `POST /predict` - Parameter-based prediction
- `POST /predict/image` - Image-based pollution detection
- `POST /predict/legacy` - Legacy endpoint

## Next Steps

1. **Set up Custom Domain** (Optional)
   - In Render dashboard, go to Settings → Custom Domain
   - Add your domain name

2. **Enable HTTPS** (Automatic)
   - Render provides free SSL certificates automatically

3. **Monitor Usage**
   - Check the Render dashboard for usage statistics
   - Set up alerts if needed

4. **Update CORS** (If needed)
   - Update CORS origins in `api/main.py` to your frontend domain

## Support

- Render Documentation: https://render.com/docs
- Render Community: https://community.render.com
- Project Issues: Create an issue in your GitHub repository

