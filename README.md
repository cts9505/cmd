# cmd
- https://gemini.google.com/share/effd50ace834
- https://www.notion.so/Salesforce-Assignments-3544a5fe0519800b8217c8bc4f53628b
- Quick setup instructions for the project.

## Prerequisites
- Git
- Node.js and npm

Install Node (Debian/Ubuntu):

```bash
sudo apt update
sudo apt install -y nodejs npm
```

## Clone repository

```bash
git clone <repo-url>
cd <repo-folder>
```

## Backend

1. Change into the backend folder:

```bash
cd backend
```

2. Create a `.env` file (example):

```env
MONGO_URI=mongodb+srv://pritam:<db_password>@cluster0.2bvlv7c.mongodb.net/?appName=Cluster0
# replace <db_password> with your database password
```

3. Install dependencies and start the backend:

```bash
npm install
npm run start
```

## Frontend

1. Change into the frontend folder:

```bash
cd frontend
```

2. Create a `.env` file (example):

```env
VITE_API_URL=http://<ip>:5000/something
# replace <ip> with the backend host/IP
```

3. Install dependencies and start the frontend (hosted for LAN access):

```bash
npm install
npm run dev -- --host 0.0.0.0
```

## Security / Ports
- The app uses ports 3000 (frontend) and 5000 (backend) by default — ensure these are secured and/or firewalled.

If you want, I can also add a `package.json` check, a CONTRIBUTING section, or commit this change. Which would you like next?
