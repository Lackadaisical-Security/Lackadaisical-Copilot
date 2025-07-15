# Deployment Guide

## 🚀 Overview

This guide covers various deployment scenarios for **Lackadaisical Local Copilot**, from local development to production enterprise deployment. The system is designed to be flexible and secure across different environments.

## 📋 Prerequisites

### System Requirements
- **Node.js**: 18.0.0 or higher (LTS recommended)
- **RAM**: 4GB minimum, 8GB recommended
- **Storage**: 2GB minimum, 10GB recommended for models
- **OS**: Windows 10/11, macOS 10.15+, Linux (Ubuntu 18.04+)

### Network Requirements
- **Ports**: 3000 (default), 443 (HTTPS), 80 (HTTP redirect)
- **Firewall**: Configure for your deployment environment
- **SSL/TLS**: Required for production deployments

## 🔧 Environment Configuration

### Create Environment File
```bash
# Copy the example environment file
cp .env.example .env

# Edit with your configuration
nano .env
```

### Essential Environment Variables
```env
# Basic Configuration
NODE_ENV=production
PORT=3000
USE_HTTPS=true
ALLOWED_ORIGINS=https://yourdomain.com

# Security (REQUIRED)
ENCRYPTION_PASSPHRASE=your-ultra-secure-passphrase-32-chars-minimum
SESSION_SECRET=your-session-secret-32-chars-minimum
API_TOKEN=your-api-token-32-chars-minimum

# Database
DATABASE_PATH=./storage/memory.sqlite
DATABASE_BACKUP_INTERVAL=86400000

# Models (Configure as needed)
OLLAMA_BASE_URL=http://localhost:11434
OLLAMA_DEFAULT_MODEL=llama3.1:8b
```

---

## 🏠 Local Development Deployment

### Quick Start
```bash
# Clone and setup
git clone https://github.com/user/lackadaisical-copilot.git
cd lackadaisical-copilot

# Install dependencies
npm install

# Run tests (recommended)
npm test

# Start development server
npm run dev
```

### Development Features
- **Hot Reload**: Automatic restart on file changes
- **Debug Mode**: Enhanced logging and error details
- **Mock Services**: Simulated external services
- **Test Database**: Separate test database

### Development URLs
- **Application**: http://localhost:3000
- **Health Check**: http://localhost:3000/health
- **API Status**: http://localhost:3000/api/system/status

---

## 🌐 Production Deployment

### Pre-Deployment Checklist
- [ ] Environment variables configured
- [ ] SSL certificates obtained
- [ ] Database initialized and backed up
- [ ] Security tests passing
- [ ] Performance benchmarks met
- [ ] Monitoring setup configured

### Production Configuration
```env
# Production Environment
NODE_ENV=production
USE_HTTPS=true
ALLOWED_ORIGINS=https://yourdomain.com,https://www.yourdomain.com

# Security
ENCRYPTION_PASSPHRASE=your-production-passphrase
SESSION_SECRET=your-production-session-secret
API_TOKEN=your-production-api-token

# Performance
RATE_LIMIT_REQUESTS=100
RATE_LIMIT_WINDOW_MS=900000

# Logging
LOG_LEVEL=warn
LOG_FILE=/var/log/copilot/app.log
LOG_CONSOLE=false
```

### Production Deployment
```bash
# Set production environment
export NODE_ENV=production

# Install production dependencies
npm ci --only=production

# Build application
npm run build

# Start production server
npm start
```

---

## 🔐 HTTPS/SSL Configuration

### Self-Signed Certificates (Development)
```bash
# Generate self-signed certificates
npm run setup:https

# Certificates will be created in ./certs/
ls -la certs/
```

### Production Certificates (Let's Encrypt)
```bash
# Install certbot
sudo apt-get install certbot

# Obtain certificates
sudo certbot certonly --standalone -d yourdomain.com

# Configure in .env
HTTPS_CERT_PATH=/etc/letsencrypt/live/yourdomain.com/fullchain.pem
HTTPS_KEY_PATH=/etc/letsencrypt/live/yourdomain.com/privkey.pem
```

### Certificate Renewal
```bash
# Add to crontab for automatic renewal
0 12 * * * /usr/bin/certbot renew --quiet --deploy-hook "systemctl reload copilot"
```

---

## 🐳 Docker Deployment

### Dockerfile
```dockerfile
FROM node:18-alpine

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy application files
COPY . .

# Create necessary directories
RUN mkdir -p storage logs certs

# Set permissions
RUN chown -R node:node /app
USER node

# Expose port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1

# Start application
CMD ["npm", "start"]
```

