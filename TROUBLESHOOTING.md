# Troubleshooting Guide

## 🔍 Overview

This guide provides comprehensive solutions for common issues encountered with **Lackadaisical Local Copilot**. Issues are organized by category with step-by-step solutions.

## 🚨 Quick Diagnostics

### Health Check Commands
```bash
# Check application health
curl -f http://localhost:3000/health

# Check system status
npm run status

# Run diagnostic tests
npm run test:diagnostics

# Check logs
npm run logs
```

### System Information
```bash
# Check Node.js version
node --version

# Check npm version
npm --version

# Check system resources
free -h
df -h
```

---

## 🔄 Installation Issues

### Node.js Version Issues

**Problem**: Application fails to start with Node.js version error

**Solution**:
```bash
# Check current Node.js version
node --version

# Install Node.js 18+ (using nvm)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
source ~/.bashrc
nvm install 18
nvm use 18

# Verify installation
node --version
npm --version
```

### Dependency Installation Issues

**Problem**: `npm install` fails with permission errors

**Solution**:
```bash
# Fix npm permissions
sudo chown -R $(whoami) ~/.npm
sudo chown -R $(whoami) /usr/local/lib/node_modules

# Clear npm cache
npm cache clean --force

# Reinstall dependencies
rm -rf node_modules package-lock.json
npm install
```

**Problem**: `better-sqlite3` compilation fails

**Solution**:
```bash
# Install build tools (Ubuntu/Debian)
sudo apt-get install build-essential python3

# Install build tools (CentOS/RHEL)
sudo yum groupinstall "Development Tools"

# Rebuild native modules
npm rebuild better-sqlite3

# Alternative: use pre-compiled binaries
npm install better-sqlite3 --build-from-source=false
```

---

## 💾 Database Issues

### Database Connection Errors

**Problem**: `Database connection failed` or `SQLITE_CANTOPEN`

**Solution**:
```bash
# Check database file permissions
ls -la storage/memory.sqlite

# Fix permissions
chmod 644 storage/memory.sqlite
chmod 755 storage/

# Check disk space
df -h

# Verify database integrity
npm run db:check
```

### Database Corruption

**Problem**: Database file is corrupted or unreadable

**Solution**:
```bash
# Backup current database
cp storage/memory.sqlite storage/memory.sqlite.backup

# Try to repair database
npm run db:repair

# If repair fails, restore from backup
cp storage/backups/latest.sqlite storage/memory.sqlite

# Restart application
npm restart
```

### Database Performance Issues

**Problem**: Slow database queries or high CPU usage

**Solution**:
```bash
# Run database optimization
npm run db:optimize

# Check database size
du -sh storage/memory.sqlite

# Analyze query performance
npm run db:analyze

# Clean up old data
npm run db:cleanup --older-than=30d
```

---

## 🔐 Security & Encryption Issues

### Encryption Key Issues

**Problem**: `Invalid encryption key` or `Decryption failed`

**Solution**:
```bash
# Check if salt file exists
ls -la storage/salt.key

# Regenerate salt (WARNING: Will require data migration)
npm run reset:encryption

# Verify encryption configuration
npm run test:encryption

# Check passphrase in environment
echo $ENCRYPTION_PASSPHRASE
```

### Certificate Issues

**Problem**: SSL certificate errors or HTTPS not working

**Solution**:
```bash
# Check certificate files
ls -la certs/

# Regenerate self-signed certificates
npm run setup:https

# Verify certificate validity
openssl x509 -in certs/server.crt -text -noout

# Check certificate expiration
openssl x509 -in certs/server.crt -checkend 86400
```

---

## 🧪 Testing Issues

### Test Failures

**Problem**: Tests fail with `Cannot find module` or import errors

**Solution**:
```bash
# Clear test cache
npm run test:clear

# Reinstall dependencies
rm -rf node_modules package-lock.json
npm install

# Run tests with verbose output
npm test -- --reporter=verbose

# Run specific test suite
npm test -- --grep "DatabaseManager"
```

### Test Database Issues

**Problem**: Test database conflicts or permission errors

**Solution**:
```bash
# Clean test environment
npm run test:clean

# Remove test database files
rm -f tests/test.sqlite*

# Reset test environment
npm run test:reset

# Run tests in isolation
npm test -- --run
```

---

## 🌐 Network & Connection Issues

### Port Already in Use

**Problem**: `EADDRINUSE: address already in use :::3000`

**Solution**:
```bash
# Find process using port 3000
sudo netstat -tlnp | grep :3000
# or
sudo lsof -i :3000

# Kill process using port
sudo kill -9 <PID>

# Use different port
PORT=3001 npm start
```

### WebSocket Connection Issues

**Problem**: WebSocket connection fails or frequent disconnections

