# 🤝 Contributing to Lackadaisical Local Copilot

> Built in a few hours on June 11th, 2025 - proof that focused development with proper vision can create amazing things! 

We welcome contributions to make this AI copilot even better while maintaining our core principles of privacy, security, and user control.

## 🌟 Core Principles

Before contributing, please understand our foundational values:

- **Privacy First**: All data stays local, encrypted at rest
- **Security by Design**: Military-grade encryption, secure defaults
- **Zero Dependencies**: Minimal external dependencies
- **User Control**: Complete ownership of data and conversations
- **Production Ready**: Real-world deployment quality

## 🚀 Quick Start for Contributors

### Prerequisites

- Node.js 18+ 
- Git 2.20+
- Modern browser for testing
- Basic understanding of JavaScript ES6+

### Development Setup

1. **Fork and Clone**
   ```bash
   git clone https://github.com/yourusername/lackadaisical-copilot.git
   cd lackadaisical-copilot
   ```

2. **Install Dependencies**
   ```bash
   npm install
   ```

3. **Set Up Environment**
   ```bash
   cp .env.example .env
   # Edit .env with your settings
   ```

4. **Start Development Server**
   ```bash
   npm run dev
   ```

5. **Open Browser**
   ```
   http://localhost:3000
   ```

## 🎯 How to Contribute

### 🐛 Bug Reports

Found a bug? Help us fix it!

**Before reporting:**
- Check existing issues to avoid duplicates
- Test with the latest version
- Gather system information

**Create a bug report with:**
- Clear, descriptive title
- Steps to reproduce
- Expected vs. actual behavior
- System information (OS, Node.js version, browser)
- Console logs or error messages
- Screenshots if applicable

### ✨ Feature Requests

Have an idea? We'd love to hear it!

**Before requesting:**
- Check if it aligns with our core principles
- Search existing feature requests
- Consider if it benefits the broader community

**Create a feature request with:**
- Clear problem statement
- Proposed solution
- Alternative solutions considered
- Implementation complexity estimate
- Mockups or examples if applicable

### 🔧 Code Contributions

Ready to code? Here's how:

#### 1. Choose Your Contribution

**Good First Issues:**
- Documentation improvements
- Bug fixes in utilities
- New theme development
- Test coverage improvements

**Advanced Contributions:**
- Memory system enhancements
- Model integration improvements
- Security feature additions
- Performance optimizations

#### 2. Create a Branch

```bash
git checkout -b feature/your-feature-name
# or
git checkout -b fix/bug-description
```

**Branch naming conventions:**
- `feature/feature-name` - New features
- `fix/bug-description` - Bug fixes
- `docs/documentation-update` - Documentation
- `security/security-improvement` - Security enhancements
- `perf/performance-improvement` - Performance

#### 3. Make Your Changes

Follow our coding standards (see below) and ensure:
- Code is well-documented
- Error handling is comprehensive
- Security is considered
- Privacy is maintained

#### 4. Test Your Changes

```bash
# Run existing tests
npm test

# Test manually
npm start

# Check for linting issues
npm run lint
```

#### 5. Commit Your Changes

We use conventional commits:

```bash
git commit -m "feat: add new theme switching animation"
git commit -m "fix: resolve memory leak in chat system"
git commit -m "docs: update API documentation"
git commit -m "security: improve input sanitization"
```

**Commit types:**
- `feat`: New features
- `fix`: Bug fixes
- `docs`: Documentation
- `style`: Code formatting
- `refactor`: Code restructuring
- `test`: Adding tests
- `security`: Security improvements
- `perf`: Performance improvements

#### 6. Submit Pull Request

- Push your branch to GitHub
- Create a pull request
- Fill out the PR template
- Link related issues
- Request review from maintainers

## 📋 Coding Standards

### JavaScript Style Guide

**ES6+ Modern JavaScript:**
```javascript
// Good: Use const/let, arrow functions, destructuring
const { DOM, Storage } = utils;
const handleClick = (event) => {
  event.preventDefault();
  // ...
};

// Avoid: var, function declarations in modern code
var element = document.getElementById('test');
function handleClick(event) {
  // ...
}
```

