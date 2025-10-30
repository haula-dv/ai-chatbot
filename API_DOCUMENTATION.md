# API Documentation

This document provides comprehensive documentation for all public APIs, functions, components, and hooks in the application.

## Table of Contents

1. [REST APIs](#rest-apis)
   - [Authentication APIs](#authentication-apis)
   - [Chat APIs](#chat-apis)
   - [Document APIs](#document-apis)
   - [History API](#history-api)
   - [Suggestions API](#suggestions-api)
2. [Server Actions](#server-actions)
   - [Authentication Actions](#authentication-actions)
   - [Chat Actions](#chat-actions)
   - [Artifact Actions](#artifact-actions)
3. [Database Queries](#database-queries)
4. [Custom Hooks](#custom-hooks)
5. [React Components](#react-components)
6. [Utility Functions](#utility-functions)
7. [AI Tools](#ai-tools)
8. [Types](#types)

---

## REST APIs

### Authentication APIs

#### POST `/api/auth/guest`

Creates a guest user account for anonymous access.

**Request:**
```typescript
// No request body required
```

**Response:**
```typescript
{
  user: {
    id: string;
    email: string;
  }
}
```

**Example:**
```typescript
const response = await fetch('/api/auth/guest', {
  method: 'POST'
});
const { user } = await response.json();
```

---

### Chat APIs

#### POST `/api/chat`

Creates a new chat session or sends a message to an existing chat.

**Request Body:**
```typescript
{
  id: string;              // UUID of the chat
  message: {
    id: string;            // UUID of the message
    role: 'user';
    parts: Array<{
      type: 'text' | 'file';
      text?: string;       // For text parts (1-2000 chars)
      url?: string;        // For file parts
      name?: string;       // File name (1-100 chars)
      mediaType?: 'image/jpeg' | 'image/png';
    }>;
  };
  selectedChatModel: 'chat-model' | 'chat-model-reasoning';
  selectedVisibilityType: 'public' | 'private';
}
```

**Response:**
Server-Sent Events (SSE) stream with JSON objects

**Example:**
```typescript
const response = await fetch('/api/chat', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    id: 'chat-uuid',
    message: {
      id: 'message-uuid',
      role: 'user',
      parts: [{ type: 'text', text: 'Hello, how are you?' }]
    },
    selectedChatModel: 'chat-model',
    selectedVisibilityType: 'private'
  })
});
```

**Features:**
- Automatic title generation for new chats
- Rate limiting based on user type
- Streaming responses
- Tool support (weather, documents, suggestions)
- Resume capability with Redis

#### DELETE `/api/chat?id={chatId}`

Deletes a chat and all associated data.

**Query Parameters:**
- `id` (required): Chat UUID

**Response:**
```typescript
{
  id: string;
  userId: string;
  title: string;
  createdAt: Date;
}
```

**Example:**
```typescript
const response = await fetch('/api/chat?id=chat-uuid', {
  method: 'DELETE'
});
const deletedChat = await response.json();
```

#### GET `/api/chat/[id]/stream`

Resumes a streaming chat response.

**Path Parameters:**
- `id`: Chat UUID

**Response:**
SSE stream

**Example:**
```typescript
const eventSource = new EventSource('/api/chat/chat-uuid/stream');
eventSource.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log(data);
};
```

---

### Document APIs

#### GET `/api/document?id={documentId}`

Retrieves all versions of a document.

**Query Parameters:**
- `id` (required): Document UUID

**Response:**
```typescript
Array<{
  id: string;
  title: string;
  content: string;
  kind: 'text' | 'code' | 'image' | 'sheet';
  userId: string;
  createdAt: Date;
}>
```

**Example:**
```typescript
const response = await fetch('/api/document?id=doc-uuid');
const documents = await response.json();
```

#### POST `/api/document?id={documentId}`

Creates a new version of a document.

**Query Parameters:**
- `id` (required): Document UUID

**Request Body:**
```typescript
{
  content: string;
  title: string;
  kind: 'text' | 'code' | 'image' | 'sheet';
}
```

**Response:**
```typescript
{
  id: string;
  title: string;
  content: string;
  kind: string;
  userId: string;
  createdAt: Date;
}
```

**Example:**
```typescript
const response = await fetch('/api/document?id=doc-uuid', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    content: 'Document content',
    title: 'My Document',
    kind: 'text'
  })
});
const document = await response.json();
```

#### DELETE `/api/document?id={documentId}&timestamp={timestamp}`

Deletes document versions after a specific timestamp.

**Query Parameters:**
- `id` (required): Document UUID
- `timestamp` (required): ISO 8601 timestamp

**Response:**
```typescript
Array<Document>  // Deleted documents
```

**Example:**
```typescript
const response = await fetch(
  `/api/document?id=doc-uuid&timestamp=${new Date().toISOString()}`,
  { method: 'DELETE' }
);
const deleted = await response.json();
```

---

### History API

#### GET `/api/history?limit={limit}&starting_after={id}&ending_before={id}`

Retrieves paginated chat history for the authenticated user.

**Query Parameters:**
- `limit` (optional): Number of chats to return (default: 10)
- `starting_after` (optional): Chat ID for forward pagination
- `ending_before` (optional): Chat ID for backward pagination

**Response:**
```typescript
{
  chats: Array<{
    id: string;
    userId: string;
    title: string;
    createdAt: Date;
    visibility: 'public' | 'private';
  }>;
  hasMore: boolean;
}
```

**Example:**
```typescript
// Get first page
const response = await fetch('/api/history?limit=10');
const { chats, hasMore } = await response.json();

// Get next page
const nextResponse = await fetch(`/api/history?limit=10&starting_after=${chats[chats.length - 1].id}`);
```

---

### Suggestions API

#### GET `/api/suggestions?documentId={documentId}`

Retrieves AI-generated suggestions for a document.

**Query Parameters:**
- `documentId` (required): Document UUID

**Response:**
```typescript
Array<{
  id: string;
  documentId: string;
  originalText: string;
  suggestedText: string;
  description: string;
  isResolved: boolean;
}>
```

**Example:**
```typescript
const response = await fetch('/api/suggestions?documentId=doc-uuid');
const suggestions = await response.json();
```

---

### File Upload API

#### POST `/api/files/upload`

Uploads a file (images only).

**Request:**
Multipart form data with `file` field

**Response:**
```typescript
{
  url: string;
  pathname: string;
  contentType: string;
}
```

**Example:**
```typescript
const formData = new FormData();
formData.append('file', fileInput.files[0]);

const response = await fetch('/api/files/upload', {
  method: 'POST',
  body: formData
});
const { url } = await response.json();
```

---

## Server Actions

### Authentication Actions

#### `login(state, formData)`

Authenticates a user with email and password.

**Parameters:**
```typescript
state: LoginActionState;
formData: FormData; // Must contain 'email' and 'password'
```

**Returns:**
```typescript
{
  status: 'idle' | 'in_progress' | 'success' | 'failed' | 'invalid_data';
}
```

**Example:**
```typescript
import { login } from '@/app/(auth)/actions';

const formData = new FormData();
formData.append('email', 'user@example.com');
formData.append('password', 'password123');

const result = await login({ status: 'idle' }, formData);
if (result.status === 'success') {
  // Redirect to dashboard
}
```

**Validation:**
- Email must be valid format
- Password must be at least 6 characters

#### `register(state, formData)`

Registers a new user account.

**Parameters:**
```typescript
state: RegisterActionState;
formData: FormData; // Must contain 'email' and 'password'
```

**Returns:**
```typescript
{
  status: 'idle' | 'in_progress' | 'success' | 'failed' | 'user_exists' | 'invalid_data';
}
```

**Example:**
```typescript
import { register } from '@/app/(auth)/actions';

const formData = new FormData();
formData.append('email', 'newuser@example.com');
formData.append('password', 'securepass123');

const result = await register({ status: 'idle' }, formData);
if (result.status === 'success') {
  // User created and logged in
}
```

---

### Chat Actions

#### `saveChatModelAsCookie(model)`

Saves the user's preferred chat model as a cookie.

**Parameters:**
```typescript
model: string; // 'chat-model' | 'chat-model-reasoning'
```

**Example:**
```typescript
import { saveChatModelAsCookie } from '@/app/(chat)/actions';

await saveChatModelAsCookie('chat-model-reasoning');
```

#### `generateTitleFromUserMessage({ message })`

Generates a chat title from the first user message.

**Parameters:**
```typescript
{
  message: UIMessage;
}
```

**Returns:**
```typescript
string // Generated title (max 80 characters)
```

**Example:**
```typescript
import { generateTitleFromUserMessage } from '@/app/(chat)/actions';

const title = await generateTitleFromUserMessage({
  message: {
    id: 'msg-1',
    role: 'user',
    parts: [{ type: 'text', text: 'How do I deploy a Next.js app?' }]
  }
});
// title: "Deploying Next.js Applications"
```

#### `deleteTrailingMessages({ id })`

Deletes all messages after a specific message in a chat.

**Parameters:**
```typescript
{
  id: string; // Message ID
}
```

**Example:**
```typescript
import { deleteTrailingMessages } from '@/app/(chat)/actions';

// Delete all messages after this one (for regeneration)
await deleteTrailingMessages({ id: 'message-uuid' });
```

#### `updateChatVisibility({ chatId, visibility })`

Updates the visibility of a chat.

**Parameters:**
```typescript
{
  chatId: string;
  visibility: 'public' | 'private';
}
```

**Example:**
```typescript
import { updateChatVisibility } from '@/app/(chat)/actions';

await updateChatVisibility({
  chatId: 'chat-uuid',
  visibility: 'public'
});
```

---

### Artifact Actions

#### `getSuggestions({ documentId })`

Retrieves suggestions for a document.

**Parameters:**
```typescript
{
  documentId: string;
}
```

**Returns:**
```typescript
Array<Suggestion>
```

**Example:**
```typescript
import { getSuggestions } from '@/artifacts/actions';

const suggestions = await getSuggestions({ documentId: 'doc-uuid' });
```

---

## Database Queries

All database queries are server-side only (`'use server'`).

### User Queries

#### `getUser(email)`

Retrieves a user by email.

**Parameters:**
- `email: string`

**Returns:**
```typescript
Array<User>
```

**Example:**
```typescript
import { getUser } from '@/lib/db/queries';

const [user] = await getUser('user@example.com');
if (user) {
  console.log(`User ID: ${user.id}`);
}
```

#### `createUser(email, password)`

Creates a new user account.

**Parameters:**
- `email: string`
- `password: string` (will be hashed)

**Example:**
```typescript
import { createUser } from '@/lib/db/queries';

await createUser('newuser@example.com', 'password123');
```

#### `createGuestUser()`

Creates a guest user account.

**Returns:**
```typescript
Array<{ id: string; email: string; }>
```

---

### Chat Queries

#### `saveChat({ id, userId, title, visibility })`

Creates a new chat.

**Parameters:**
```typescript
{
  id: string;
  userId: string;
  title: string;
  visibility: 'public' | 'private';
}
```

#### `getChatById({ id })`

Retrieves a chat by ID.

**Parameters:**
```typescript
{ id: string }
```

**Returns:**
```typescript
Chat | null
```

#### `getChatsByUserId({ id, limit, startingAfter, endingBefore })`

Retrieves paginated chats for a user.

**Parameters:**
```typescript
{
  id: string;
  limit: number;
  startingAfter: string | null;
  endingBefore: string | null;
}
```

**Returns:**
```typescript
{
  chats: Array<Chat>;
  hasMore: boolean;
}
```

#### `deleteChatById({ id })`

Deletes a chat and all related data.

**Parameters:**
```typescript
{ id: string }
```

#### `updateChatVisiblityById({ chatId, visibility })`

Updates chat visibility.

**Parameters:**
```typescript
{
  chatId: string;
  visibility: 'private' | 'public';
}
```

---

### Message Queries

#### `saveMessages({ messages })`

Saves multiple messages to the database.

**Parameters:**
```typescript
{
  messages: Array<DBMessage>;
}
```

#### `getMessagesByChatId({ id })`

Retrieves all messages for a chat.

**Parameters:**
```typescript
{ id: string }
```

**Returns:**
```typescript
Array<DBMessage>
```

#### `getMessageById({ id })`

Retrieves a single message.

**Parameters:**
```typescript
{ id: string }
```

#### `deleteMessagesByChatIdAfterTimestamp({ chatId, timestamp })`

Deletes messages after a timestamp.

**Parameters:**
```typescript
{
  chatId: string;
  timestamp: Date;
}
```

#### `getMessageCountByUserId({ id, differenceInHours })`

Gets count of user messages in a time window.

**Parameters:**
```typescript
{
  id: string;
  differenceInHours: number;
}
```

**Returns:**
```typescript
number
```

---

### Document Queries

#### `saveDocument({ id, title, kind, content, userId })`

Saves a new document version.

**Parameters:**
```typescript
{
  id: string;
  title: string;
  kind: 'text' | 'code' | 'image' | 'sheet';
  content: string;
  userId: string;
}
```

#### `getDocumentsById({ id })`

Gets all versions of a document.

**Parameters:**
```typescript
{ id: string }
```

**Returns:**
```typescript
Array<Document>
```

#### `getDocumentById({ id })`

Gets the latest version of a document.

**Parameters:**
```typescript
{ id: string }
```

**Returns:**
```typescript
Document | undefined
```

#### `deleteDocumentsByIdAfterTimestamp({ id, timestamp })`

Deletes document versions after a timestamp.

**Parameters:**
```typescript
{
  id: string;
  timestamp: Date;
}
```

---

### Suggestion Queries

#### `saveSuggestions({ suggestions })`

Saves multiple suggestions.

**Parameters:**
```typescript
{
  suggestions: Array<Suggestion>;
}
```

#### `getSuggestionsByDocumentId({ documentId })`

Gets suggestions for a document.

**Parameters:**
```typescript
{ documentId: string }
```

**Returns:**
```typescript
Array<Suggestion>
```

---

### Vote Queries

#### `voteMessage({ chatId, messageId, type })`

Records a vote on a message.

**Parameters:**
```typescript
{
  chatId: string;
  messageId: string;
  type: 'up' | 'down';
}
```

#### `getVotesByChatId({ id })`

Gets all votes for a chat.

**Parameters:**
```typescript
{ id: string }
```

**Returns:**
```typescript
Array<Vote>
```

---

## Custom Hooks

### `useArtifact()`

Manages artifact state using SWR.

**Returns:**
```typescript
{
  artifact: UIArtifact;
  setArtifact: (updater: UIArtifact | ((current: UIArtifact) => UIArtifact)) => void;
  metadata: any;
  setMetadata: (data: any) => void;
}
```

**Example:**
```typescript
import { useArtifact } from '@/hooks/use-artifact';

function MyComponent() {
  const { artifact, setArtifact } = useArtifact();
  
  const showArtifact = () => {
    setArtifact(prev => ({ ...prev, isVisible: true }));
  };
  
  return <div>{artifact.title}</div>;
}
```

### `useArtifactSelector(selector)`

Selects a specific value from artifact state.

**Parameters:**
```typescript
selector: (state: UIArtifact) => T
```

**Returns:**
```typescript
T
```

**Example:**
```typescript
import { useArtifactSelector } from '@/hooks/use-artifact';

function MyComponent() {
  const isVisible = useArtifactSelector(state => state.isVisible);
  const title = useArtifactSelector(state => state.title);
  
  return <div>{isVisible && title}</div>;
}
```

### `useChatVisibility({ chatId, initialVisibilityType })`

Manages chat visibility state.

**Parameters:**
```typescript
{
  chatId: string;
  initialVisibilityType: 'public' | 'private';
}
```

**Returns:**
```typescript
{
  visibilityType: 'public' | 'private';
  setVisibilityType: (type: 'public' | 'private') => void;
}
```

**Example:**
```typescript
import { useChatVisibility } from '@/hooks/use-chat-visibility';

function ChatSettings({ chatId }) {
  const { visibilityType, setVisibilityType } = useChatVisibility({
    chatId,
    initialVisibilityType: 'private'
  });
  
  return (
    <button onClick={() => setVisibilityType('public')}>
      Make {visibilityType === 'private' ? 'Public' : 'Private'}
    </button>
  );
}
```

### `useMessages({ chatId, status })`

Manages message list state with auto-scrolling.

**Parameters:**
```typescript
{
  chatId: string;
  status: UseChatHelpers['status'];
}
```

**Returns:**
```typescript
{
  containerRef: RefObject<HTMLDivElement>;
  endRef: RefObject<HTMLDivElement>;
  isAtBottom: boolean;
  scrollToBottom: () => void;
  onViewportEnter: () => void;
  onViewportLeave: () => void;
  hasSentMessage: boolean;
}
```

**Example:**
```typescript
import { useMessages } from '@/hooks/use-messages';

function MessageList({ chatId, status }) {
  const { containerRef, endRef, scrollToBottom } = useMessages({ chatId, status });
  
  return (
    <div ref={containerRef}>
      {/* Messages */}
      <div ref={endRef} />
    </div>
  );
}
```

### `useScrollToBottom()`

Provides scroll-to-bottom functionality with bottom detection.

**Returns:**
```typescript
{
  containerRef: RefObject<HTMLDivElement>;
  endRef: RefObject<HTMLDivElement>;
  isAtBottom: boolean;
  scrollToBottom: () => void;
  onViewportEnter: () => void;
  onViewportLeave: () => void;
}
```

**Example:**
```typescript
import { useScrollToBottom } from '@/hooks/use-scroll-to-bottom';

function ScrollableContent() {
  const { containerRef, endRef, isAtBottom, scrollToBottom } = useScrollToBottom();
  
  return (
    <>
      <div ref={containerRef}>
        {/* Content */}
        <div ref={endRef} />
      </div>
      {!isAtBottom && (
        <button onClick={scrollToBottom}>Scroll to Bottom</button>
      )}
    </>
  );
}
```

### `useAutoResume({ autoResume, initialMessages, resumeStream, setMessages })`

Automatically resumes interrupted streaming sessions.

**Parameters:**
```typescript
{
  autoResume: boolean;
  initialMessages: ChatMessage[];
  resumeStream: () => void;
  setMessages: (messages: ChatMessage[]) => void;
}
```

**Example:**
```typescript
import { useAutoResume } from '@/hooks/use-auto-resume';

function Chat({ autoResume, initialMessages }) {
  const { resumeStream, setMessages } = useChat();
  
  useAutoResume({
    autoResume,
    initialMessages,
    resumeStream,
    setMessages
  });
  
  // Component renders chat
}
```

### `useMobile()`

Detects if the device is mobile (width < 768px).

**Returns:**
```typescript
boolean
```

**Example:**
```typescript
import { useMobile } from '@/hooks/use-mobile';

function ResponsiveComponent() {
  const isMobile = useMobile();
  
  return (
    <div>
      {isMobile ? <MobileView /> : <DesktopView />}
    </div>
  );
}
```

---

## React Components

### `<Chat>`

Main chat interface component.

**Props:**
```typescript
{
  id: string;                              // Chat UUID
  initialMessages: ChatMessage[];          // Initial message history
  initialChatModel: string;                // Selected model ID
  initialVisibilityType: VisibilityType;   // 'public' | 'private'
  isReadonly: boolean;                     // Disable input
  session: Session;                        // User session
  autoResume: boolean;                     // Auto-resume streams
  initialLastContext?: LanguageModelUsage; // Token usage
}
```

**Example:**
```typescript
import { Chat } from '@/components/chat';

<Chat
  id="chat-123"
  initialMessages={[]}
  initialChatModel="chat-model"
  initialVisibilityType="private"
  isReadonly={false}
  session={session}
  autoResume={true}
/>
```

### `<MultimodalInput>`

Input component for text and file messages.

**Props:**
```typescript
{
  chatId: string;
  input: string;
  setInput: Dispatch<SetStateAction<string>>;
  status: 'ready' | 'submitted' | 'streaming';
  stop: () => void;
  attachments: Attachment[];
  setAttachments: Dispatch<SetStateAction<Attachment[]>>;
  messages: UIMessage[];
  setMessages: (messages: UIMessage[]) => void;
  sendMessage: (message: ChatMessage) => void;
  selectedVisibilityType: VisibilityType;
  selectedModelId: string;
  usage?: LanguageModelUsage;
}
```

**Features:**
- Text input with auto-resize
- File attachments (images)
- Model selector
- Token usage display
- Submit button with loading states

**Example:**
```typescript
<MultimodalInput
  chatId="chat-123"
  input={input}
  setInput={setInput}
  status={status}
  stop={stop}
  attachments={attachments}
  setAttachments={setAttachments}
  messages={messages}
  setMessages={setMessages}
  sendMessage={sendMessage}
  selectedVisibilityType="private"
  selectedModelId="chat-model"
  usage={usage}
/>
```

### `<Artifact>`

Displays and edits artifacts (documents, code, images, sheets).

**Props:**
```typescript
{
  chatId: string;
  input: string;
  setInput: Dispatch<SetStateAction<string>>;
  status: UseChatHelpers['status'];
  stop: UseChatHelpers['stop'];
  attachments: Attachment[];
  setAttachments: Dispatch<SetStateAction<Attachment[]>>;
  messages: ChatMessage[];
  setMessages: UseChatHelpers['setMessages'];
  votes: Array<Vote> | undefined;
  sendMessage: UseChatHelpers['sendMessage'];
  regenerate: UseChatHelpers['regenerate'];
  isReadonly: boolean;
  selectedVisibilityType: VisibilityType;
  selectedModelId: string;
}
```

**Supported Artifact Types:**
- `text`: Rich text documents
- `code`: Code with syntax highlighting
- `image`: Image generation
- `sheet`: Spreadsheets

### `<Messages>`

Displays chat message history.

**Props:**
```typescript
{
  chatId: string;
  status: UseChatHelpers['status'];
  votes: Array<Vote> | undefined;
  messages: ChatMessage[];
  setMessages: UseChatHelpers['setMessages'];
  regenerate: UseChatHelpers['regenerate'];
  isReadonly: boolean;
  isArtifactVisible: boolean;
  selectedModelId: string;
}
```

### `<Message>`

Single message component with vote buttons and actions.

**Props:**
```typescript
{
  chatId: string;
  message: ChatMessage;
  vote: Vote | undefined;
  isLoading: boolean;
  setMessages: UseChatHelpers['setMessages'];
  regenerate: UseChatHelpers['regenerate'];
  isReadonly: boolean;
}
```

### `<AuthForm>`

Authentication form for login/register.

**Props:**
```typescript
{
  action: 'login' | 'register';
  children: ReactNode;
  defaultEmail?: string;
}
```

**Example:**
```typescript
<AuthForm action="login">
  <SubmitButton>Sign In</SubmitButton>
</AuthForm>
```

### `<ModelSelector>`

Dropdown for selecting chat models.

**Props:**
```typescript
{
  selectedModelId: string;
  className?: string;
}
```

**Example:**
```typescript
<ModelSelector selectedModelId="chat-model" />
```

### `<VisibilitySelector>`

Toggle for chat visibility (public/private).

**Props:**
```typescript
{
  chatId: string;
  selectedVisibilityType: VisibilityType;
  className?: string;
}
```

---

## Utility Functions

### `cn(...inputs)`

Merges Tailwind CSS class names.

**Parameters:**
```typescript
...inputs: ClassValue[]
```

**Returns:**
```typescript
string
```

**Example:**
```typescript
import { cn } from '@/lib/utils';

const className = cn(
  'base-class',
  isActive && 'active-class',
  'hover:bg-blue-500'
);
```

### `generateUUID()`

Generates a UUID v4 string.

**Returns:**
```typescript
string
```

**Example:**
```typescript
import { generateUUID } from '@/lib/utils';

const id = generateUUID();
// "a1b2c3d4-e5f6-4a7b-8c9d-0e1f2a3b4c5d"
```

### `fetcher(url)`

SWR fetcher with error handling.

**Parameters:**
```typescript
url: string
```

**Returns:**
```typescript
Promise<any>
```

**Example:**
```typescript
import useSWR from 'swr';
import { fetcher } from '@/lib/utils';

const { data, error } = useSWR('/api/data', fetcher);
```

### `fetchWithErrorHandlers(input, init)`

Fetch wrapper with ChatSDKError handling and offline detection.

**Parameters:**
```typescript
input: RequestInfo | URL;
init?: RequestInit;
```

**Returns:**
```typescript
Promise<Response>
```

**Example:**
```typescript
import { fetchWithErrorHandlers } from '@/lib/utils';

try {
  const response = await fetchWithErrorHandlers('/api/chat', {
    method: 'POST',
    body: JSON.stringify(data)
  });
  const result = await response.json();
} catch (error) {
  if (error instanceof ChatSDKError) {
    console.error(error.code, error.message);
  }
}
```

### `convertToUIMessages(messages)`

Converts database messages to UI format.

**Parameters:**
```typescript
messages: DBMessage[]
```

**Returns:**
```typescript
ChatMessage[]
```

**Example:**
```typescript
import { convertToUIMessages } from '@/lib/utils';

const dbMessages = await getMessagesByChatId({ id: 'chat-123' });
const uiMessages = convertToUIMessages(dbMessages);
```

### `getTextFromMessage(message)`

Extracts text content from a message.

**Parameters:**
```typescript
message: ChatMessage
```

**Returns:**
```typescript
string
```

**Example:**
```typescript
import { getTextFromMessage } from '@/lib/utils';

const text = getTextFromMessage(message);
// "Hello, how are you?"
```

### `getMostRecentUserMessage(messages)`

Gets the last user message from a list.

**Parameters:**
```typescript
messages: Array<UIMessage>
```

**Returns:**
```typescript
UIMessage | undefined
```

---

## AI Tools

### `getWeather`

Fetches current weather data from Open-Meteo API.

**Input Schema:**
```typescript
{
  latitude: number;
  longitude: number;
}
```

**Returns:**
Weather data including temperature, sunrise/sunset

**Example (used by AI):**
```
User: "What's the weather like?"
AI: *calls getWeather({ latitude: 37.7749, longitude: -122.4194 })*
```

### `createDocument({ session, dataStream })`

Creates a new document artifact.

**Input Schema:**
```typescript
{
  title: string;
  kind: 'text' | 'code' | 'image' | 'sheet';
}
```

**Returns:**
```typescript
{
  id: string;
  title: string;
  kind: string;
  content: string;
}
```

**Flow:**
1. Generates UUID for document
2. Streams kind, id, and title to client
3. Calls appropriate document handler
4. Streams content as it's generated
5. Saves to database

### `updateDocument({ session, dataStream })`

Updates an existing document.

**Input Schema:**
```typescript
{
  id: string;
  description: string;
}
```

**Returns:**
```typescript
{
  id: string;
  title: string;
  kind: string;
  content: string;
}
```

**Flow:**
1. Retrieves existing document
2. Streams updates to client
3. Calls document handler with update description
4. Saves new version to database

### `requestSuggestions({ session, dataStream })`

Generates editing suggestions for a document.

**Input Schema:**
```typescript
{
  documentId: string;
}
```

**Returns:**
```typescript
{
  id: string;
  title: string;
  kind: string;
  message: string;
}
```

**Flow:**
1. Retrieves document
2. Uses AI to generate suggestions (max 5)
3. Streams each suggestion to client
4. Saves suggestions to database

---

## Types

### `ChatMessage`

Extended UI message with metadata.

```typescript
type ChatMessage = UIMessage<
  MessageMetadata,
  CustomUIDataTypes,
  ChatTools
>;

interface MessageMetadata {
  createdAt: string; // ISO 8601
}
```

### `Attachment`

File attachment metadata.

```typescript
interface Attachment {
  name: string;
  url: string;
  contentType: string;
}
```

### `VisibilityType`

Chat visibility setting.

```typescript
type VisibilityType = 'public' | 'private';
```

### `ArtifactKind`

Type of artifact/document.

```typescript
type ArtifactKind = 'text' | 'code' | 'image' | 'sheet';
```

### `ChatModel`

Chat model configuration.

```typescript
interface ChatModel {
  id: string;
  name: string;
  description: string;
}
```

**Available Models:**
- `chat-model`: "Grok Vision" - Multimodal with vision
- `chat-model-reasoning`: "Grok Reasoning" - Chain-of-thought reasoning

### `UIArtifact`

Client-side artifact state.

```typescript
interface UIArtifact {
  title: string;
  documentId: string;
  kind: ArtifactKind;
  content: string;
  isVisible: boolean;
  status: 'streaming' | 'idle';
  boundingBox: {
    top: number;
    left: number;
    width: number;
    height: number;
  };
}
```

### `ChatSDKError`

Custom error class with error codes.

```typescript
class ChatSDKError extends Error {
  code: ErrorCode;
  cause?: string;
  
  toResponse(): Response;
}
```

**Error Codes:**
- `unauthorized:chat` - Not authenticated
- `forbidden:chat` - Access denied
- `not_found:chat` - Chat not found
- `not_found:document` - Document not found
- `rate_limit:chat` - Rate limit exceeded
- `bad_request:api` - Invalid request
- `offline:chat` - Offline

---

## Environment Variables

Required environment variables:

```bash
# Database
POSTGRES_URL=postgresql://...

# Authentication
AUTH_SECRET=your-secret-key
NEXTAUTH_URL=http://localhost:3000

# Redis (optional - for resumable streams)
REDIS_URL=redis://...

# AI Provider
# Configure in lib/ai/providers.ts
```

---

## Best Practices

### Error Handling

Always wrap API calls in try-catch:

```typescript
try {
  const response = await fetchWithErrorHandlers('/api/chat', options);
  const data = await response.json();
} catch (error) {
  if (error instanceof ChatSDKError) {
    toast({ type: 'error', description: error.message });
  }
}
```

### Authentication

Check session before accessing protected routes:

```typescript
import { auth } from '@/app/(auth)/auth';

const session = await auth();
if (!session?.user) {
  return redirect('/login');
}
```

### Rate Limiting

The chat API enforces rate limits based on user type:

```typescript
// Defined in lib/ai/entitlements.ts
const entitlementsByUserType = {
  regular: { maxMessagesPerDay: 100 },
  premium: { maxMessagesPerDay: 1000 }
};
```

### Streaming

Use Server-Sent Events for streaming responses:

```typescript
const response = await fetch('/api/chat', { method: 'POST', body: data });
const reader = response.body?.getReader();
// Process stream chunks
```

### Pagination

Use cursor-based pagination for history:

```typescript
const { chats, hasMore } = await getChatsByUserId({
  id: userId,
  limit: 10,
  startingAfter: lastChatId,
  endingBefore: null
});
```

---

## Examples

### Complete Chat Flow

```typescript
import { useChat } from '@ai-sdk/react';
import { Chat } from '@/components/chat';

function ChatPage({ chatId, session }) {
  return (
    <Chat
      id={chatId}
      initialMessages={[]}
      initialChatModel="chat-model"
      initialVisibilityType="private"
      isReadonly={false}
      session={session}
      autoResume={true}
    />
  );
}
```

### Creating and Editing Documents

```typescript
// User: "Create a document about React hooks"
// AI automatically calls createDocument tool:
{
  title: "React Hooks Guide",
  kind: "text"
}

// Document is created and displayed
// User: "Add a section about useEffect"
// AI calls updateDocument tool:
{
  id: "doc-123",
  description: "Add a detailed section about useEffect hook"
}
```

### File Upload Flow

```typescript
const handleFileUpload = async (file: File) => {
  const formData = new FormData();
  formData.append('file', file);
  
  const response = await fetch('/api/files/upload', {
    method: 'POST',
    body: formData
  });
  
  if (response.ok) {
    const { url, pathname, contentType } = await response.json();
    setAttachments(prev => [...prev, { url, name: pathname, contentType }]);
  }
};
```

---

## Testing

### Running Tests

```bash
# Unit tests
npm test

# E2E tests
npm run test:e2e

# Specific test file
npm test -- path/to/test.ts
```

### API Testing

Use the provided test helpers:

```typescript
import { testApiRoute } from '@/tests/helpers';

test('POST /api/chat creates a new chat', async () => {
  const response = await testApiRoute('/api/chat', {
    method: 'POST',
    body: { /* ... */ }
  });
  
  expect(response.status).toBe(200);
});
```

---

## Support

For issues or questions:
- Check the [README](./README.md)
- Review error codes in `lib/errors.ts`
- Examine test files in `tests/` directory

---

**Last Updated:** 2025-10-30