**Solution**:
```bash
# Check WebSocket endpoint
curl -I -N \
  -H "Connection: Upgrade" \
  -H "Upgrade: websocket" \
  -H "Sec-WebSocket-Key: test" \
  http://localhost:3000

# Test WebSocket connection
npm run test:websocket

# Check network configuration
netstat -an | grep :3000

# Verify proxy configuration (if using nginx)
sudo nginx -t
```

### API Request Issues

**Problem**: API requests fail with 404 or 500 errors

**Solution**:
```bash
# Check API routes
curl -v http://localhost:3000/api/system/status

# Verify API token
curl -H "Authorization: Bearer $API_TOKEN" \
     http://localhost:3000/api/system/status

# Check request logs
tail -f logs/app.log

# Test API endpoints
npm run test:api
```

---

## 🤖 AI Model Issues

### Ollama Connection Issues

**Problem**: Cannot connect to Ollama server

**Solution**:
```bash
# Check Ollama service status
ollama ps

# Start Ollama service
ollama serve

# Test Ollama connection
curl http://localhost:11434/api/version

# Check Ollama models
ollama list

# Pull required model
ollama pull llama3.1:8b
```

### Model Loading Issues

**Problem**: Model fails to load or responds with errors

**Solution**:
```bash
# Check model availability
ollama list

# Test model directly
ollama run llama3.1:8b "Hello world"

# Check model file size and disk space
df -h

# Verify model configuration
echo $OLLAMA_DEFAULT_MODEL

# Update model
ollama pull llama3.1:8b
```

### llama.cpp Issues

**Problem**: llama.cpp model loading fails

**Solution**:
```bash
# Check model file exists
ls -la "$LLAMA_CPP_MODEL_PATH"

# Test model file
file "$LLAMA_CPP_MODEL_PATH"

# Check available memory
free -h

# Reduce context size
export LLAMA_CPP_CONTEXT_SIZE=2048
```

---

## 📁 File System Issues

### Permission Errors

**Problem**: `EACCES: permission denied` errors

**Solution**:
```bash
# Check file permissions
ls -la storage/

# Fix permissions
chmod 755 storage/
chmod 644 storage/*.sqlite
chmod 600 storage/salt.key

# Change ownership (if needed)
sudo chown -R $(whoami):$(whoami) storage/
```

### Disk Space Issues

**Problem**: `ENOSPC: no space left on device`

**Solution**:
```bash
# Check disk usage
df -h

# Check directory sizes
du -sh storage/
du -sh logs/
du -sh node_modules/

# Clean up log files
find logs/ -name "*.log" -mtime +7 -delete

# Clean up old backups
find storage/backups/ -name "*.sqlite" -mtime +30 -delete

# Clean npm cache
npm cache clean --force
```

---

## 🚀 Performance Issues

### High Memory Usage

**Problem**: Application consuming excessive memory

**Solution**:
```bash
# Monitor memory usage
htop
# or
ps aux --sort=-%mem | head

# Check for memory leaks
npm run test:memory

# Restart application
npm restart

# Optimize memory settings
export NODE_OPTIONS="--max-old-space-size=4096"
```

### Slow Response Times

**Problem**: API responses are slow or timeout

**Solution**:
```bash
# Check system resources
htop
iostat -x 1

# Monitor database performance
npm run db:analyze

# Check network latency
ping localhost

# Optimize database
npm run db:optimize

# Check for long-running queries
npm run db:slow-queries
```

### High CPU Usage

**Problem**: Application consuming high CPU

**Solution**:
```bash
# Monitor CPU usage
htop

# Check for CPU-intensive operations
npm run monitor:cpu

# Optimize AI model settings
echo "OLLAMA_TIMEOUT=60000" >> .env

# Reduce concurrent connections
echo "MAX_CONNECTIONS=50" >> .env
```

---

## 📊 Configuration Issues

### Environment Variables

**Problem**: Configuration not loading or invalid values

**Solution**:
```bash
# Check .env file exists
ls -la .env

# Verify environment variables
node -e "console.log(process.env.DATABASE_PATH)"

# Test configuration loading
npm run test:config

# Validate configuration
npm run validate:config

# Reset to defaults
cp .env.example .env
```

### Configuration Validation

**Problem**: Invalid configuration values

**Solution**:
```bash
# Check configuration syntax
npm run validate:config

# Test specific configuration
npm run test:config -- --section=database

# View current configuration
npm run config:show

# Reset specific configuration
npm run config:reset -- --section=memory
```

---

## 🔧 Service & Process Issues

### Service Won't Start

**Problem**: Systemd service fails to start

**Solution**:
```bash
# Check service status
sudo systemctl status copilot

# View service logs
sudo journalctl -u copilot -f

# Check service file
sudo systemctl cat copilot

# Reload service configuration
sudo systemctl daemon-reload

# Start service manually
sudo systemctl start copilot
```

### Process Management

