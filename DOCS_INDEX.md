# Documentation Index

Welcome to the comprehensive documentation for this AI chat application. This index will help you find the information you need quickly.

## 📚 Documentation Files

### [API Documentation](./API_DOCUMENTATION.md)
Complete reference for all public APIs, functions, and services.

**Contains:**
- REST API endpoints (Authentication, Chat, Documents, History, Suggestions)
- Server Actions (login, register, chat operations)
- Database queries and operations
- Custom React hooks
- Utility functions
- AI tools integration
- TypeScript types and interfaces

**Best for:**
- Backend developers
- API integration
- Understanding data flow
- Database operations

### [Component Documentation](./COMPONENTS.md)
Detailed documentation for all React components in the application.

**Contains:**
- Layout components (Sidebar, Header)
- Chat components (Chat, Messages, Message)
- Artifact components (Document editors)
- Form components (Auth, Input)
- UI components (Button, Dialog, Tooltip)
- Message elements (CodeBlock, Image, Tool)

**Best for:**
- Frontend developers
- UI/UX customization
- Component usage examples
- Props and features reference

### [Development Guide](./DEVELOPMENT_GUIDE.md)
Comprehensive guide for developers working on this project.

**Contains:**
- Getting started instructions
- Project structure overview
- Development workflow
- Code patterns and best practices
- Testing strategies
- Deployment procedures
- Troubleshooting guide

**Best for:**
- New developers onboarding
- Understanding project architecture
- Learning development patterns
- Deployment and maintenance

---

## 🚀 Quick Start

### For New Developers

