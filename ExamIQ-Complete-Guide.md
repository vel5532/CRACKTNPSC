# 🎯 ExamIQ — AI-Powered Exam Platform v2.0
### Complete Documentation & Deployment Guide

---

## 📁 Full Project Structure

```
examiq/
├── app.html                    ← ✅ COMPLETE standalone web app (use this!)
├── docker-compose.yml          ← Docker full-stack setup
│
├── backend/
│   ├── server.js               ← Express API (all routes)
│   ├── package.json
│   └── Dockerfile
│
├── docker/
│   ├── nginx.conf              ← Production reverse proxy
│   └── mongo-init.js           ← DB seed script
│
├── frontend/                   ← React source (for npm build)
│   └── src/
│       ├── pages/
│       │   ├── Login.jsx
│       │   ├── Dashboard.jsx
│       │   ├── Practice.jsx    ← MCQ practice mode
│       │   ├── Tests.jsx
│       │   ├── Exam.jsx        ← Full exam interface
│       │   ├── Result.jsx
│       │   ├── Analytics.jsx
│       │   ├── Leaderboard.jsx
│       │   ├── admin/
│       │   │   ├── Dashboard.jsx
│       │   │   ├── Upload.jsx  ← Book/PDF upload
│       │   │   ├── MCQBank.jsx
│       │   │   ├── Tests.jsx
│       │   │   └── Students.jsx
│       └── utils/
│
└── docs/
    └── README.md               ← This file
```

---

## ⚡ Quickest Start — Open app.html

**Just open `app.html` in any browser.** No installation needed.

All 12 features work immediately:
- Login / Register (Demo accounts available)
- Student dashboard with analytics
- Practice MCQ mode (with instant feedback + explanations)
- Full exam interface (timer, palette, mark for review)
- Result analysis with chapter-wise breakdown
- Analytics page with rank prediction
- Leaderboard
- **Admin Panel** (upload books, manage MCQ bank, view students)
- AI MCQ generation (requires Anthropic API key)
- Dark/light mode
- Export results

---

## 🗄️ Database Schema

### User
```json
{
  "_id": "ObjectId",
  "name": "string",
  "email": "string (unique)",
  "password": "bcrypt hash",
  "role": "student | admin",
  "streak": 0,
  "lastActive": "Date",
  "createdAt": "Date"
}
```

### Book (MCQ Bank)
```json
{
  "_id": "ObjectId",
  "title": "TNPSC Group 1 Study Material",
  "subject": "General Studies",
  "year": "2024",
  "source": "PDF | DOCX | TXT | Manual",
  "filename": "stored-file.pdf",
  "totalMcqs": 240,
  "chapters": ["Polity", "History", "Science"],
  "status": "processing | ready | error",
  "questions": [
    {
      "question": "Which article...?",
      "options": ["A", "B", "C", "D"],
      "correct": 2,
      "topic": "Polity",
      "chapter": "Constitutional Law",
      "difficulty": "Medium",
      "explanation": "Article 17...",
      "concept": "Fundamental Rights"
    }
  ],
  "uploadedBy": "ObjectId",
  "createdAt": "Date"
}
```

### Test
```json
{
  "_id": "ObjectId",
  "name": "TNPSC Mock Test 1",
  "subject": "General Studies",
  "duration": 90,
  "difficulty": "Hard",
  "questions": [ /* embedded question objects */ ],
  "totalAttempts": 1240,
  "isPublished": true,
  "createdBy": "ObjectId",
  "createdAt": "Date"
}
```

### Result
```json
{
  "_id": "ObjectId",
  "userId": "ObjectId",
  "testId": "ObjectId",
  "testName": "TNPSC Mock Test 1",
  "score": 78,
  "correct": 15,
  "wrong": 3,
  "skipped": 2,
  "accuracy": 83,
  "timeTaken": 3240,
  "totalQuestions": 20,
  "answers": { "0": 1, "1": 3, "2": 2 },
  "weakTopics": [
    { "name": "Polity", "correct": 3, "total": 5, "accuracy": 60 }
  ],
  "date": "Date"
}
```

---