**Problem**: Process hangs or becomes unresponsive

**Solution**:
```bash
# Find process ID
ps aux | grep node

# Check process status
kill -0 <PID>

# Graceful restart
kill -TERM <PID>

# Force kill (last resort)
kill -9 <PID>

# Restart service
sudo systemctl restart copilot
```

---

## 📋 Backup & Recovery Issues

### Backup Failures

**Problem**: Backup creation fails or backups are corrupted

**Solution**:
```bash
# Check backup directory
ls -la storage/backups/

# Test backup creation
npm run backup:create

# Verify backup integrity
npm run backup:verify

# Check disk space for backups
df -h storage/backups/

# Manual backup
cp storage/memory.sqlite storage/backups/manual_$(date +%Y%m%d_%H%M%S).sqlite
```

### Recovery Issues

**Problem**: Cannot restore from backup

**Solution**:
```bash
# List available backups
ls -la storage/backups/

# Test backup file
sqlite3 storage/backups/latest.sqlite "SELECT COUNT(*) FROM memories;"

# Restore from backup
npm run backup:restore -- --file=storage/backups/latest.sqlite

# Verify restoration
npm run db:check
```

---

## 🔍 Logging & Monitoring Issues

### Missing Logs

**Problem**: Log files not created or empty

**Solution**:
```bash
# Check log directory
ls -la logs/

# Create log directory
mkdir -p logs/
chmod 755 logs/

# Check log configuration
grep LOG .env

# Test logging
npm run test:logging

# View logs in real-time
tail -f logs/app.log
```

### Log Rotation Issues

**Problem**: Log files growing too large

**Solution**:
```bash
# Check log file sizes
du -sh logs/*

# Configure log rotation
sudo nano /etc/logrotate.d/copilot

# Test log rotation
sudo logrotate -d /etc/logrotate.d/copilot

# Manual log rotation
npm run logs:rotate
```

---

## 🔄 Update & Migration Issues

### Update Failures

**Problem**: Application update fails or breaks functionality

**Solution**:
```bash
# Backup before update
npm run backup

# Check git status
git status

# Stash local changes
git stash

# Pull updates
git pull origin main

# Restore environment
cp .env.backup .env

# Reinstall dependencies
npm install

# Run migration
npm run migrate

# Test updated application
npm test
```

### Migration Issues

**Problem**: Database migration fails

**Solution**:
```bash
# Check migration status
npm run migrate:status

# Backup before migration
npm run backup

# Run migration with verbose output
npm run migrate -- --verbose

# Rollback migration (if needed)
npm run migrate:rollback

# Manual migration
npm run migrate:manual
```

---

## 📞 Getting Help

### Diagnostic Information

When seeking help, provide this information:

```bash
# System information
uname -a
node --version
npm --version

# Application status
npm run status

# Recent logs
tail -50 logs/app.log

# Configuration (without secrets)
npm run config:show --safe

# Test results
npm run test:quick
```

### Support Channels

- **Documentation**: Check relevant guides
- **GitHub Issues**: Report bugs and issues
- **Community**: Join discussions
- **Email**: `support@lackadaisical-copilot.dev`

### Creating Bug Reports

Include:
1. **System information** (OS, Node.js version)
2. **Error messages** (exact error text)
3. **Steps to reproduce**
4. **Expected vs actual behavior**
5. **Log files** (relevant portions)
6. **Configuration** (without secrets)

---

## 🛠️ Advanced Troubleshooting

### Debug Mode

Enable debug mode for detailed logging:

```bash
# Enable debug mode
DEBUG=* npm start

# Enable specific debug categories
DEBUG=copilot:* npm start

# Debug database operations
DEBUG=copilot:database npm start
```

### Performance Profiling

Profile application performance:

```bash
# CPU profiling
node --prof src/main.js

# Memory profiling
node --inspect src/main.js

# Heap snapshot
npm run profile:heap

# Performance monitoring
npm run monitor:performance
```

### Network Debugging

Debug network issues:

```bash
# Monitor network traffic
sudo tcpdump -i lo port 3000

# Check network connections
ss -tuln | grep :3000

# Test network connectivity
telnet localhost 3000
```

---

## 📋 Maintenance Commands

### Regular Maintenance

```bash
# Weekly maintenance
npm run maintain:weekly

# Monthly maintenance
npm run maintain:monthly

# Database maintenance
npm run db:maintain

# Log maintenance
npm run logs:maintain
```

### Health Checks

```bash
# Full health check
npm run health:full

# Quick health check
npm run health:quick

# Database health
npm run health:database

# System health
npm run health:system
```

---

**Version**: 1.0.0-rc  
**Last Updated**: July 15, 2025  
**Support**: `support@lackadaisical-copilot.dev`

---

*"Every problem is an opportunity to learn. We're here to help you succeed."* 🔧 