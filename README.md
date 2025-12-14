# WordPress Docker Setup

Docker-based WordPress installation with MySQL database for VPS deployment.

## Table of Contents

- [Description](#description)
- [Quickstart](#quickstart)
- [Usage](#usage)
  - [Prerequisites](#prerequisites)
  - [Installation Steps](#installation-steps)
  - [Configuration](#configuration)
  - [Managing the Application](#managing-the-application)
  - [Customization](#customization)
- [Project Structure](#project-structure)

## Description

This repository contains a Docker Compose configuration for running WordPress with MySQL on a VPS. The setup includes:

- **WordPress Service**: Latest WordPress image running on Apache
- **MySQL Database**: MySQL 8.0 for data storage
- **Persistent Storage**: Docker volumes for database and WordPress files
- **Network Isolation**: Custom Docker network for service communication

**Purpose**: Provide a simple, reproducible WordPress deployment that can be quickly set up on any VPS with Docker installed.

## Quickstart

**If you already have Docker and Docker Compose installed:**

```bash
# 1. Clone or copy the repository to your VPS
cd /path/to/project

# 2. Create environment file and set passwords
cp .env.example .env
nano .env  # Edit and set your passwords

# 3. Start WordPress
docker compose up -d

# 4. Access WordPress
# Open browser: http://YOUR_VPS_IP:8080
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

### Installation Steps

#### Step 1: Install Docker

If Docker is not installed on your VPS:

```bash
# Update package index
sudo apt update

# Install Docker
sudo apt install -y docker.io

# Install Docker Compose plugin
sudo apt install -y docker-compose-plugin

# Add your user to the docker group
sudo usermod -aG docker $USER

# Logout and login again for group changes to take effect
exit
```

After logging back in, verify the installation:

```bash
docker --version
docker compose version
```

#### Step 2: Prepare Project Files

Copy the project files to your VPS. You can use `git clone`, `scp`, or any other method:

```bash
# Example with git
git clone <repository-url>
cd <repository-directory>

# Or create directory and copy files manually
mkdir wordpress-docker
cd wordpress-docker
# Copy docker-compose.yaml, .env.example, .gitignore here
```

#### Step 3: Configure Environment Variables

Create your `.env` file from the example:

```bash
cp .env.example .env
```

Edit the `.env` file and set secure passwords:

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

#### Step 4: Deploy WordPress

Start the Docker containers:

```bash
docker compose up -d
```

This command will:
- Download the WordPress and MySQL images (first time only)
- Create the network and volumes
- Start both containers in the background

Check if containers are running:

```bash
docker compose ps
```

You should see both `wordpress_app` and `wordpress_db` containers in "Up" status.

#### Step 5: Access WordPress

Open your browser and navigate to:

```
http://YOUR_VPS_IP:8080
```

Replace `YOUR_VPS_IP` with your actual VPS IP address.

Follow the WordPress installation wizard:
1. Select your language
2. Enter site title, admin username, and password
3. Enter your email address
4. Click "Install WordPress"

### Configuration

#### Changing the Port

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

#### Database Configuration

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

#### View Logs

To see what's happening in the containers:

```bash
# All logs
docker compose logs

# Follow logs in real-time
docker compose logs -f

# Logs for specific service
docker compose logs wordpress
docker compose logs db
```

#### Stop WordPress

```bash
docker compose stop
```

This stops the containers but preserves all data.

#### Start Stopped Containers

```bash
docker compose start
```

#### Restart Containers

```bash
docker compose restart
```

#### Stop and Remove Containers

```bash
docker compose down
```

This removes containers but **keeps your data** in volumes.

#### Remove Everything Including Data

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

### Customization

#### Add Plugins and Themes

Once WordPress is running, you can install plugins and themes through the WordPress admin interface at:

```
http://YOUR_VPS_IP:8080/wp-admin
```

Alternatively, you can copy them directly into the volume:

```bash
# Find the volume mount point
docker volume inspect wordpress-docker_wordpress_data

# Copy files to the volume
docker cp my-plugin.zip wordpress_app:/var/www/html/wp-content/plugins/
```

#### Custom PHP Configuration

Create a custom PHP configuration file and mount it:

1. Create `custom-php.ini`:
```ini
upload_max_filesize = 64M
post_max_size = 64M
memory_limit = 256M
```

2. Add to `docker-compose.yaml` under wordpress volumes:
```yaml
volumes:
  - wordpress_data:/var/www/html
  - ./custom-php.ini:/usr/local/etc/php/conf.d/custom.ini
```

3. Restart:
```bash
docker compose down
docker compose up -d
```

#### Using with Nginx Reverse Proxy

If you have Nginx already running on your VPS (e.g., for another web shop), you can configure it as a reverse proxy:

Create `/etc/nginx/sites-available/wordpress`:

```nginx
server {
    listen 80;
    server_name wordpress.yourdomain.com;

    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Enable the site:
```bash
sudo ln -s /etc/nginx/sites-available/wordpress /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

Now WordPress will be accessible via your domain name.

#### Backup Your Data

**Backup Database:**
```bash
docker compose exec db mysqldump -u${MYSQL_USER} -p${MYSQL_PASSWORD} ${MYSQL_DATABASE} > backup.sql
```

**Backup WordPress Files:**
```bash
docker run --rm -v wordpress-docker_wordpress_data:/data -v $(pwd):/backup alpine tar czf /backup/wordpress-backup.tar.gz -C /data .
```

**Restore Database:**
```bash
cat backup.sql | docker compose exec -T db mysql -u${MYSQL_USER} -p${MYSQL_PASSWORD} ${MYSQL_DATABASE}
```

**Restore WordPress Files:**
```bash
docker run --rm -v wordpress-docker_wordpress_data:/data -v $(pwd):/backup alpine tar xzf /backup/wordpress-backup.tar.gz -C /data
```

## Project Structure

```
wordpress-docker/
├── docker-compose.yaml    # Main Docker Compose configuration
├── .env                   # Environment variables (create from .env.example)
├── .env.example           # Template for environment variables
├── .gitignore            # Git ignore rules
└── README.md             # This file
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
```bash
# Check logs
docker compose logs

# Check if port 8080 is already in use
sudo netstat -tulpn | grep 8080
```

**Can't connect to WordPress:**
- Verify containers are running: `docker compose ps`
- Check firewall: `sudo ufw status`
- Allow port if needed: `sudo ufw allow 8080`

**Database connection error:**
- Verify passwords in `.env` match
- Check database container is running: `docker compose ps`

**Out of disk space:**
```bash
# Check disk usage
df -h

# Clean up unused Docker resources
docker system prune
```

For more help, check the logs:
```bash
docker compose logs -f
```
