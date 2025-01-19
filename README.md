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
│   │   └── server.js      # Main server file
│   ├── package.json
│   └── ...
└── README.md
```

## Setup Instructions

### 1. Database Setup
**Since this application is made to run locally make sure you install mySQL, you can follow the steps mention in the blog for [windows](https://dev.mysql.com/downloads/installer/) [mac os](https://medium.com/@rodolfovmartins/how-to-install-mysql-on-mac-959df86a5319)**

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
```

Create a `.env` file in the server directory:
```
DB_HOST=localhost
DB_USER=your_username
DB_PASSWORD=your_password
DB_NAME=hackernews
PORT=3000
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
npm start
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
The server scrapes Hacker News every 100 seconds by default. You can modify this in `server.js`:
```javascript
setInterval(startScraping, 100000); // 100 seconds
```

### Database Configuration
Database settings can be modified in `dbConfig` object in `server.js`:
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