### Docker Compose
```yaml
version: '3.8'

services:
  copilot:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - ./storage:/app/storage
      - ./logs:/app/logs
      - ./certs:/app/certs
    environment:
      - NODE_ENV=production
      - USE_HTTPS=true
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./certs:/etc/nginx/certs
    depends_on:
      - copilot
    restart: unless-stopped
```

### Docker Commands
```bash
# Build and start
docker-compose up -d

# View logs
docker-compose logs -f copilot

# Stop services
docker-compose down

# Update and restart
docker-compose down
docker-compose build
docker-compose up -d
```

---

## 🐧 Linux System Service

### Systemd Service File
```ini
# /etc/systemd/system/copilot.service
[Unit]
Description=Lackadaisical Local Copilot
After=network.target

[Service]
Type=simple
User=copilot
Group=copilot
WorkingDirectory=/opt/copilot
ExecStart=/usr/bin/node src/main.js
Restart=always
RestartSec=10
Environment=NODE_ENV=production
Environment=PATH=/usr/bin:/usr/local/bin
Environment=NODE_PATH=/opt/copilot/node_modules

# Security
NoNewPrivileges=true
PrivateTmp=true
ProtectSystem=strict
ReadWritePaths=/opt/copilot/storage /opt/copilot/logs

[Install]
WantedBy=multi-user.target
```

### Service Management
```bash
# Install service
sudo cp scripts/copilot.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable copilot

# Start service
sudo systemctl start copilot

# Check status
sudo systemctl status copilot

# View logs
sudo journalctl -u copilot -f

# Restart service
sudo systemctl restart copilot
```

---

## 🔄 Reverse Proxy Configuration

### Nginx Configuration
```nginx
# /etc/nginx/sites-available/copilot
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name yourdomain.com www.yourdomain.com;

    # SSL Configuration
    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    # Security Headers
    add_header X-Frame-Options DENY;
    add_header X-Content-Type-Options nosniff;
    add_header X-XSS-Protection "1; mode=block";
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";

    # Proxy to Node.js application
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }

    # WebSocket support
    location /ws {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Static files caching
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
        proxy_pass http://localhost:3000;
    }
}
```

### Enable Nginx Configuration
```bash
sudo ln -s /etc/nginx/sites-available/copilot /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## 🌍 Cloud Deployment

### AWS EC2 Deployment
```bash
# Launch EC2 instance (Ubuntu 22.04 LTS)
# Configure security group (ports 22, 80, 443)

# Connect to instance
ssh -i your-key.pem ubuntu@your-ec2-ip

# Install Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# Install application
git clone https://github.com/user/lackadaisical-copilot.git
cd lackadaisical-copilot
npm install

# Configure environment
cp .env.example .env
nano .env

# Install and configure nginx
sudo apt-get install nginx
sudo cp nginx.conf /etc/nginx/sites-available/copilot
sudo ln -s /etc/nginx/sites-available/copilot /etc/nginx/sites-enabled/

# Setup SSL with Let's Encrypt
sudo apt-get install certbot python3-certbot-nginx
sudo certbot --nginx -d yourdomain.com

# Install as service
sudo cp scripts/copilot.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable copilot
sudo systemctl start copilot
```

### DigitalOcean Droplet
```bash
# Create droplet (Ubuntu 22.04)
# Follow similar steps as AWS EC2

# Configure firewall
sudo ufw allow ssh
sudo ufw allow 'Nginx Full'
sudo ufw enable

# Setup monitoring
sudo apt-get install htop iotop
```

---

## 📊 Database Deployment

### Production Database Setup
```bash
# Ensure database directory exists
mkdir -p /opt/copilot/storage

# Set proper permissions
chown -R copilot:copilot /opt/copilot/storage
chmod 700 /opt/copilot/storage

# Configure database path
echo "DATABASE_PATH=/opt/copilot/storage/memory.sqlite" >> .env
```

### Database Backup Strategy
```bash
# Create backup script
cat > /opt/copilot/scripts/backup.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="/opt/copilot/storage/backups"
DB_PATH="/opt/copilot/storage/memory.sqlite"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p $BACKUP_DIR
cp $DB_PATH $BACKUP_DIR/memory_backup_$DATE.sqlite
gzip $BACKUP_DIR/memory_backup_$DATE.sqlite

# Keep only last 30 backups
find $BACKUP_DIR -name "memory_backup_*.sqlite.gz" -mtime +30 -delete
EOF

chmod +x /opt/copilot/scripts/backup.sh

# Add to crontab
echo "0 2 * * * /opt/copilot/scripts/backup.sh" | crontab -
```

---

## 🔍 Monitoring & Logging

### System Monitoring
```bash
# Install monitoring tools
sudo apt-get install htop iotop nethogs

