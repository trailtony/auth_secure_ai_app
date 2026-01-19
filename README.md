# AI Coding Challenge Generator

A full-stack web application that generates AI-powered coding challenges using OpenAI's GPT-4. Users can create personalized multiple-choice coding questions at different difficulty levels, with intelligent quota management and authentication powered by Clerk.

## ✨ Features

- 🤖 **AI-Powered Challenges** - Generate unique coding questions using GPT-4
- 🎯 **Multiple Difficulty Levels** - Easy, Medium, and Hard challenges tailored to your skill level
- 🔐 **Secure Authentication** - Enterprise-grade auth with Clerk (OAuth, email/password)
- 📊 **Challenge History** - Track all your generated challenges in one place
- 🎫 **Smart Quota System** - Daily limits with automatic 24-hour reset per user
- ⚡ **Modern Tech Stack** - FastAPI backend with React 19 frontend
- 🔄 **Real-time Updates** - Instant feedback and seamless user experience

## 🎥 Demo

<!-- Add your demo GIF or screenshots here -->
```
[Screenshots/GIF Coming Soon]
```

## 🏗️ Architecture

```
┌─────────────────┐         ┌──────────────────┐         ┌─────────────┐
│  React Frontend │ ──JWT──>│  FastAPI Backend │ ──API──>│   OpenAI    │
│   (Vite + React)│         │   (Python 3.12)  │         │   GPT-4     │
└────────┬────────┘         └────────┬─────────┘         └─────────────┘
         │                           │
         │                           │
         v                           v
  ┌──────────────┐          ┌─────────────────┐
  │ Clerk Auth   │          │ SQLite Database │
  │  (JWT/OAuth) │          │  (SQLAlchemy)   │
  └──────────────┘          └─────────────────┘
```

### Tech Stack

**Backend:**
- FastAPI - Modern, fast Python web framework
- SQLAlchemy - SQL toolkit and ORM
- SQLite - Lightweight database
- Clerk SDK - Authentication and user management
- OpenAI API - GPT-4 for challenge generation
- uv - Fast Python package manager

**Frontend:**
- React 19 - UI library
- Vite - Build tool and dev server
- React Router v6 - Client-side routing
- Clerk React - Authentication UI components

## 🚀 Quick Start

### Prerequisites

