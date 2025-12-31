# PyTutor AI

## 📖 Tổng quan

**PyTutor AI** là hệ thống học lập trình Python thông minh, tích hợp AI và RAG (Retrieval-Augmented Generation) với Qdrant vector database. Hệ thống cung cấp môi trường lập trình tương tác với khả năng chạy code trong Docker sandbox, AI tutor với kiến thức từ tài liệu Python, và đánh giá tự động bài làm.

## ✨ Tính năng chính

### 🎓 Cho Người học
- **📝 Trình soạn thảo code**: Monaco Editor với IntelliSense và syntax highlighting cho Python
- **🐳 Sandbox an toàn**: Chạy code Python trong Docker container cô lập với giới hạn CPU/RAM
- **🤖 AI Chat Tutor**: Trợ lý AI với RAG, truy xuất kiến thức từ tài liệu Python qua Qdrant
- **💡 AI Hints**: Phân tích code và đưa ra gợi ý cải thiện thông minh
- **📚 Thư viện bài tập**: Bài tập đa dạng với phân loại theo chủ đề
- **✅ Kiểm tra tự động**: Đánh giá code với test cases và feedback chi tiết
- **📊 Dashboard cá nhân**: Theo dõi tiến độ học tập và thống kê

### 👨‍💼 Cho Quản trị viên
- **👥 Quản lý người dùng**: CRUD users, roles (student/admin)
- **📝 Quản lý bài tập**: Problems, test cases, và problem types
- **🗄️ Qdrant Management**: Import/Export tài liệu vào vector database
- **📈 Dashboard admin**: Thống kê hệ thống, submissions, users
- **🔧 System Config**: Sandbox settings, execution limits

## 🏗️ Kiến trúc hệ thống

### Clean Architecture (Backend)

```
backend/
├── api/                       # API Layer (Controllers)
│   └── routers/              # FastAPI routers
│       ├── admin.py          # Admin endpoints
│       ├── ai_tutor.py       # AI chat/hints endpoints
│       ├── problems.py       # Problems CRUD
│       ├── submissions.py    # Submissions handling
│       └── system.py         # System/config endpoints
│
├── domain/                    # Domain Layer (Business Logic)
│   ├── ai/                   # AI services
│   │   ├── hybrid_analyzer.py # Code analysis with RAG
│   │   ├── hybrid_tutor.py   # Chat tutor with RAG
│   │   └── qdrant_tutor.py   # Qdrant-based RAG
│   └── models/               # Domain models (SQLAlchemy)
│       ├── core.py           # User, Problem, Submission
│       ├── qdrant_schedule.py # Qdrant import jobs
│       └── submission.py     # Submission details
│
├── infra/                     # Infrastructure Layer
│   ├── analysis/             # Code execution & analysis
│   ├── services/             # External services
│   │   ├── docker_manager.py # Docker sandbox manager
│   │   └── scheduler.py      # Background job scheduler
│   └── utils/                # Utilities
│
├── app/                       # Application Layer
│   ├── main.py               # FastAPI app setup
│   ├── settings.py           # Configuration
│   ├── db.py                 # Database connection
│   └── auth.py               # JWT authentication
│
└── sandbox_service/           # Standalone sandbox service
    └── main.py               # WebSocket server for code execution
```

### Frontend Structure

```
frontend/
├── App.tsx                    # Main SPA với routing
├── components/               # React components
│   ├── AdminDashboard.tsx    # Admin panel
│   ├── Login.tsx             # Authentication
│   ├── ProblemList.tsx       # Problem browser
│   ├── CodeEditor.tsx        # Monaco editor wrapper
│   ├── ProblemTypeManager.tsx # Admin problem types
│   └── ...                   # Other UI components
├── services/
│   └── api.ts                # API client (fetch wrappers)
└── types.ts                  # TypeScript definitions
```

## 🛠️ Tech Stack

### Backend
- **FastAPI** (v0.109+): Modern Python web framework
- **PostgreSQL**: Production database (SQLAlchemy ORM)
- **Qdrant**: Vector database cho RAG
- **Google Gemini AI**: LLM cho chat và code analysis
- **Docker SDK**: Quản lý sandbox containers
- **SentenceTransformers**: Embedding model
- **WebSocket**: Real-time terminal communication
- **JWT**: Authentication

### Frontend
- **React 19** + **TypeScript**: UI framework
- **Vite**: Fast build tool
- **Monaco Editor** (`@monaco-editor/react`): VS Code editor
- **Lucide React**: Modern icon library
- **Recharts**: Data visualization
- **XTerm.js**: Terminal emulator for sandbox
- **React Router DOM**: SPA routing
- **React Markdown**: Markdown rendering

### DevOps & Infrastructure
- **Docker**: Container platform cho sandbox
- **PostgreSQL**: Relational database
- **Qdrant Cloud**: Managed vector database
- **Render**: Backend hosting
- **Vercel**: Frontend hosting
- **Hugging Face Spaces**: Sandbox service hosting

