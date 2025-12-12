# Undisputed Canvas

A web application for cinema seat booking with Google OAuth integration and an administrative management panel.

[🇺🇸 English](README.md) | [🇺🇦 Українська](docs/README.uk.md)

---

![picture 1](docs_resources/images/picture%201.png)
![picture 2](docs_resources/images/picture%202.png)
![picture 3](docs_resources/images/picture%203.png)
![picture 4](docs_resources/images/picture%204.png)

## Project Description

Undisputed Canvas is a modern cinema web application that offers an intuitive interface for selecting showtimes and booking seats in the hall for viewing. The system includes an extensive administrative panel for managing films, sessions, and halls.

## Key Features

### For Visitors

- **Browse Listings**: detailed information about films currently showing
- **Ticket Booking**: step-by-step seat reservation process
- **Hall Visualization**: interactive cinema seating layout
- **Google Authentication**: quick sign-in using Google account
- **Personal Account**: view booking history

### For Administrators

- **Film Management**: add, edit, and delete films
- **Data Parsing**: automatic retrieval of film information from UAkino
- **Session Management**: create screening schedules
- **Hall Management**: configure cinema halls and their characteristics
- **Booking Monitoring**: view all bookings with cancellation capability

### For Staff

- **View Current Bookings**: access to information about reserved seats for upcoming sessions

## Technology Stack

### Frontend

- **Next.js 15.2.5** — React framework for server-side rendering
- **React 19** — library for building interfaces
- **Tailwind CSS 4** — utility-first CSS framework
- **Framer Motion 12** — animation library
- **React Toastify** — notification system
- **Google OAuth** — user authentication
- **FontAwesome** — icon set

### Infrastructure

- **Docker** — application containerization
- **Node.js 20.17.0** — server environment

## Project Structure

```
cinema_client/
├── src/
│   ├── app/
│   │   ├── booking/[film_id]/    # Booking page
│   │   ├── profile/               # Personal account
│   │   ├── page.js                # Main page
│   │   ├── layout.js              # Main layout
│   │   └── globals.css            # Global styles
│   └── components/
│       ├── booking_page/          # Booking page components
│       ├── common/                # Common components
│       ├── contexts/              # React Context
│       ├── main_page/             # Main page components
│       ├── profile_page/          # Profile components
│       └── services/              # Helper functions and services
├── public/                        # Static resources
├── Dockerfile                     # Docker configuration
├── package.json                   # Project dependencies
└── tailwind.config.js            # Tailwind configuration
```

## Installation and Running

### Prerequisites

- Node.js version 18.18.0 or higher
- npm or yarn
- Docker (optional)

### Local Development

1. Clone the repository:
```bash
git clone https://github.com/AndriyKramar2288/cinema_client
cd cinema_client
```

2. Install dependencies:
```bash
npm install
```

3. Create a `.env.local` file in the project root:
```env
API_URL=http://localhost:8080/api
```

4. Start the development server:
```bash
npm run dev
```

5. Open your browser at [http://localhost:3000](http://localhost:3000)

### Production Build

```bash
npm run build
npm start
```

### Building and Running with Docker Compose

If using docker compose, follow these steps:

#### 1. Project Preparation

Clone both repositories into one directory:

```bash
# Create working directory
mkdir cinema-app
cd cinema-app

# Clone backend
git clone https://github.com/AndriyKramar2288/cinema_server

# Clone frontend
git clone https://github.com/AndriyKramar2288/cinema_client

# Create directory for DB secrets
mkdir db
```

#### 2. Database Configuration

Create a `db/password.txt` file with the PostgreSQL password:

```bash
echo "your_secure_database_password" > db/password.txt
```

**Important**: Make sure `db/password.txt` is added to `.gitignore`!

#### 3. Configuration Creation

Create a `compose.yaml` file in the root directory:

```yaml
services:
  server:
    build:
      context: ./cinema_server
    ports:
      - 8080:8080
    secrets:
      - db-password
    depends_on:
      db:
        condition: service_healthy

  client:
    build:
      context: ./cinema_client
    environment:
      - NODE_ENV=production
      - REACT_APP_API_URL=http://localhost:8080
    ports:
      - 3000:3000
    depends_on:
      - server

  db:
    image: postgres:16-alpine
    restart: always
    user: postgres
    secrets:
      - db-password
    volumes:
      - cinema_volume_prod:/var/lib/postgresql/data
    environment:
      - POSTGRES_DB=cinema_db
      - POSTGRES_PASSWORD_FILE=/run/secrets/db-password
    expose:
      - 5432
    healthcheck:
      test: ["CMD", "pg_isready"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  cinema_volume_prod:

secrets:
  db-password:
    file: db/password.txt
```

#### 4. Launch

```bash
# Build and start all services
docker compose up -d --build

# View logs
docker compose logs -f

# View logs for specific service
docker compose logs -f server
docker compose logs -f client
docker compose logs -f db
```

#### 5. Verification

After starting, check service availability:
- Frontend: http://localhost:3000
- Backend API: http://localhost:8080

#### 6. Management

```bash
# Stop services
docker compose down

# Stop with volume removal (WARNING: will delete DB!)
docker compose down -v

# Restart specific service
docker compose restart server

# View status
docker compose ps

# Connect to DB
docker compose exec db psql -U postgres -d cinema_db
```

## Google OAuth Configuration

1. Create a project in [Google Cloud Console](https://console.cloud.google.com)
2. Enable Google+ API
3. Create OAuth 2.0 Client ID
4. Add authorized origins: `http://localhost:3000`
5. Replace the Client ID in the file `src/components/services/nonComponents.js`

## Backend API

The application interacts with the backend API at the address specified in the `API_URL` variable. Main endpoints:

- `GET /api/films/with_sessions` — get films with available sessions
- `GET /api/session/available/by_film/{id}` — get sessions for a film
- `POST /api/booking/` — create a booking
- `POST /api/auth/google_id_token` — authenticate via Google
- `GET /api/users/session/` — get user bookings

More detailed API documentation should be provided by the backend team.

## Implementation Features

### Booking System

The booking process consists of three stages:

1. **Session Selection**: user selects the date and time of the showing
2. **Seat Selection**: interactive hall layout with visualization of available and occupied seats
3. **Data Entry**: filling in contact information for each seat

### Administrative Panel

The management panel includes three main sections:
- **Films**: CRUD operations with data parsing capability
- **Sessions**: creating screening schedules
- **Halls**: managing cinema halls

### Role System

The application supports three access levels:
- **Guest**: view films and make bookings
- **User**: additionally personal account
- **Staff**: view all bookings
- **Administrator**: full access to system management

## Styling

The project uses:
- **Tailwind CSS** for utility classes
- **Custom fonts** from Google Fonts (PT Mono, Dancing Script, Cormorant Garamond, and others)
- **Gradients and backdrop filters** for depth effect
- **Responsive design** with mobile device support