- **Python 3.12+** - [Download](https://www.python.org/downloads/)
- **Node.js 18+** - [Download](https://nodejs.org/)
- **uv** - Python package manager: `pip install uv`
- **Clerk Account** - [Sign up free](https://clerk.com/)
- **OpenAI API Key** - [Get API key](https://platform.openai.com/api-keys)

### Installation

#### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/auth_secure_ai_app.git
cd auth_secure_ai_app
```

#### 2. Backend Setup

```bash
cd backend

# Install dependencies
uv sync

# Create .env file
cp .env.example .env
```

Edit `backend/.env` with your credentials:

```env
CLERK_SECRET_KEY=sk_test_your_clerk_secret_key
CLERK_JWT_KEY=your_clerk_jwt_key
CLERK_WEBHOOK_SECRET=whsec_your_webhook_secret
OPENAI_API_KEY=sk-your_openai_api_key
```

#### 3. Frontend Setup

```bash
cd ../frontend

# Install dependencies
npm install

# Create .env file
cp .env.example .env
```

Edit `frontend/.env` with your Clerk publishable key:

```env
VITE_CLERK_PUBLISHABLE_KEY=pk_test_your_clerk_publishable_key
```

#### 4. Run the Application

**Terminal 1 - Backend:**
```bash
cd backend
python server.py
```
Backend runs on `http://localhost:8000`

**Terminal 2 - Frontend:**
```bash
cd frontend
npm run dev
```
Frontend runs on `http://localhost:5173`

#### 5. Configure Clerk Webhooks (Optional but Recommended)

In your Clerk Dashboard:
1. Go to **Webhooks** → **Add Endpoint**
2. Set URL: `http://localhost:8000/webhooks/clerk` (use ngrok for local testing)
3. Subscribe to: `user.created`
4. Copy the signing secret to `CLERK_WEBHOOK_SECRET` in backend `.env`

## 📋 Environment Variables

### Backend Variables

| Variable | Description | Required | Example |
|----------|-------------|----------|----------|
| `CLERK_SECRET_KEY` | Clerk secret key for backend authentication | Yes | `sk_test_...` |
| `CLERK_JWT_KEY` | Clerk JWT verification key | Yes | `-----BEGIN PUBLIC KEY-----...` |
| `CLERK_WEBHOOK_SECRET` | Webhook signature verification secret | Yes | `whsec_...` |
| `OPENAI_API_KEY` | OpenAI API key for GPT-4 access | Yes | `sk-...` |

### Frontend Variables

| Variable | Description | Required | Example |
|----------|-------------|----------|----------|
| `VITE_CLERK_PUBLISHABLE_KEY` | Clerk publishable key for frontend | Yes | `pk_test_...` |

### Getting Your Clerk Keys

1. **Clerk Dashboard** → Your Application → **API Keys**
2. Copy **Publishable Key** (for frontend)
3. Copy **Secret Key** (for backend)
4. For JWT Key: **Show JWT public key** → Copy the entire key including headers

## 📚 API Documentation

### Authentication
All endpoints (except webhooks) require a valid Clerk JWT token in the Authorization header:
```
Authorization: Bearer <clerk_jwt_token>
```

### Endpoints

#### Generate Challenge
```http
POST /api/generate-challenge
Content-Type: application/json

{
  "difficulty": "easy" | "medium" | "hard"
}
```

**Response:**
```json
{
  "id": 1,
  "difficulty": "easy",
  "title": "Basic Python List Operation",
  "options": [
    "my_list.append(5)",
    "my_list.add(5)",
    "my_list.push(5)",
    "my_list.insert(5)"
  ],
  "correct_answer_id": 0,
  "explanation": "In Python, the correct method...",
  "timestamp": "2026-01-17T20:30:00.000Z"
}
```

#### Get Challenge History
```http
GET /api/my-history
```

**Response:**
```json
{
  "challenges": [
    {
      "id": 1,
      "difficulty": "easy",
      "title": "Basic Python List Operation",
      "date_created": "2026-01-17T20:30:00.000Z",
      ...
    }
  ]
}
```

#### Get Quota Status
```http
GET /api/quota
```

**Response:**
```json
{
  "user_id": "user_...",
  "quota_remaining": 45,
  "last_reset_date": "2026-01-17T00:00:00.000Z"
}
```

#### Webhook Endpoint
```http
POST /webhooks/clerk
```
Handles Clerk `user.created` events to initialize user quotas.

## 🗄️ Database Schema

### Challenges Table
```sql
CREATE TABLE challenges (
    id INTEGER PRIMARY KEY,
    difficulty VARCHAR NOT NULL,
    date_created DATETIME DEFAULT CURRENT_TIMESTAMP,
    created_by VARCHAR NOT NULL,
    title VARCHAR NOT NULL,
    options VARCHAR NOT NULL,  -- JSON string
    correct_answer_id INTEGER NOT NULL,
    explanation VARCHAR
);
```

### Challenge Quotas Table
```sql
CREATE TABLE challenge_quotas (
    id INTEGER PRIMARY KEY,
    user_id VARCHAR NOT NULL UNIQUE,
    quota_remaining INTEGER NOT NULL DEFAULT 50,
    last_reset_date DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

## 🔒 Security Features

- ✅ JWT-based authentication with Clerk
- ✅ Token verification on every protected endpoint
- ✅ Webhook signature verification (HMAC)
- ✅ CORS configuration for trusted origins
- ✅ Environment variable management for secrets
- ✅ SQL injection protection via SQLAlchemy ORM

## 🎯 Key Implementation Details

### Authentication Flow
1. User signs up/in via Clerk on frontend
2. Clerk issues a JWT token
3. Frontend includes token in `Authorization` header
4. Backend validates token with Clerk SDK
5. User ID extracted from validated token for database operations

### Quota Management
- Each user gets 50 challenges per 24-hour period
- Quota automatically resets 24 hours after first usage
- Checked on every challenge generation request
- New users get quota initialized via webhook

### AI Challenge Generation
- Uses GPT-4 with structured JSON output format
- System prompt ensures consistent challenge structure
- Fallback challenge provided if API fails
- Temperature set to 0.7 for creative variation

## 🚢 Deployment

### Backend Deployment Options

**Render / Railway:**
```bash
# Use these commands in your service configuration
Build Command: cd backend && uv sync
Start Command: cd backend && python server.py
```

**Heroku:**
```bash
# Add a Procfile in backend/
web: uvicorn src.app:app --host 0.0.0.0 --port $PORT
```

### Frontend Deployment Options

**Vercel / Netlify:**
```bash
Build Command: npm run build
Output Directory: dist
```

### Production Considerations

1. **Database**: Migrate from SQLite to PostgreSQL
   ```python
   # In models.py
   engine = create_engine(os.getenv('DATABASE_URL'))
   ```

2. **CORS**: Restrict allowed origins in `app.py`
   ```python
   allow_origins=["https://yourdomain.com"]
   ```

3. **JWT Parties**: Update authorized parties in `utils.py`
   ```python
   authorized_parties=["https://yourdomain.com"]
   ```

4. **Environment Variables**: Set all env vars in your hosting platform

## 🧪 Testing

```bash
# Backend (when tests are added)
cd backend
pytest

# Frontend (when tests are added)
cd frontend
npm run test
```

*Note: Test suite is planned for future implementation*

## 📁 Project Structure

```
auth_secure_ai_app/
├── backend/
│   ├── src/
│   │   ├── routes/
│   │   │   ├── challenge.py      # Challenge endpoints
│   │   │   └── webhooks.py       # Clerk webhook handler
│   │   ├── database/
│   │   │   ├── models.py         # SQLAlchemy models
│   │   │   └── db.py             # Database operations
│   │   ├── app.py                # FastAPI app setup
│   │   ├── ai_generator.py       # OpenAI integration
│   │   └── utils.py              # Auth utilities
│   ├── server.py                 # Application entry point
│   ├── pyproject.toml            # Python dependencies
│   └── .env                      # Backend environment vars
├── frontend/
│   ├── src/
│   │   ├── auth/                 # Authentication components
│   │   ├── challenge/            # Challenge UI components
│   │   ├── history/              # History panel
│   │   ├── layout/               # Layout wrapper
│   │   ├── utils/
│   │   │   └── api.js            # API client with auth
│   │   ├── App.jsx               # Root component
│   │   └── main.jsx              # Entry point
│   ├── package.json              # Node dependencies
│   ├── vite.config.js            # Vite configuration
│   └── .env                      # Frontend environment vars
└── README.md
```

## 🔧 Troubleshooting

### Common Issues

**Issue**: `ImportError: No module named 'fastapi'`
- Solution: Run `uv sync` in backend directory

**Issue**: Frontend can't connect to backend
- Solution: Ensure backend is running on port 8000
- Check CORS settings in `backend/src/app.py`

**Issue**: `Invalid token` error
- Solution: Verify Clerk keys are correct in both `.env` files
- Ensure JWT key includes full PEM format with headers

**Issue**: Webhook not working
- Solution: Use ngrok to expose localhost for testing
- Verify webhook secret matches Clerk dashboard

**Issue**: OpenAI API errors
- Solution: Check API key is valid and has credits
- Ensure you have access to GPT-4 model

## 🔮 Future Enhancements

- [ ] Add user challenge statistics and analytics
- [ ] Implement challenge difficulty rating system
- [ ] Add support for code execution/testing
- [ ] Create challenge sharing functionality
- [ ] Add multi-language support for challenges
- [ ] Implement leaderboard and achievements
- [ ] Add comprehensive test coverage
- [ ] Migrate to PostgreSQL for production
- [ ] Add Redis for caching and rate limiting
- [ ] Implement WebSocket for real-time features

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [FastAPI](https://fastapi.tiangolo.com/) - Modern Python web framework
- [Clerk](https://clerk.com/) - Authentication and user management
- [OpenAI](https://openai.com/) - GPT-4 API for challenge generation
- [React](https://reactjs.org/) - Frontend library
- [Vite](https://vitejs.dev/) - Build tool


⭐ If you found this project helpful, please consider giving it a star!