# Back-EW

REST API backend for **Elemental Wars** — a blockchain-based game managing NFT assets, player resources, in-game economy, and staking.

Built for game clients and frontends that need to read/write player state against a MySQL database.

---

## Features

- NFT asset management: wands, scrolls, lands, bases, magicons
- Player account creation and lookup
- In-game currency (coins) increment/decrement
- Resource inventory tracking per player
- NFT staking records
- Magicon skill and characteristic associations (many-to-many)
- Health check and server time endpoints

---

## Tech Stack

| Layer      | Technology              |
|------------|-------------------------|
| Runtime    | Node.js (ESM)           |
| Framework  | Express 4               |
| ORM        | Sequelize 6             |
| Database   | MySQL                   |
| Process    | PM2                     |
| Dev reload | nodemon                 |

---

## Getting Started

### Prerequisites

- Node.js ≥ 14
- MySQL server running locally
- Database named `elementalwars` created

### Install

```bash
git clone https://github.com/JumboMiller/Back-EW.git
cd Back-EW
npm install
```

### Configure

Database credentials are currently hardcoded in `index.js`. Update these values before running:

```js
const DB_NAME = 'elementalwars'
const USER_NAME = 'root'
const PASSWORD = 'password'   // change this
```

> **TODO**: Move to environment variables (`.env` + `dotenv`).

### Run

```bash
# Development (auto-reload)
npm run dev

# Production
npm start
```

The server starts on port **4000**.

---

## Scripts

| Command       | Description                        |
|---------------|------------------------------------|
| `npm start`   | Start with Node                    |
| `npm run dev` | Start with nodemon (auto-reload)   |

---

## API Reference

All endpoints accept and return JSON. CORS is open to all origins.

Base URL: `http://localhost:4000`

### Health

| Method | Path    | Description              |
|--------|---------|--------------------------|
| GET    | `/`     | Returns server status    |
| GET    | `/time` | Returns current UTC time |

### Users

| Method | Path                   | Description                                      |
|--------|------------------------|--------------------------------------------------|
| GET    | `/getuserid/:Email`    | Look up user by email                            |
| POST   | `/setuserid`           | Create user, initialise 12 resource slots        |
| GET    | `/userdata/:UserId`    | Get all resource balances for a user             |

**POST `/setuserid` body:**
```json
{ "UserName": "Alice", "Email": "alice@example.com", "Coins": "0" }
```

### Coins

| Method | Path           | Description           |
|--------|----------------|-----------------------|
| PUT    | `/coinsminus`  | Deduct coins from user |
| PUT    | `/coinsplus`   | Add coins to user     |

**Body:** `{ "UserId": 1, "Amount": 100 }`

### Resources

| Method | Path               | Description                   |
|--------|--------------------|-------------------------------|
| PUT    | `/resoursesminus`  | Deduct a named resource       |
| PUT    | `/resoursesplus`   | Add a named resource          |

**Body:** `{ "UserId": 1, "Name": "Iron", "RarityId": 1, "Cost": 5 }`

### Wands

| Method | Path               | Description                  |
|--------|--------------------|------------------------------|
| GET    | `/wands/:UserId`   | Get all wands for a user     |
| POST   | `/wands`           | Register a wand              |

**POST body:** `{ "AssetId": "abc123", "Name": "Fire Wand", "Rarity": "Rare", "Ability": "Burn", "UserId": 1 }`

### Scrolls

| Method | Path                 | Description                    |
|--------|----------------------|--------------------------------|
| GET    | `/scrolls/:UserId`   | Get all scrolls for a user     |
| POST   | `/scrolls`           | Register a scroll              |
| DELETE | `/scrolls`           | Remove a scroll by AssetId     |

### Lands

| Method | Path               | Description                  |
|--------|--------------------|------------------------------|
| GET    | `/lands/:UserId`   | Get all lands for a user     |
| POST   | `/lands`           | Register a land              |
| DELETE | `/lands`           | Remove a land by AssetId     |

### Bases

| Method | Path                  | Description                     |
|--------|-----------------------|---------------------------------|
| GET    | `/bases/:AssetId`     | Get bases associated with asset |
| POST   | `/bases`              | Create a base                   |
| DELETE | `/bases`              | Delete a base by BaseId         |

### Magicons

| Method | Path                   | Description                                          |
|--------|------------------------|------------------------------------------------------|
| GET    | `/magicons/:UserId`    | Get magicons with characteristics and skills         |
| POST   | `/magicons`            | Create magicon with skill and characteristic links   |

### Staking

| Method | Path                | Description                          |
|--------|---------------------|--------------------------------------|
| GET    | `/staking/:Mail`    | Get staked assets for an email       |
| POST   | `/staking`          | Record a staked asset                |

---

## Project Structure

```
Back-EW/
└── index.js          # Entry point — Express app, Sequelize models, all routes
    package.json
    package-lock.json
```

All models and routes are defined in a single `index.js`. The database schema is auto-synced via `sequelize.sync()` on startup.

---

## Configuration

| Setting    | Location    | Default       | Notes                            |
|------------|-------------|---------------|----------------------------------|
| Port       | `index.js`  | `4000`        | Hardcoded constant               |
| DB name    | `index.js`  | `elementalwars` | Hardcoded constant             |
| DB user    | `index.js`  | `root`        | Hardcoded constant               |
| DB password| `index.js`  | `password`    | **Change before deploying**      |
| DB host    | `index.js`  | `localhost`   | Hardcoded constant               |
| timestamps | Sequelize   | `false`       | Disabled globally for all models |

---

## Deployment

PM2 is included as a dependency. To run in production:

```bash
npx pm2 start index.js --name back-ew
npx pm2 save
npx pm2 startup
```

> **TODO**: Add a `ecosystem.config.js` for PM2 configuration.

---

## License

ISC
