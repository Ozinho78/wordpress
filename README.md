# WordPress Docker Setup

This repository contains a Docker Compose configuration for running WordPress with MySQL on a VPS. The setup includes:

- **WordPress Service**: WordPress image running on Apache
- **Purpose**: Provide a simple, reproducible WordPress deployment that can be quickly set up on any VPS with Docker installed.


## Table of Contents

- [Description](#description)
- [Quickstart](#quickstart)
- [Usage](#usage)
  - [Prerequisites](#prerequisites)
  - [Installation Steps](#installation-steps)
  - [Configuration](#configuration)
  - [Managing the Application](#managing-the-application)
- [Project Structure](#project-structure)

## Quickstart

### Prerequisites:
You have already Docker and Docker Compose installed.


## 1. Clone or copy the repository to your VPS
```bash
git clone -b feature/wordpress-docker-setup git@github.com:Ozinho78/wordpress.git
cd wordpress
```

## 2. Create environment file and set passwords
```bash
cp .env.example .env
nano .env
```

> [!IMPORTANT]  
> **Set secure passwords for these required variables:**
> 
> - `MYSQL_ROOT_PASSWORD`: MySQL root password (min. 16 characters)
> - `MYSQL_PASSWORD`: WordPress database user password (min. 16 characters)
> 
> Use strong, unique passwords with mixed case, numbers, and special characters.
> 
> **Example:**
> ```env
> MYSQL_ROOT_PASSWORD=X9$kL2#mP8qR4vN7wZ3@hG5jB6
> MYSQL_PASSWORD=Q4#nC8$tY2pL9wM6@rD3xF7vH5
> ```

## 3. Start WordPress
```bash
docker compose up -d
```

## 4. Access WordPress
```bash
http://<YOUR_VPS_IP>:8080
```

WordPress will be available on port 8080. Complete the installation through the web interface.

## Usage

### Prerequisites

Your VPS needs:
- Ubuntu 20.04 or newer (or similar Linux distribution)
- Minimum 1GB RAM
- Minimum 2GB free disk space
- Port 8080 available
- Internet connection

## Installation Steps

### Step 1: Install Docker

If Docker is not installed on your VPS:


### Update package index
```bash
sudo apt update
```

### Install Docker
```bash
sudo apt install -y docker.io
```

### Install Docker Compose plugin
```bash
sudo apt install -y docker-compose-plugin
```

### Add your user to the docker group
```bash
sudo usermod -aG docker $USER
```

### Logout and login again for group changes to take effect
```bash
exit
```

After logging back in, verify the installation:

```bash
docker --version
docker compose version
```

### Step 2: Prepare Project Files

Copy the project files to your VPS. You can use `git clone`, `scp`, or any other method:


### Example with git
```bash
git clone <repository-url>
cd <repository-directory>
```

### Or create directory and copy files manually
```bash
mkdir wordpress-docker
cd wordpress-docker
```
#### Copy docker-compose.yaml, .env.example, .gitignore here

### Step 3: Configure Environment Variables

### Create your `.env` file from the example:
```bash
cp .env.example .env
```

### Edit the `.env` file and set secure passwords:
```bash
nano .env
```

Change these values:
- `MYSQL_PASSWORD`: Set a strong password for the WordPress database user
- `MYSQL_ROOT_PASSWORD`: Set a strong root password for MySQL

Example:
```env
MYSQL_DATABASE=wordpress
MYSQL_USER=wpuser
MYSQL_PASSWORD=MyS3cur3P@ssw0rd!
MYSQL_ROOT_PASSWORD=R00tP@ssw0rd!Str0ng

WORDPRESS_TABLE_PREFIX=wp_
WORDPRESS_DEBUG=0
```

**Important**: Use strong, unique passwords. Never commit the `.env` file to git.

### Step 4: Deploy WordPress

### Start the Docker containers:
```bash
docker compose up -d
```

This command will:
- Download the WordPress and MySQL images (first time only)
- Create the network and volumes
- Start both containers in the background

### Check if containers are running:
```bash
docker compose ps
```

You should see both `wordpress_app` and `wordpress_db` containers in "Up" status.

### Step 5: Access WordPress

Open your browser and navigate to:
```bash
http://<YOUR_VPS_IP>:8080
```

Replace `YOUR_VPS_IP` with your actual VPS IP address.

Follow the WordPress installation wizard:
1. Select your language
2. Enter site title, admin username, and password
3. Enter your email address
4. Click "Install WordPress"

### Configuration

### Changing the Port

To use a different port than 8080, edit `docker-compose.yaml`:
```yaml
wordpress:
  ports:
    - "9090:80"  # Change 9090 to your desired port
```

Then restart:
```bash
docker compose down
docker compose up -d
```

### Database Configuration

You can modify database settings in the `.env` file:
```env
# Change database name
MYSQL_DATABASE=my_custom_db

# Change database user
MYSQL_USER=my_db_user

# Change table prefix (before first installation only)
WORDPRESS_TABLE_PREFIX=mysite_
```

After changing `.env`, restart the containers:
```bash
docker compose down
docker compose up -d
```

#### Debug Mode

To enable WordPress debug mode for troubleshooting:

```env
WORDPRESS_DEBUG=1
```

Restart containers to apply changes.

### Managing the Application

### View Logs

To see what's happening in the containers:

### All logs
```bash
docker compose logs
```

### Follow logs in real-time
```bash
docker compose logs -f
```

### Logs for specific service
```bash
docker compose logs wordpress
docker compose logs db
```

### Stop WordPress
```bash
docker compose stop
```

This stops the containers but preserves all data.

### Start Stopped Containers
```bash
docker compose start
```

### Restart Containers
```bash
docker compose restart
```

### Stop and Remove Containers

```bash
docker compose down
```

This removes containers but **keeps your data** in volumes.

### Remove Everything Including Data

**Warning**: This deletes all WordPress content and database data.

```bash
docker compose down -v
```

#### Update WordPress and MySQL

To update to the latest versions:

```bash
docker compose pull
docker compose up -d
```

## Project Structure

```
wordpress-docker/
├── docker-compose.yaml    # Main Docker Compose configuration
├── .env                   # Environment variables (create from .env.example)
├── .env.example           # Template for environment variables
├── .gitignore             # Git ignore rules
└── README.md              # This file
```

**Files to commit to git:**
- `docker-compose.yaml`
- `.env.example`
- `.gitignore`
- `README.md`

**Files to NEVER commit to git:**
- `.env` (contains passwords)

---

## Troubleshooting

**Containers won't start:**
### Check logs
```bash
docker compose logs
```

### Check if port 8080 is already in use
```bash
sudo netstat -tulpn | grep 8080
```

### Clean up unused Docker resources
```bash
docker system prune
```

### For more help, check the logs:
```bash
docker compose logs -f
```