**Error Handling:**
```javascript
// Good: Comprehensive error handling
try {
  const result = await api.request('/endpoint');
  return result;
} catch (error) {
  const errorInfo = ErrorHandler.handle(error, 'API Request');
  throw new Error(ErrorHandler.getUserMessage(error));
}

// Avoid: Silent failures
const result = await api.request('/endpoint').catch(() => null);
```

**Security-First:**
```javascript
// Good: Input validation and sanitization
const sanitizeInput = (input) => {
  return DOMPurify.sanitize(input.trim());
};

// Good: Secure defaults
const config = {
  encryption: true,
  https: process.env.NODE_ENV === 'production',
  ...userConfig
};

// Avoid: Trusting user input
element.innerHTML = userInput; // XSS vulnerability
```

### CSS Standards

**Modular Architecture:**
```css
/* Good: BEM methodology */
.chat-message {}
.chat-message__content {}
.chat-message--sent {}

/* Good: CSS Custom Properties */
:root {
  --primary-color: #1e293b;
  --text-color: #374151;
}

/* Good: Responsive design */
@media (max-width: 768px) {
  .chat-container {
    flex-direction: column;
  }
}
```

**Theme Support:**
```css
/* Good: Theme-aware styles */
:root[data-theme="dark"] {
  --background-color: #0a0a0a;
  --text-color: #ffffff;
}
```

### HTML Standards

**Semantic HTML:**
```html
<!-- Good: Semantic, accessible markup -->
<main role="main">
  <section aria-label="Chat interface">
    <h2>Conversation</h2>
    <div class="chat-messages" role="log" aria-live="polite">
      <!-- messages -->
    </div>
  </section>
</main>

<!-- Good: Accessibility attributes -->
<button aria-label="Send message" aria-describedby="send-help">
  <span aria-hidden="true">📤</span>
</button>
```

### Documentation Standards

**Function Documentation:**
```javascript
/**
 * Encrypt sensitive data using AES-256-GCM
 * @param {string} data - Data to encrypt
 * @param {string} passphrase - Encryption passphrase
 * @returns {Promise<Object>} Encrypted data with IV and tag
 * @throws {Error} If encryption fails
 */
async function encrypt(data, passphrase) {
  // Implementation
}
```

**README Structure:**
- Clear purpose statement
- Installation instructions
- Usage examples
- Configuration options
- Troubleshooting guide

## 🔒 Security Guidelines

### Code Security

**Input Validation:**
```javascript
// Always validate and sanitize inputs
const validateEmail = (email) => {
  const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return regex.test(email) && email.length <= 254;
};
```

**Secure Communication:**
```javascript
// Use HTTPS in production
const protocol = process.env.NODE_ENV === 'production' ? 'https:' : 'http:';

// Validate origins
const allowedOrigins = process.env.ALLOWED_ORIGINS?.split(',') || [];
```

**Data Protection:**
```javascript
// Encrypt sensitive data
const encryptedData = await encryptor.encrypt(sensitiveData);

// Clear sensitive data from memory
sensitiveData = null;
```

### Dependency Security

- Minimize external dependencies
- Regularly audit dependencies: `npm audit`
- Pin dependency versions
- Review dependency code before including

### Reporting Security Issues

**DO NOT** create public issues for security vulnerabilities.

Instead:
1. Email security concerns privately
2. Include detailed description
3. Provide reproduction steps
4. Allow time for response and fix

## 🧪 Testing Guidelines

### Test Structure

```javascript
// Good: Descriptive test structure
describe('MemoryManager', () => {
  describe('when storing encrypted memories', () => {
    it('should encrypt data before storage', async () => {
      // Test implementation
    });
    
    it('should handle encryption failures gracefully', async () => {
      // Test implementation
    });
  });
});
```

### Test Coverage