## 🚀 Cài đặt và Chạy

### Yêu cầu
- **Python 3.9+** và `pip`
- **Node.js 18+** và `npm`
- **Docker** (cho sandbox execution)
- **PostgreSQL** (hoặc dùng SQLite cho dev)

### 1. Clone Repository

```bash
git clone <repository-url>
cd pytutor
```

### 2. Backend Setup

```bash
cd backend
pip install -r requirements.txt
```

**Cấu hình môi trường** - Tạo file `backend/.env`:

```env
# Database
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/pytutor
# hoặc dùng SQLite cho dev:
# DATABASE_URL=sqlite:///./pytutor.db

# Authentication
SECRET_KEY=your-secret-key-change-in-production
JWT_ALGORITHM=HS256

# AI Services
GEMINI_API_KEY=your-gemini-api-key

# Qdrant Vector Database (optional, sẽ dùng in-memory nếu không set)
QDRANT_URL=https://your-qdrant-cloud-url
QDRANT_API_KEY=your-qdrant-api-key

# CORS
CORS_ALLOW_ORIGINS=http://localhost:5173,http://localhost:3000

# Sandbox Settings
SANDBOX_IMAGE=python-sandbox
EXEC_TIMEOUT_SECONDS=10
EXEC_CPU_LIMIT_PERCENT=10
EXEC_MEMORY_LIMIT_MB=512
EXEC_NETWORK_ACCESS=false

# Features
ENABLE_WS_TERMINAL=true
WARMUP_AI_ON_STARTUP=false
```

**Chạy backend:**

```bash
# Từ thư mục backend/
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

API sẽ chạy tại `http://localhost:8000`

### 3. Sandbox Service (Optional - cho WebSocket terminal)

```bash
cd backend/sandbox_service
pip install -r requirements.txt
python main.py
```

Sandbox service chạy tại `ws://localhost:8765`

### 4. Frontend Setup

```bash
cd frontend
npm install
npm run dev
```

Frontend chạy tại `http://localhost:5173`

### 5. Docker Sandbox Image

Build Docker image cho sandbox:

```bash
cd backend
docker build -f Dockerfile.sandbox -t python-sandbox .
```

## 📊 Database

### Khởi tạo Database

SQLAlchemy sẽ tự động tạo bảng khi backend start lần đầu.

### Models chính:

- **User**: Users (students, admins), authentication
- **Problem**: Coding problems với metadata
- **ProblemType**: Categories cho problems
- **Submission**: Student submissions với results
- **TestCase**: Unit tests cho problems
- **QdrantSchedule**: Background jobs cho Qdrant import

## 📝 API Documentation

Sau khi chạy backend:
- **Swagger UI**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc

### Main Endpoints:

- `POST /api/auth/login` - Authentication
- `GET /api/problems` - List problems
- `POST /api/submissions` - Submit code
- `POST /api/ai/chat` - AI tutor chat
- `POST /api/ai/hint` - Get AI hint
- `GET /api/admin/users` - Admin: list users
- `POST /api/admin/qdrant/import` - Admin: import Qdrant docs

## 🧪 Testing

### Backend

```bash
cd backend
pytest
```

### Frontend

```bash
cd frontend
npm run build  # Verify production build
```

## 🌐 Deployment

### Backend (Render)

```yaml
# render.yaml
services:
  - type: web
    name: pytutor-backend
    env: python
    buildCommand: pip install -r backend/requirements.txt
    startCommand: uvicorn app.main:app --host 0.0.0.0 --port $PORT
    envVars:
      - key: DATABASE_URL
        fromDatabase: pytutor-db
      - key: SECRET_KEY
        generateValue: true
      - key: GEMINI_API_KEY
        sync: false
```

### Frontend (Vercel)

```json
// vercel.json
{
  "buildCommand": "npm run build",
  "outputDirectory": "dist",
  "framework": "vite",
  "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }]
}
```

### Sandbox Service (Hugging Face Spaces)

Deploy như Space với Docker SDK enabled.

## 📖 Documentation

- **Thesis**: Luận văn chi tiết về system design
- **UML Diagrams**: Use case, sequence, class diagrams
- **API Docs**: `/docs` endpoint (Swagger)

## 🔐 Security

- **JWT Authentication**: Token-based auth
- **Docker Isolation**: Sandboxed code execution
- **Resource Limits**: CPU/RAM/timeout constraints
- **No network access**: Trong sandbox (mặc định)

## 📄 License

MIT License (hoặc license khác tùy chọn)

## 👨‍💻 Author

[Tên tác giả] - [Email/Contact]

## 🙏 Acknowledgments

- **Google Gemini AI** - LLM capabilities
- **Qdrant** - Vector database
- **FastAPI** & **React** teams
- **Monaco Editor** - VS Code technology
- Open-source community

---

**Built with ❤️ for Python learners**