1. Read the [Getting Started](./DEVELOPMENT_GUIDE.md#getting-started) section
2. Review [Project Structure](./DEVELOPMENT_GUIDE.md#project-structure)
3. Follow [Development Workflow](./DEVELOPMENT_GUIDE.md#development-workflow)

### For API Integration

1. Check [REST APIs](./API_DOCUMENTATION.md#rest-apis) section
2. Review [Authentication APIs](./API_DOCUMENTATION.md#authentication-apis)
3. Understand [Error Handling](./API_DOCUMENTATION.md#best-practices)

### For Frontend Development

1. Browse [Component Documentation](./COMPONENTS.md)
2. Learn about [Chat Components](./COMPONENTS.md#chat-components)
3. Review [Component Patterns](./COMPONENTS.md#component-patterns)

---

## 📖 Common Tasks

### Creating a New Feature

1. **Design the feature**
   - Define types in [Types](./API_DOCUMENTATION.md#types)
   - Plan database schema
   - Sketch component hierarchy

2. **Implement backend**
   - Create API route: [API Integration](./DEVELOPMENT_GUIDE.md#api-integration)
   - Add server action: [Server Actions](./API_DOCUMENTATION.md#server-actions)
   - Write database queries: [Database Queries](./API_DOCUMENTATION.md#database-queries)

3. **Build frontend**
   - Create components: [Components](./COMPONENTS.md)
   - Add hooks if needed: [Custom Hooks](./API_DOCUMENTATION.md#custom-hooks)
   - Style with Tailwind: [Styling](./COMPONENTS.md#styling)

4. **Test**
   - Write unit tests: [Testing](./DEVELOPMENT_GUIDE.md#testing)
   - Add E2E tests: [E2E Tests](./DEVELOPMENT_GUIDE.md#e2e-tests)
   - Test manually

### Adding a New API Endpoint

See: [Creating a New API Endpoint](./DEVELOPMENT_GUIDE.md#creating-a-new-api-endpoint)

**Steps:**
1. Define schema with Zod
2. Implement route handler
3. Add error handling
4. Create client-side fetch function
5. Write tests

### Creating a New Component

See: [Component Patterns](./COMPONENTS.md#component-patterns)

**Steps:**
1. Define component props interface
2. Implement component logic
3. Add TypeScript types
4. Style with Tailwind
5. Add accessibility features
6. Write tests
7. Document in COMPONENTS.md

### Adding Database Tables

See: [Database Operations](./DEVELOPMENT_GUIDE.md#database-operations)

**Steps:**
1. Define schema in `lib/db/schema.ts`
2. Generate migration: `pnpm db:generate`
3. Apply migration: `pnpm db:migrate`
4. Add query functions
5. Update types

---

## 🔍 Finding Information

### By Technology

- **Next.js**: [Project Structure](./DEVELOPMENT_GUIDE.md#project-structure), [API Routes](./API_DOCUMENTATION.md#rest-apis)
- **React**: [Components](./COMPONENTS.md), [Hooks](./API_DOCUMENTATION.md#custom-hooks)
- **TypeScript**: [Types](./API_DOCUMENTATION.md#types), [Type Safety](./DEVELOPMENT_GUIDE.md#code-quality)
- **Drizzle ORM**: [Database Queries](./API_DOCUMENTATION.md#database-queries), [Migrations](./DEVELOPMENT_GUIDE.md#creating-a-migration)
- **Tailwind CSS**: [Styling](./COMPONENTS.md#styling)
- **AI SDK**: [AI Tools](./API_DOCUMENTATION.md#ai-tools)

### By Feature

- **Authentication**: [Auth APIs](./API_DOCUMENTATION.md#authentication-apis), [Auth Components](./COMPONENTS.md#form-components)
- **Chat**: [Chat APIs](./API_DOCUMENTATION.md#chat-apis), [Chat Components](./COMPONENTS.md#chat-components)
- **Documents**: [Document APIs](./API_DOCUMENTATION.md#document-apis), [Artifact Components](./COMPONENTS.md#artifact-components)
- **Streaming**: [Streaming Responses](./DEVELOPMENT_GUIDE.md#streaming-responses)
- **File Upload**: [File Upload API](./API_DOCUMENTATION.md#file-upload-api)

### By Role

**Backend Developer:**
- [API Documentation](./API_DOCUMENTATION.md)
- [Database Queries](./API_DOCUMENTATION.md#database-queries)
- [Server Actions](./API_DOCUMENTATION.md#server-actions)
- [Development Guide](./DEVELOPMENT_GUIDE.md)

**Frontend Developer:**
- [Component Documentation](./COMPONENTS.md)
- [Custom Hooks](./API_DOCUMENTATION.md#custom-hooks)
- [UI Components](./COMPONENTS.md#ui-components)
- [Styling](./COMPONENTS.md#styling)

**Full-Stack Developer:**
- All documentation files
- [Code Patterns](./DEVELOPMENT_GUIDE.md#code-patterns)
- [Best Practices](./DEVELOPMENT_GUIDE.md#best-practices)

**DevOps/SRE:**
- [Deployment](./DEVELOPMENT_GUIDE.md#deployment)
- [Troubleshooting](./DEVELOPMENT_GUIDE.md#troubleshooting)
- [Environment Variables](./API_DOCUMENTATION.md#environment-variables)

---

## 📋 Documentation Conventions

### Code Examples

All code examples follow these conventions:

**TypeScript/JavaScript:**
```typescript
// Full working example
function exampleFunction(param: string): void {
  // Implementation
}
```

**API Requests:**
```typescript
const response = await fetch('/api/endpoint', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify(data)
});
```

**Component Usage:**
```tsx
<Component
  prop1="value"
  prop2={variable}
/>
```

### File Paths

All file paths are relative to the project root:
- ✅ `/workspace/components/chat.tsx`
- ✅ `components/chat.tsx`
- ❌ `./chat.tsx`

### Links

Internal documentation links use relative paths:
- `[Text](./FILE.md#section)`

External links use full URLs:
- `[Text](https://example.com)`

---

## 🎯 Feature Reference

### Core Features

| Feature | API Docs | Component Docs | Guide |
|---------|----------|----------------|-------|
| Authentication | [Link](./API_DOCUMENTATION.md#authentication-apis) | [Link](./COMPONENTS.md#form-components) | [Link](./DEVELOPMENT_GUIDE.md#security) |
| Chat | [Link](./API_DOCUMENTATION.md#chat-apis) | [Link](./COMPONENTS.md#chat-components) | [Link](./DEVELOPMENT_GUIDE.md#api-integration) |
| Documents | [Link](./API_DOCUMENTATION.md#document-apis) | [Link](./COMPONENTS.md#artifact-components) | [Link](./DEVELOPMENT_GUIDE.md#code-patterns) |
| File Upload | [Link](./API_DOCUMENTATION.md#file-upload-api) | [Link](./COMPONENTS.md#multimodalinput) | - |
| Streaming | [Link](./API_DOCUMENTATION.md#streaming) | [Link](./COMPONENTS.md#datastreamhandler) | [Link](./DEVELOPMENT_GUIDE.md#streaming-responses) |

### Advanced Features

| Feature | Documentation |
|---------|---------------|
| Resumable Streams | [API Docs](./API_DOCUMENTATION.md#get-apichatidstream) |
| AI Tools | [API Docs](./API_DOCUMENTATION.md#ai-tools) |
| Rate Limiting | [Best Practices](./API_DOCUMENTATION.md#rate-limiting) |
| Suggestions | [API Docs](./API_DOCUMENTATION.md#requestsuggestions-session-datastream) |
| Version History | [Components](./COMPONENTS.md#versionfooter) |

---

## 🛠️ Tools and Resources

### Development Tools

- **Database**: [Drizzle Studio](https://orm.drizzle.team/drizzle-studio/overview) - Visual database explorer
- **API Testing**: [Postman](https://www.postman.com/) or [Thunder Client](https://www.thunderclient.com/)
- **E2E Testing**: [Playwright](https://playwright.dev/)
- **Type Checking**: [TypeScript](https://www.typescriptlang.org/)

### Useful Commands

```bash
# Development
pnpm dev              # Start dev server
pnpm build            # Build for production
pnpm start            # Start production server

# Database
pnpm db:migrate       # Run migrations
pnpm db:generate      # Generate migrations
pnpm db:studio        # Open Drizzle Studio

# Testing
pnpm test             # Run tests
pnpm test:e2e         # Run E2E tests
pnpm test:watch       # Watch mode

# Code Quality
pnpm lint             # Lint code
pnpm format           # Format code
pnpm type-check       # Check types
```

---

## 📝 Contributing to Documentation

### When to Update Docs

- Adding a new API endpoint
- Creating a new component
- Changing existing behavior
- Adding new features
- Fixing bugs that affect API

### How to Update

1. **API changes**: Update [API_DOCUMENTATION.md](./API_DOCUMENTATION.md)
2. **Component changes**: Update [COMPONENTS.md](./COMPONENTS.md)
3. **Process changes**: Update [DEVELOPMENT_GUIDE.md](./DEVELOPMENT_GUIDE.md)
4. **New sections**: Update this index

### Documentation Style

- Use clear, concise language
- Provide working code examples
- Include both simple and complex examples
- Add TypeScript types for all examples
- Link to related documentation

---

## 🔗 Quick Links

### API Endpoints

- [POST /api/chat](./API_DOCUMENTATION.md#post-apichat) - Send message
- [GET /api/history](./API_DOCUMENTATION.md#get-apihistory) - Get chat history
- [POST /api/document](./API_DOCUMENTATION.md#post-apidocument) - Save document
- [POST /api/files/upload](./API_DOCUMENTATION.md#post-apifilesupload) - Upload file

### Components

- [Chat](./COMPONENTS.md#chat) - Main chat interface
- [MultimodalInput](./COMPONENTS.md#multimodalinput) - Message input
- [Artifact](./COMPONENTS.md#artifact) - Document editor
- [Messages](./COMPONENTS.md#messages) - Message list

### Hooks

- [useArtifact](./API_DOCUMENTATION.md#useartifact) - Artifact state
- [useChat](./COMPONENTS.md#chat) - Chat functionality
- [useChatVisibility](./API_DOCUMENTATION.md#usechatvisibility-chatid-initialvisibilitytype) - Visibility management

### Utils

- [generateUUID](./API_DOCUMENTATION.md#generateuuid) - UUID generation
- [fetcher](./API_DOCUMENTATION.md#fetcherurl) - SWR fetcher
- [cn](./API_DOCUMENTATION.md#cninputs) - Class name merger

---

## 💡 Tips

### For Learning

1. Start with [Getting Started](./DEVELOPMENT_GUIDE.md#getting-started)
2. Read through [Project Structure](./DEVELOPMENT_GUIDE.md#project-structure)
3. Try implementing a simple feature
4. Refer to docs when stuck

### For Reference

1. Use Cmd/Ctrl+F to search within docs
2. Check examples in each section
3. Look at existing code for patterns
4. Read error messages carefully

### For Debugging

1. Check [Troubleshooting](./DEVELOPMENT_GUIDE.md#troubleshooting)
2. Enable [Debug Mode](./DEVELOPMENT_GUIDE.md#debug-mode)
3. Review error codes in [ChatSDKError](./API_DOCUMENTATION.md#chatsdkerror)
4. Check browser console and server logs

---

## 📞 Support

### Getting Help

1. **Search documentation** - Most questions are answered here
2. **Check examples** - Look at code examples in docs
3. **Review tests** - Test files show usage patterns
4. **Read source code** - Code is well-commented

### Reporting Issues

When reporting issues, include:
- Clear description of the problem
- Steps to reproduce
- Expected vs actual behavior
- Error messages and logs
- Environment details

---

## 🎓 Learning Path

### Beginner

1. [Getting Started](./DEVELOPMENT_GUIDE.md#getting-started)
2. [Project Structure](./DEVELOPMENT_GUIDE.md#project-structure)
3. [Basic Components](./COMPONENTS.md#ui-components)
4. [Simple API Endpoints](./API_DOCUMENTATION.md#rest-apis)

### Intermediate

1. [Server Actions](./API_DOCUMENTATION.md#server-actions)
2. [Database Queries](./API_DOCUMENTATION.md#database-queries)
3. [Custom Hooks](./API_DOCUMENTATION.md#custom-hooks)
4. [Chat Components](./COMPONENTS.md#chat-components)

### Advanced

1. [Streaming](./DEVELOPMENT_GUIDE.md#streaming-responses)
2. [AI Tools](./API_DOCUMENTATION.md#ai-tools)
3. [Performance](./DEVELOPMENT_GUIDE.md#performance)
4. [Testing](./DEVELOPMENT_GUIDE.md#testing)

---

**Documentation Version:** 1.0  
**Last Updated:** 2025-10-30  
**Maintainers:** Development Team

---

## Change Log

### 2025-10-30
- Initial comprehensive documentation release
- Added API Documentation
- Added Component Documentation
- Added Development Guide
- Created Documentation Index