# Monitor application
htop
sudo netstat -tlnp | grep :3000
```

### Log Management
```bash
# Configure log rotation
sudo tee /etc/logrotate.d/copilot << 'EOF'
/opt/copilot/logs/*.log {
    daily
    rotate 30
    compress
    delaycompress
    missingok
    notifempty
    create 644 copilot copilot
    postrotate
        systemctl reload copilot
    endscript
}
EOF
```

### Performance Monitoring
```bash
# Monitor application performance
npm run monitor

# Check system resources
free -h
df -h
iostat -x 1
```

---

## 🔒 Security Hardening

### Server Security
```bash
# Update system
sudo apt-get update && sudo apt-get upgrade -y

# Configure firewall
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow 'Nginx Full'
sudo ufw enable

# Disable root login
sudo sed -i 's/PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl restart sshd

# Configure fail2ban
sudo apt-get install fail2ban
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

### Application Security
```bash
# Set proper file permissions
sudo chown -R copilot:copilot /opt/copilot
sudo chmod -R 644 /opt/copilot
sudo chmod 755 /opt/copilot/src/main.js

# Secure configuration files
sudo chmod 600 /opt/copilot/.env
sudo chmod 600 /opt/copilot/storage/salt.key
```

---

## 🧪 Testing Deployment

### Pre-Deployment Tests
```bash
# Run test suite
npm test

# Run security tests
npm run test:security

# Run performance tests
npm run test:performance

# Verify configuration
npm run verify:config
```

### Post-Deployment Validation
```bash
# Health check
curl -f http://localhost:3000/health

# API test
curl -H "Authorization: Bearer $API_TOKEN" \
     http://localhost:3000/api/system/status

# WebSocket test
npm run test:websocket

# Load test
npm run test:load
```

---

## 🚨 Troubleshooting

### Common Issues

#### Application Won't Start
```bash
# Check logs
sudo journalctl -u copilot -f

# Check process
ps aux | grep node

# Check ports
sudo netstat -tlnp | grep :3000

# Check permissions
ls -la /opt/copilot/storage/
```

#### Database Issues
```bash
# Check database file
ls -la /opt/copilot/storage/memory.sqlite

# Test database connection
npm run test:database

# Restore from backup
cp /opt/copilot/storage/backups/latest.sqlite /opt/copilot/storage/memory.sqlite
sudo systemctl restart copilot
```

#### SSL/HTTPS Issues
```bash
# Check certificates
sudo certbot certificates

# Test SSL configuration
openssl s_client -connect yourdomain.com:443

# Check nginx configuration
sudo nginx -t
sudo systemctl status nginx
```

### Performance Issues
```bash
# Check memory usage
free -h
ps aux --sort=-%mem | head

# Check CPU usage
htop

# Check disk usage
df -h
du -sh /opt/copilot/storage/

# Monitor network
sudo nethogs
```

---

## 🔄 Update & Maintenance

### Application Updates
```bash
# Backup before update
npm run backup

# Pull latest code
git pull origin main

# Update dependencies
npm install

# Run tests
npm test

# Restart application
sudo systemctl restart copilot
```

### System Maintenance
```bash
# Update system packages
sudo apt-get update && sudo apt-get upgrade -y

# Clean logs
sudo journalctl --vacuum-time=30d

# Check disk space
df -h

# Optimize database
npm run db:optimize
```

---

## 📚 Best Practices

### Security Best Practices
1. **Use HTTPS** in production
2. **Rotate secrets** regularly
3. **Monitor logs** for security events
4. **Keep system updated**
5. **Use strong passwords**
6. **Configure firewall properly**
7. **Regular security audits**

### Performance Best Practices
1. **Monitor resource usage**
2. **Optimize database queries**
3. **Use caching strategies**
4. **Configure load balancing**
5. **Regular performance testing**
6. **Proper log management**
7. **Database maintenance**

### Operational Best Practices
1. **Automated backups**
2. **Monitoring and alerting**
3. **Documentation updates**
4. **Version control**
5. **Testing procedures**
6. **Rollback procedures**
7. **Incident response plan**

---

## 📞 Support

For deployment support:
- **Documentation**: Check this deployment guide
- **Issues**: Report on GitHub
- **Community**: Join discussions
- **Email**: `deployment-support@lackadaisical-copilot.dev`

---

**Version**: 1.0.0-rc  
**Last Updated**: December 19, 2024  
**Tested Platforms**: Ubuntu 22.04, CentOS 8, AWS EC2, DigitalOcean

---

*"Deployment is not the end, it's the beginning of your journey."* 🚀 