- **Unit Tests**: Individual functions and modules
- **Integration Tests**: API endpoints and database operations
- **Security Tests**: Input validation and encryption
- **Performance Tests**: Memory usage and response times

### Testing Commands

```bash
# Run all tests
npm test

# Run with coverage
npm run test:coverage

# Run integration tests
npm run test:integration

# Run security tests
npm run test:security
```

## 📚 Development Resources

### Project Architecture

```
src/
├── main.js              # Application entry point
├── config/              # Configuration management
├── storage/             # Memory & encryption systems
├── agent/               # AI model integration
├── routes/              # API endpoints
├── websocket/           # Real-time communication
├── utils/               # Shared utilities
└── models/              # Data models

public/
├── index.html           # Main interface
├── css/                 # Styling system
└── js/                  # Frontend modules
```

### Key Technologies

- **Backend**: Node.js, Express.js, SQLite, WebSocket
- **Frontend**: Vanilla JavaScript ES6+, CSS Grid/Flexbox
- **Security**: AES-256-GCM, PBKDF2, HTTPS
- **AI**: Ollama, llama.cpp, OpenAI API, Anthropic API

### Useful Scripts

```bash
# Development
npm run dev          # Start with hot reload
npm run lint         # Check code style
npm run format       # Auto-format code

# Production
npm run build        # Build for production
npm run start:prod   # Start production server

# Security
npm audit            # Check for vulnerabilities
npm run security     # Run security tests
```

## 🎨 Theme Development

Want to create a new theme? Here's how:

1. **Create theme CSS file:**
   ```css
   /* public/css/themes/mytheme.css */
   :root[data-theme="mytheme"] {
     --primary-color: #your-color;
     --background-primary: #your-bg;
     /* ... all required variables */
   }
   ```

2. **Register theme:**
   ```javascript
   // Add to themes.js
   { id: 'mytheme', name: 'My Theme', description: 'Description' }
   ```

3. **Test thoroughly:**
   - All UI components
   - Accessibility contrast
   - Dark/light variants
   - Mobile responsive

## 🌍 Internationalization

Planning to add language support:

1. **Create translation files:**
   ```json
   // locales/en.json
   {
     "chat.send": "Send",
     "memory.search": "Search memories"
   }
   ```

2. **Use translation keys:**
   ```javascript
   const message = t('chat.send');
   ```

3. **Consider RTL languages:**
   ```css
   [dir="rtl"] .chat-message {
     text-align: right;
   }
   ```

## 📝 Documentation

### Types of Documentation

- **Code Comments**: Explain complex logic
- **API Documentation**: Endpoint specifications
- **User Guide**: End-user instructions
- **Developer Guide**: Technical implementation details

### Documentation Standards

- Use clear, concise language
- Include code examples
- Keep up-to-date with changes
- Test all examples

## 🚀 Release Process

### Versioning

We use Semantic Versioning (SemVer):
- **MAJOR**: Breaking changes
- **MINOR**: New features (backward compatible)
- **PATCH**: Bug fixes (backward compatible)

### Release Checklist

- [ ] Update version numbers
- [ ] Update CHANGELOG.md
- [ ] Run full test suite
- [ ] Security audit
- [ ] Documentation review
- [ ] Create release notes

## 🤔 Questions?

### Getting Help

- **Documentation**: Check implementation guide
- **Issues**: Search existing GitHub issues
- **Discussions**: Join community discussions
- **Direct Contact**: Reach out to maintainers

### Best Practices

- Ask specific questions
- Provide context and examples
- Be patient and respectful
- Help others when you can

## 📃 License

By contributing, you agree that your contributions will be licensed under the same dual license as the project:
- MIT License for open-source use
- Commons Clause for commercial restrictions

---

## 🎉 Recognition

Contributors are recognized in:
- CONTRIBUTORS.md file
- Release notes
- Annual contributor highlights

Thank you for helping make Lackadaisical Local Copilot better for everyone! 

---

*Built with 🌪️ Purple Storm energy on June 11th, 2025*
*Because great things happen when vision meets action!* 