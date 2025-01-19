# Hacker News Live

A real-time Hacker News scraper and viewer built with Node.js, React, Socket.IO, and MySQL.

## Features
- 🔄 Real-time story updates via WebSocket
- 📊 Live story count from last 5 minutes
- 💾 MySQL data persistence
- 🔍 Full content scraping from source URLs
- ⚡ Automatic updates every 100 seconds

## Tech Stack
- **Frontend**: React, TailwindCSS, Socket.IO Client
- **Backend**: Node.js, Express, Socket.IO, Cheerio
- **Database**: MySQL

## Prerequisites
- Node.js (v14 or higher)
- MySQL (v8.0 or higher)
- npm or yarn
- nodemon (for development)

## Project Structure
```
hackernews-live/
├── client/                 # Frontend React application
│   ├── src/
│   │   ├── App.jsx        # Main React component
│   │   └── ...
│   ├── package.json
│   └── ...
├── server/                 # Backend Node.js application
│   ├── server.js          # Main server file
│   ├── package.json
│   └── ...
└── README.md
```

## Setup Instructions

### 1. Database Setup
First, install MySQL if you haven't already:
- **Windows**: Download and install from [MySQL Official Website](https://dev.mysql.com/downloads/installer/)
- **macOS**: Follow this [installation guide](https://medium.com/@rodolfovmartins/how-to-install-mysql-on-mac-959df86a5319)

After installation, create the database and table:
```sql
CREATE DATABASE hackernews;
USE hackernews;

CREATE TABLE IF NOT EXISTS stories (
  id INT AUTO_INCREMENT PRIMARY KEY,
  title VARCHAR(255) NOT NULL,
  points VARCHAR(50) DEFAULT '0',
  author VARCHAR(100) DEFAULT 'unknown',
  website VARCHAR(255) DEFAULT '',
  url TEXT,
  content TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 2. Backend Setup

```bash
cd server
npm install
npm install nodemon --save-dev  # Install nodemon for development
```

In server.js, update the database configuration according to your MySQL setup:
```javascript
const dbConfig = {
  host: "localhost",
  user: "your_username",    // Replace with your MySQL username
  password: "your_password", // Replace with your MySQL password
  database: "hackernews",
};
```

Update your package.json with this script:
```json
{
  "scripts": {
    "start": "nodemon server.js"
  },
  "type": "module" //make sure this line is present in both client and server package.json
}
```

### 3. Frontend Setup
```bash
cd client
npm install
```

### 4. Running the Application

Start the backend:
```bash
cd server
npm start  # This will run nodemon server.js
```

Start the frontend:
```bash
cd client
npm run dev
```

The application will be available at:
- Frontend: http://localhost:5173
- Backend: http://localhost:3000

## WebSocket Events

### Client-Side Usage
```javascript
import io from 'socket.io-client';

const socket = io('http://localhost:3000');

// Listen for initial story count
socket.on('initial_count', (data) => {
  console.log('Stories in last 5 minutes:', data.count);
});

// Listen for new stories
socket.on('new_stories', (data) => {
  console.log('New stories:', data.stories);
});

// Connection status
socket.on('connect', () => {
  console.log('Connected to server');
});

socket.on('disconnect', () => {
  console.log('Disconnected from server');
});
```

### Server-Side Events
- `connection`: Triggered when a client connects
- `disconnect`: Triggered when a client disconnects
- `initial_count`: Sent to client with story count from last 5 minutes
- `new_stories`: Broadcast to all clients when new stories are scraped

## Configuration Options

### Scraping Interval
The server scrapes Hacker News every 100 seconds by default. Modify in `server.js`:
```javascript
setInterval(startScraping, 100000); // 100 seconds
```

### Database Configuration
Update the dbConfig object in `server.js`:
```javascript
const dbConfig = {
  host: "localhost",
  user: "your_username",
  password: "your_password",
  database: "hackernews",
};
```

## Error Handling
The application includes error handling for:
- Database connection issues
- Scraping failures
- WebSocket connection errors
- Invalid story data

## Contributing
1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## License
This project is licensed under the MIT License - see the LICENSE file for details.

## Troubleshooting

If you encounter any issues:

1. Make sure MySQL is running
2. Check your database configuration in server.js
3. Ensure all dependencies are installed
4. Check if ports 3000 and 5173 are available
5. Look for any error messages in the console
