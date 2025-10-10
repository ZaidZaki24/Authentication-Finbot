# JAZZ-AWS-DYNAMO

FastAPI + React app with **AWS DynamoDB**.  
The backend exposes authentication-ready endpoints, **admin routes to create a DynamoDB table from the UI**, and generic CRUD for items. The frontend (React) provides a modern UI to interact with the backend.

---

## ✨ Features

- **FastAPI** backend (Python) with CORS enabled for React
- **DynamoDB integration** via `boto3` (create table + CRUD)
- Admin panel action to **create a DynamoDB table from the UI**
- Simple, environment-based configuration
- Ready for local dev or deployment (EC2/Render/Elastic Beanstalk/etc.)

---

## 🗂️ Project Structure

.
├── backend/
│ ├── main.py # Single-file FastAPI backend (DynamoDB)
│ ├── requirements.txt
│ └── .env.example
└── frontend/
└── frontend/
├── public/
└── src/
├── Admin.js
├── api.js
├── App.js
├── index.js
├── Login.js
├── Welcome.js
└── (styles, assets, etc.)

yaml
Copy code

> If your frontend folder is nested (`frontend/frontend`), that’s fine; commands below account for it.

---

## 🛠️ Prerequisites

- Node.js ≥ 18 (for React)
- Python ≥ 3.10 (for FastAPI)
- An AWS account with access to DynamoDB
- (Local only) AWS credentials (see **AWS Setup** below)

---

## 🔐 AWS Setup (One-time)

1. **Pick a region** (e.g., `us-east-1`) and use it consistently (backend `.env` + DynamoDB console).

2. **Create an IAM user** (programmatic access):
   - Go to **IAM → Users → Create user** (e.g., `dynamodb-app-dev`).
   - Attach a **least-privilege policy**. Example (replace `YOUR_ACCOUNT_ID` and region):
     ```json
     {
       "Version": "2012-10-17",
       "Statement": [{
         "Effect": "Allow",
         "Action": [
           "dynamodb:CreateTable","dynamodb:DeleteTable","dynamodb:DescribeTable",
           "dynamodb:PutItem","dynamodb:GetItem","dynamodb:UpdateItem",
           "dynamodb:DeleteItem","dynamodb:Query","dynamodb:Scan"
         ],
         "Resource": "arn:aws:dynamodb:us-east-1:YOUR_ACCOUNT_ID:table/finbot-*"
       }]
     }
     ```
   - Generate **Access key ID** and **Secret access key** and keep them safe.

3. **(Optional) Create a table in the console** (you can also create it from the Admin UI later):
   - **DynamoDB → Tables → Create table**
   - Example:  
     - Table name: `finbot-dynamo`  
     - Partition key: `id` (**String**)  
     - Billing mode: **On-demand (PAY_PER_REQUEST)** (default)  
   - You can add a Sort key later if needed.

---

## ⚙️ Backend Setup (FastAPI)

```bash
cd backend

# (Recommended) Create & activate a virtual env
python -m venv venv
# Windows
venv\Scripts\activate
# macOS/Linux
source venv/bin/activate

# Install deps
pip install -r requirements.txt

# Copy env template and fill in values
cp .env.example .env
# edit .env and set AWS keys, region, and allowed CORS origins

# Run backend
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
Backend will be available at: http://localhost:8000

.env.example

ini
Copy code
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=REPLACE_ME
AWS_SECRET_ACCESS_KEY=REPLACE_ME
CORS_ORIGINS=http://localhost:3000
🖥️ Frontend Setup (React)
bash
Copy code
cd frontend/frontend   # if your app is nested like this
npm install
npm start
Frontend will be available at: http://localhost:3000

Ensure your frontend points to the backend base URL (e.g., http://localhost:8000).
If you have src/api.js, set BASE_URL accordingly.

🔌 API Overview (DynamoDB)
The backend provides endpoints the Admin panel can call to create a table, plus generic CRUD for items.

Base URL: http://localhost:8000

Create a table
css
Copy code
POST /api/table
Body:
{
  "tableName": "finbot-dynamo",
  "partitionKey": { "name": "id", "type": "S" },
  "sortKey": { "name": "createdAt", "type": "S" }   # optional
}
Describe / Delete table
pgsql
Copy code
GET    /api/table/{name}
DELETE /api/table/{name}
Create/replace item
pgsql
Copy code
POST /api/{table}
Body: any JSON (must include PK, and SK if defined)
List / Query items
pgsql
Copy code
GET /api/{table}?pkName=userId&pkValue=u1&skName=orderId&skOp=begins_with&skValue=2025-&limit=25
# If pkName/pkValue are omitted, a full table Scan (dev only) is performed.
Get / Update / Delete one
bash
Copy code
GET    /api/{table}/{pkVal}?pkName=id&skName=...&skVal=...
PUT    /api/{table}/{pkVal}?pkName=id&skName=...&skVal=...   (Body: fields to update)
DELETE /api/{table}/{pkVal}?pkName=id&skName=...&skVal=...
Example curl

bash
Copy code
# Create table
curl -X POST http://localhost:8000/api/table \
  -H "Content-Type: application/json" \
  -d '{"tableName":"finbot-dynamo","partitionKey":{"name":"id","type":"S"}}'

# Put item
curl -X POST http://localhost:8000/api/finbot-dynamo \
  -H "Content-Type: application/json" \
  -d '{"id":"u1","name":"Ali","age":21}'
🚀 Deploy Notes
Backend: EC2 / Elastic Beanstalk / Render / Fly.io.

Set the same .env values as environment variables on your host.

Open port (e.g., 8000) and put a reverse proxy (Nginx) if needed.

Frontend: Any static host (Vercel, Netlify, S3+CloudFront).

Set REACT_APP_API_BASE_URL (or your api.js base URL) to the deployed backend URL.

🧰 Troubleshooting
AccessDeniedException: IAM policy is missing actions or wrong ARN/region/account.

ValidationException: Primary key names/types don’t match table definition.

Query returns 0: DynamoDB requires exact PK; partial matches only work on Sort Key (e.g., begins_with).

CORS errors: make sure CORS_ORIGINS includes your frontend origin
