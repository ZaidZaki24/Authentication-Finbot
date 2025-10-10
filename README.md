# JAZZ-AWS-DYNAMO

FastAPI + React application with **AWS DynamoDB** integration.

The backend provides authentication-ready endpoints, **admin routes to create a DynamoDB table from the UI**, and generic CRUD operations for items. The frontend (React) offers a modern user interface to interact with the backend.

---

## ✨ Features

- **FastAPI** backend (Python) with CORS enabled for React.
- **DynamoDB** integration via `boto3` (create table + CRUD).
- Admin panel action to **create a DynamoDB table from the UI**.
- Simple, environment-based configuration for easy setup.
- Ready for local development or deployment (EC2 / Render / Elastic Beanstalk / Fly.io).

---

## 🗂️ Project Structure

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


> If your frontend folder is nested (`frontend/frontend`), that’s fine; the setup commands below account for it.

---

## 🛠️ Prerequisites

To run this project, you will need:

- **Node.js** $\geq$ 18 (for React)
- **Python** $\geq$ 3.10 (for FastAPI)
- **AWS account** with access to DynamoDB
- (Local only) **AWS credentials** (see **AWS Setup** below)

---

## 🔐 AWS Setup (one-time)

1.  **Pick a region** (e.g., `us-east-1`) and use it consistently (backend `.env` + DynamoDB console).

2.  **Create an IAM user** (programmatic access):
    * AWS Console $\rightarrow$ **IAM $\rightarrow$ Users $\rightarrow$ Create user** (e.g., `dynamodb-app-dev`).
    * Attach a **least-privilege** policy (replace `YOUR_ACCOUNT_ID` and region):

    ```json
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Action": [
            "dynamodb:CreateTable","dynamodb:DeleteTable","dynamodb:DescribeTable",
            "dynamodb:PutItem","dynamodb:GetItem","dynamodb:UpdateItem",
            "dynamodb:DeleteItem","dynamodb:Query","dynamodb:Scan"
          ],
          "Resource": "arn:aws:dynamodb:us-east-1:YOUR_ACCOUNT_ID:table/finbot-*"
        }
      ]
    }
    ```
    * **Generate Access key ID and Secret access key and keep them safe.** You will need these for the backend `.env` file.

3.  (Optional) Create a table in the console (you can also create it from the Admin UI later):
    * DynamoDB $\rightarrow$ Tables $\rightarrow$ Create table
    * Example:
        * Table name: `finbot-dynamo`
        * Partition key: `id` (String)
        * Billing mode: On-demand (PAY\_PER\_REQUEST)
        * *You can add a Sort key later if needed.*

---

## ⚙️ Backend Setup (FastAPI)

1.  Navigate to the backend directory:
    ```bash
    cd backend
    ```

2.  (Recommended) Create & activate a virtual environment:
    ```bash
    python -m venv venv
    # Windows
    venv\Scripts\activate
    # macOS/Linux
    source venv/bin/activate
    ```

3.  Install dependencies:
    ```bash
    pip install -r requirements.txt
    ```

4.  Configure environment variables:
    ```bash
    cp .env.example .env
    ```
    Edit the newly created `.env` file and set your AWS keys, region, and allowed CORS origins:

    **`.env`**
    ```ini
    AWS_REGION=us-east-1
    AWS_ACCESS_KEY_ID=REPLACE_ME
    AWS_SECRET_ACCESS_KEY=REPLACE_ME
    CORS_ORIGINS=http://localhost:3000
    ```

5.  Run the backend:
    ```bash
    uvicorn main:app --host 0.0.0.0 --port 8000 --reload
    ```
    The backend will be available at **`http://localhost:8000`**.

---

## 🖥️ Frontend Setup (React)

1.  Navigate to the frontend directory:
    ```bash
    cd frontend/frontend   # Use 'cd frontend' if your app is not nested
    ```

2.  Install dependencies:
    ```bash
    npm install
    ```

3.  Start the React development server:
    ```bash
    npm start
    ```
    The frontend will be available at **`http://localhost:3000`**.

> **Note:** Ensure your frontend configuration points to the correct backend base URL (`http://localhost:8000`). If you have `src/api.js`, set `BASE_URL` accordingly.

---

## 🔌 API Overview (DynamoDB)

The backend provides endpoints for table management (called by the Admin panel) and generic CRUD operations for items.

Base URL: `http://localhost:8000`

### Table Management

| Action | Endpoint | Method | Details |
| :--- | :--- | :--- | :--- |
| **Create Table** | `/api/table` | `POST` | Body must include `tableName`, `partitionKey` name/type, and optional `sortKey` name/type. |
| **Describe Table** | `/api/table/{name}` | `GET` | Get table details. |
| **Delete Table** | `/api/table/{name}` | `DELETE` | Delete the specified table. |