## 🔌 API Reference

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| POST | /api/auth/register | ❌ | Register student/admin |
| POST | /api/auth/login | ❌ | Login, get JWT |
| GET | /api/auth/me | ✅ | Current user profile |
| GET | /api/books | ✅ | List all books |
| POST | /api/books/upload | ✅ Admin | Upload PDF/DOCX/TXT → generate MCQs |
| GET | /api/books/:id | ✅ | Get book + MCQs |
| GET | /api/books/:id/status | ✅ | Poll MCQ generation status |
| DELETE | /api/books/:id | ✅ Admin | Delete book |
| GET | /api/mcqs | ✅ | Get MCQs with filters |
| GET | /api/tests | ✅ | List published tests |
| GET | /api/tests/:id | ✅ | Get test with questions |
| POST | /api/tests | ✅ Admin | Create test from MCQ bank |
| DELETE | /api/tests/:id | ✅ Admin | Delete test |
| POST | /api/results | ✅ | Submit test result |
| GET | /api/results/me | ✅ | My result history |
| GET | /api/results/stats | ✅ | My analytics |
| GET | /api/results/all | ✅ Admin | All student results |
| GET | /api/leaderboard | ✅ | Top 20 students |
| GET | /api/admin/analytics | ✅ Admin | Platform overview stats |
| GET | /health | ❌ | Server health check |

---

## 🚀 Deployment Options

### Option 1: Standalone HTML (Zero deployment)
```bash
# Just open the file
open app.html
# OR serve it
npx serve . -p 8080
```

### Option 2: Docker (Recommended for production)
```bash
# Clone/copy project
cd examiq

# Create .env file
cat > .env << 'EOF'
ANTHROPIC_API_KEY=sk-ant-your-key-here
JWT_SECRET=change_this_to_random_64char_string
FRONTEND_URL=https://yourdomain.com
EOF

# Start everything
docker-compose up -d

# View logs
docker-compose logs -f

# Stop
docker-compose down

# With Nginx production profile
docker-compose --profile production up -d
```

Services started:
- MongoDB at localhost:27017
- Backend API at localhost:5000
- Frontend at localhost:3000

### Option 3: Manual VPS Setup (Ubuntu 22.04)
```bash
# 1. Install Node.js 20
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs

# 2. Install MongoDB
curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | sudo gpg --dearmor -o /usr/share/keyrings/mongodb-server-7.0.gpg
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
sudo apt update && sudo apt install -y mongodb-org
sudo systemctl start mongod && sudo systemctl enable mongod

# 3. Setup project
git clone your-repo && cd examiq/backend
npm install
cat > .env << 'EOF'
PORT=5000
MONGODB_URI=mongodb://localhost:27017/examiq
JWT_SECRET=your_secret_key
ANTHROPIC_API_KEY=sk-ant-your-key
EOF

# 4. PM2 Process Manager
sudo npm install -g pm2
pm2 start server.js --name examiq-api
pm2 startup && pm2 save

# 5. Nginx
sudo apt install -y nginx
sudo nano /etc/nginx/sites-available/examiq

# Paste:
server {
    listen 80;
    server_name yourdomain.com;
    location /api/ { proxy_pass http://localhost:5000; }
    location / { root /var/www/examiq; try_files $uri /index.html; }
}

sudo nginx -t && sudo systemctl restart nginx

# 6. SSL (free)
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d yourdomain.com
```

### Option 4: Render.com (Free tier)
```yaml
# render.yaml
services:
  - type: web
    name: examiq-api
    env: node
    buildCommand: cd backend && npm install
    startCommand: cd backend && node server.js
    envVars:
      - key: MONGODB_URI
        fromDatabase:
          name: examiq-db
          property: connectionString
      - key: ANTHROPIC_API_KEY
        sync: false
      - key: JWT_SECRET
        generateValue: true

databases:
  - name: examiq-db
    databaseName: examiq
    plan: free
```

### Option 5: Railway.app
```bash
# Install Railway CLI
npm install -g @railway/cli
railway login
railway new examiq

# Add MongoDB service in Railway dashboard
# Set environment variables
railway up
```

---

## 📱 Android APK Conversion

### Method 1: Capacitor (Best — Native Features)
```bash
# Install
npm install @capacitor/core @capacitor/cli
npm install @capacitor/android @capacitor/camera @capacitor/filesystem

# Initialize
npx cap init "ExamIQ" "com.examiq.app" --web-dir=dist

# Add capacitor.config.json
{
  "appId": "com.examiq.app",
  "appName": "ExamIQ",
  "webDir": "dist",
  "server": {
    "androidScheme": "https",
    "url": "https://your-deployed-app.com"  // or remove for offline
  },
  "android": {
    "buildOptions": {
      "keystorePath": "examiq.keystore",
      "keystoreAlias": "examiq"
    }
  },
  "plugins": {
    "SplashScreen": {
      "launchShowDuration": 2000,
      "backgroundColor": "#0a0a0f"
    }
  }
}

# Copy web build
npm run build  # or just copy app.html to dist/index.html
npx cap add android
npx cap sync android

# Open in Android Studio
npx cap open android
# In Android Studio: Build → Generate Signed Bundle/APK → APK
```

