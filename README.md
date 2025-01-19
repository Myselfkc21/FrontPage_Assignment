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
  "type": "module"  // Make sure this line is present in both client and server package.json
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

## API Documentation

### WebSocket Events

#### Client-Side Implementation
```javascript
import io from 'socket.io-client';

const socket = io('http://localhost:3000');

// Connection status
socket.on('connect', () => {
  console.log('Connected to server');
});

socket.on('disconnect', () => {
  console.log('Disconnected from server');
});

// Data handling
socket.on('initial_count', (data) => {
  console.log('Stories in last 5 minutes:', data.count);
});

socket.on('new_stories', (data) => {
  console.log('New stories:', data.stories);
});
```

#### Available WebSocket Events
- `connection`: Triggered when a client connects
- `disconnect`: Triggered when a client disconnects
- `initial_count`: Sent to client with story count from last 5 minutes
- `new_stories`: Broadcast to all clients when new stories are scraped

#### Example WebSocket Response
```javascript
// initial_count event
{
  count: 174
}

// new_stories event
{
  stories: [
    {
      title: "Example Story",
      points: "100",
      author: "user123",
      website: "example.com",
      url: "https://example.com/story",
      content: [...],
      created_at: "2024-01-19T10:00:00Z"
    }
  ]
}
```

### REST API

#### Get Top Stories
```http
GET /api/stories
```

Retrieves the top 30 stories sorted by points in descending order.

**URL:** `http://localhost:3000/api/stories`

**Method:** `GET`

**Response Format:** JSON

**Success Response:**
```json
[
  {
    "id": 1,
    "title": "Example Story Title",
    "points": "100",
    "author": "johndoe",
    "website": "example.com",
    "url": "https://example.com/story",
    "content": "[...]",
    "created_at": "2024-01-19T10:00:00Z"
  }
]
```

**Error Response:**
```json
{
  "error": "Failed to fetch stories"
}
```

**Example Usage:**

Using fetch:
```javascript
fetch('http://localhost:3000/api/stories')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));
```

Using curl:
```bash
curl http://localhost:3000/api/stories
```

Using axios:
```javascript
import axios from 'axios';

axios.get('http://localhost:3000/api/stories')
  .then(response => console.log(response.data))
  .catch(error => console.error('Error:', error));
```

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

### WebSocket Errors
```javascript
socket.on('connect_error', () => {
  console.log('Connection error');
});

socket.on('error', (data) => {
  console.error('Error:', data.message);
});
```

### REST API Errors
```javascript
fetch('http://localhost:3000/api/stories')
  .then(response => {
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    return response.json();
  })
  .catch(error => {
    console.error('Error:', error);
  });
```

## Troubleshooting

If you encounter any issues:

1. Make sure MySQL is running
2. Check your database configuration in server.js
3. Ensure all dependencies are installed
4. Check if ports 3000 and 5173 are available
5. Look for any error messages in the console

## Notes
- WebSocket updates occur every 100 seconds
- REST API returns maximum 30 stories sorted by points
- Stories are stored with full content
- All timestamps are in UTC
- CORS is enabled for local development

## Contributing
1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## License
This project is licensed under the MIT License - see the LICENSE file for details.
