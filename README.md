This project consists of a FastAPI backend and a React frontend. The backend provides authentication, admin routes, and database integration, while the frontend provides a UI to interact with the backend.

📂 Project Structure
JAZZ-AWS-DYNAMO/
│── backend/
│   ├── app/
│   │   ├── routers/
│   │   │   ├── admin.py
│   │   │   └── auth.py
│   │   ├── config.py
│   │   ├── database.py
│   │   ├── main.py
│   │   ├── models.py
│   │   ├── schemas.py
│   │   ├── security.py
│   │   └── global-bundle.pem
│   ├── requirements.txt
│   └── test.py
│
│── frontend/
│   ├── public/
│   ├── src/
│   │   ├── Admin.js
│   │   ├── Admin.css
│   │   └── ...
│   └── package.json
│
└── Dockerfile

⚙️ Backend Setup (FastAPI)
1. Navigate to backend
cd backend

2. Activate virtual environment
venv\Scripts\activate    # On Windows
# OR
source venv/bin/activate # On Linux/Mac

3. Install dependencies
pip install -r requirements.txt

4. Run the FastAPI server
uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload


👉 Backend will be available at:
http://localhost:8000

Swagger docs:
http://localhost:8000/docs

🎨 Frontend Setup (React)
1. Navigate to frontend
cd frontend
cd frontend   # if nested

2. Install dependencies
npm install

3. Start the React development server
npm start


👉 Frontend will be available at:
http://localhost:3000