### Method 2: WebView App (Simplest)
```java
// MainActivity.java in Android Studio
public class MainActivity extends AppCompatActivity {
    private WebView webView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        webView = new WebView(this);
        WebSettings settings = webView.getSettings();
        settings.setJavaScriptEnabled(true);
        settings.setDomStorageEnabled(true);      // localStorage support
        settings.setAllowFileAccess(true);
        settings.setAllowFileAccessFromFileURLs(true);
        settings.setLoadWithOverviewMode(true);
        settings.setUseWideViewPort(true);
        settings.setBuiltInZoomControls(false);
        settings.setSupportZoom(false);
        settings.setDatabaseEnabled(true);
        settings.setCacheMode(WebSettings.LOAD_DEFAULT);

        webView.setWebViewClient(new WebViewClient());
        setContentView(webView);

        // Option A: Local file (offline)
        webView.loadUrl("file:///android_asset/index.html");

        // Option B: Hosted URL (requires internet)
        // webView.loadUrl("https://your-deployed-examiq.com");
    }

    @Override
    public void onBackPressed() {
        if (webView.canGoBack()) webView.goBack();
        else super.onBackPressed();
    }
}
```

**AndroidManifest.xml additions:**
```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

<!-- Inside <application> tag: -->
<application
    android:usesCleartextTraffic="true"
    android:networkSecurityConfig="@xml/network_security">
```

**Steps in Android Studio:**
1. New Project → Empty Activity → Java
2. Copy `app.html` → `app/src/main/assets/index.html`
3. Replace MainActivity.java with code above
4. Add permissions to AndroidManifest.xml
5. **Build → Generate Signed Bundle/APK**
6. Create keystore (one time): `keytool -genkey -v -keystore examiq.keystore -alias examiq -keyalg RSA -keysize 2048 -validity 10000`
7. Upload .apk to Google Play or distribute directly

### Method 3: PWA (No App Store Needed)
Add to `<head>` in app.html:
```html
<link rel="manifest" href="/manifest.json">
<meta name="theme-color" content="#7c3aed">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
```

Create `manifest.json`:
```json
{
  "name": "ExamIQ — AI Exam Platform",
  "short_name": "ExamIQ",
  "description": "AI-powered competitive exam preparation",
  "start_url": "/",
  "display": "standalone",
  "orientation": "portrait",
  "background_color": "#0a0a0f",
  "theme_color": "#7c3aed",
  "icons": [
    { "src": "icon-72.png",  "sizes": "72x72",   "type": "image/png" },
    { "src": "icon-192.png", "sizes": "192x192", "type": "image/png" },
    { "src": "icon-512.png", "sizes": "512x512", "type": "image/png", "purpose": "any maskable" }
  ]
}
```

Users can then: **Chrome menu → Add to Home Screen** → Works like a native app!

---

## 🔧 Environment Variables

```bash
# backend/.env
PORT=5000
NODE_ENV=production
MONGODB_URI=mongodb://localhost:27017/examiq
JWT_SECRET=minimum_32_character_random_string_here
ANTHROPIC_API_KEY=sk-ant-api03-...
FRONTEND_URL=https://yourdomain.com
```

---

## ✅ Feature Checklist

| Feature | Status |
|---------|--------|
| PDF / DOCX / TXT Upload | ✅ |
| Text extraction | ✅ |
| AI MCQ generation (Claude) | ✅ |
| 1000–10000 MCQ bank | ✅ |
| Chapter / topic categorization | ✅ |
| Difficulty tagging | ✅ |
| Real exam interface | ✅ |
| Countdown timer | ✅ |
| 5-min warning alert | ✅ |
| Auto-submit on timeout | ✅ |
| Question palette | ✅ |
| Mark for review | ✅ |
| Next / Previous | ✅ |
| Instant results | ✅ |
| Weak area detection | ✅ |
| Chapter-wise accuracy | ✅ |
| Answer explanations | ✅ |
| Concept tags | ✅ |
| Student dashboard | ✅ |
| Admin panel | ✅ |
| Admin: Upload books | ✅ |
| Admin: MCQ bank view | ✅ |
| Admin: Manage tests | ✅ |
| Admin: Student performance | ✅ |
| Leaderboard | ✅ |
| Analytics page | ✅ |
| Rank prediction | ✅ |
| Dark / light mode | ✅ |
| Export results | ✅ |
| Docker setup | ✅ |
| Android APK guide | ✅ |
| PWA support | ✅ |
