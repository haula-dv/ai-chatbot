# Component Documentation

This document provides detailed documentation for all React components in the application.

## Table of Contents

1. [Layout Components](#layout-components)
2. [Chat Components](#chat-components)
3. [Artifact Components](#artifact-components)
4. [Form Components](#form-components)
5. [UI Components](#ui-components)
6. [Message Components](#message-components)

---

## Layout Components

### `<AppSidebar>`

Main application sidebar with navigation and chat history.

**Location:** `components/app-sidebar.tsx`

**Features:**
- User navigation
- Chat history list
- Collapsible sidebar
- Responsive design

**Usage:**
```tsx
import { AppSidebar } from '@/components/app-sidebar';

<AppSidebar />
```

### `<ChatHeader>`

Header for chat interface with title and controls.

**Location:** `components/chat-header.tsx`

**Props:**
```typescript
{
  chatId: string;
  selectedVisibilityType: VisibilityType;
  isReadonly: boolean;
  session: Session;
}
```

**Features:**
- Chat title display
- Visibility toggle
- Share button
- Model selector

---

## Chat Components

### `<Chat>`

Main chat interface component that orchestrates all chat functionality.

**Location:** `components/chat.tsx`

**Props:**
```typescript
{
  id: string;
  initialMessages: ChatMessage[];
  initialChatModel: string;
  initialVisibilityType: VisibilityType;
  isReadonly: boolean;
  session: Session;
  autoResume: boolean;
  initialLastContext?: LanguageModelUsage;
}
```

**State Management:**
- Uses `useChat` hook from @ai-sdk/react
- Manages messages, input, attachments
- Handles streaming responses
- Tracks token usage

**Features:**
- Message streaming
- File attachments
- Model selection
- Auto-resume
- Optimistic updates

**Example:**
```tsx
<Chat
  id="chat-123"
  initialMessages={messages}
  initialChatModel="chat-model"
  initialVisibilityType="private"
  isReadonly={false}
  session={session}
  autoResume={true}
/>
```

### `<Messages>`

Displays a list of chat messages with voting and regeneration.

**Location:** `components/messages.tsx`

**Props:**
```typescript
{
  chatId: string;
  status: 'ready' | 'submitted' | 'streaming';
  votes: Array<Vote> | undefined;
  messages: ChatMessage[];
  setMessages: (messages: ChatMessage[]) => void;
  regenerate: (messageId: string) => void;
  isReadonly: boolean;
  isArtifactVisible: boolean;
  selectedModelId: string;
}
```

**Features:**
- Virtualized scrolling for performance
- Auto-scroll to bottom
- Grouped by date
- Loading states
- Empty state with greeting

**Structure:**
```
<Messages>
  <Greeting /> (if no messages)
  <Message /> (for each message)
  <div ref={endRef} /> (scroll anchor)
</Messages>
```

### `<Message>`

Individual message component with actions and rendering.

**Location:** `components/message.tsx`

**Props:**
```typescript
{
  chatId: string;
  message: ChatMessage;
  vote: Vote | undefined;
  isLoading: boolean;
  setMessages: (messages: ChatMessage[]) => void;
  regenerate: (messageId: string) => void;
  isReadonly: boolean;
}
```

**Features:**
- Markdown rendering
- Code syntax highlighting
- Image attachments
- Vote buttons (thumbs up/down)
- Copy button
- Edit functionality
- Regenerate option

**Message Types:**
- User messages (blue background)
- Assistant messages (default background)
- System messages (muted)
- Tool call messages (gray background)

### `<MessageActions>`

Action buttons for messages (copy, edit, regenerate).

**Location:** `components/message-actions.tsx`

**Props:**
```typescript
{
  message: ChatMessage;
  vote: Vote | undefined;
  isLoading: boolean;
  chatId: string;
  setMessages: (messages: ChatMessage[]) => void;
  regenerate: (messageId: string) => void;
}
```

**Actions:**
- Copy to clipboard
- Edit message
- Regenerate response
- Vote up/down

### `<MultimodalInput>`

Rich input component supporting text and files.

**Location:** `components/multimodal-input.tsx`

**Props:**
```typescript
{
  chatId: string;
  input: string;
  setInput: (input: string) => void;
  status: 'ready' | 'submitted' | 'streaming';
  stop: () => void;
  attachments: Attachment[];
  setAttachments: (attachments: Attachment[]) => void;
  messages: UIMessage[];
  setMessages: (messages: UIMessage[]) => void;
  sendMessage: (message: ChatMessage) => void;
  selectedVisibilityType: VisibilityType;
  selectedModelId: string;
  usage?: LanguageModelUsage;
}
```

**Features:**
- Auto-resizing textarea
- File upload (drag & drop)
- Attachment previews
- Model selector
- Token usage display
- Submit on Enter (Shift+Enter for new line)
- Scroll to bottom button

**Keyboard Shortcuts:**
- `Enter` - Submit message
- `Shift+Enter` - New line
- `Cmd/Ctrl+K` - Focus input

**Layout:**
```
<PromptInput>
  <AttachmentsPreview />
  <Textarea />
  <Context usage={usage} />
  <Toolbar>
    <AttachmentsButton />
    <ModelSelector />
    <SubmitButton />
  </Toolbar>
</PromptInput>
```

### `<SuggestedActions>`

Displays suggested prompts for new chats.

**Location:** `components/suggested-actions.tsx`

**Props:**
```typescript
{
  sendMessage: (message: ChatMessage) => void;
  chatId: string;
  selectedVisibilityType: VisibilityType;
}
```

**Features:**
- Pre-defined prompt suggestions
- One-click message sending
- Only shown for new chats

**Example Suggestions:**
- "Help me debug my code"
- "Write a creative story"
- "Explain a concept"
- "Generate an image"

---

## Artifact Components

### `<Artifact>`

Container for displaying and editing artifacts (documents, code, images, sheets).

**Location:** `components/artifact.tsx`

**Props:**
```typescript
{
  chatId: string;
  input: string;
  setInput: (input: string) => void;
  status: UseChatHelpers['status'];
  stop: () => void;
  attachments: Attachment[];
  setAttachments: (attachments: Attachment[]) => void;
  messages: ChatMessage[];
  setMessages: (messages: ChatMessage[]) => void;
  votes: Array<Vote> | undefined;
  sendMessage: (message: ChatMessage) => void;
  regenerate: () => void;
  isReadonly: boolean;
  selectedVisibilityType: VisibilityType;
  selectedModelId: string;
}
```

**Supported Types:**
- **Text**: Rich text editor with markdown support
- **Code**: Code editor with syntax highlighting
- **Image**: Image generation and display
- **Sheet**: Spreadsheet editor

**Features:**
- Version history
- Real-time streaming updates
- Save to database
- Undo/redo
- Full-screen mode
- Close button

**Layout:**
```
<Artifact>
  <ArtifactHeader>
    <Title />
    <ArtifactActions />
    <ArtifactCloseButton />
  </ArtifactHeader>
  <ArtifactContent>
    {/* Type-specific editor */}
  </ArtifactContent>
  <VersionFooter />
  <ArtifactMessages />
</Artifact>
```

### `<CodeEditor>`

Monaco-based code editor for code artifacts.

**Location:** `components/code-editor.tsx`

**Props:**
```typescript
{
  code: string;
  language: string;
  onCodeChange: (code: string) => void;
  readOnly?: boolean;
}
```

**Features:**
- Syntax highlighting (100+ languages)
- Auto-completion
- Multi-cursor editing
- Find and replace
- Minimap
- Line numbers
- Bracket matching

**Supported Languages:**
- JavaScript/TypeScript
- Python
- Java
- C/C++
- Go
- Rust
- And many more...

### `<TextEditor>`

Rich text editor for text artifacts.

**Location:** `components/text-editor.tsx`

**Props:**
```typescript
{
  content: string;
  onContentChange: (content: string) => void;
  suggestions?: Suggestion[];
  readOnly?: boolean;
}
```

**Features:**
- Markdown support
- Rich formatting toolbar
- Inline suggestions
- Real-time collaboration
- Word count

**Toolbar Actions:**
- Bold, italic, underline
- Headers (H1-H6)
- Lists (ordered/unordered)
- Links
- Code blocks
- Quotes

### `<ImageEditor>`

Image display and editing component.

**Location:** `components/image-editor.tsx`

**Props:**
```typescript
{
  imageUrl: string;
  onImageChange?: (url: string) => void;
  readOnly?: boolean;
}
```

**Features:**
- Image preview
- Zoom controls
- Download button
- Regeneration options

### `<SheetEditor>`

Spreadsheet editor for sheet artifacts.

**Location:** `components/sheet-editor.tsx`

**Props:**
```typescript
{
  data: any[][];
  onDataChange: (data: any[][]) => void;
  readOnly?: boolean;
}
```

**Features:**
- Cell editing
- Row/column operations
- Formulas
- Copy/paste
- Sorting
- Export to CSV

### `<ArtifactActions>`

Action buttons for artifacts.

**Location:** `components/artifact-actions.tsx`

**Features:**
- Save
- Download
- Share
- Delete
- Version history

### `<VersionFooter>`

Displays and navigates artifact versions.

**Location:** `components/version-footer.tsx`

**Props:**
```typescript
{
  documents: Document[];
  currentIndex: number;
  onVersionChange: (index: number) => void;
}
```

**Features:**
- Timeline view
- Version comparison
- Restore previous versions

---

## Form Components

### `<AuthForm>`

Authentication form wrapper with server actions.

**Location:** `components/auth-form.tsx`

**Props:**
```typescript
{
  action: 'login' | 'register';
  children: ReactNode;
  defaultEmail?: string;
}
```

**Features:**
- Email validation
- Password requirements
- Loading states
- Error messages
- Form persistence

**Usage:**
```tsx
<AuthForm action="login">
  <Input name="email" type="email" required />
  <Input name="password" type="password" required />
  <SubmitButton>Sign In</SubmitButton>
</AuthForm>
```

### `<SubmitButton>`

Submit button with loading state.

**Location:** `components/submit-button.tsx`

**Props:**
```typescript
{
  children: ReactNode;
  disabled?: boolean;
  isLoading?: boolean;
}
```

**States:**
- Default: "Submit"
- Loading: Spinner + "Submitting..."
- Disabled: Grayed out

---

## UI Components

All base UI components are in `components/ui/` and built with Radix UI.

### `<Button>`

Customizable button component.

**Location:** `components/ui/button.tsx`

**Variants:**
- `default`: Primary button
- `destructive`: Red for dangerous actions
- `outline`: Border only
- `secondary`: Muted colors
- `ghost`: Transparent background
- `link`: Text link style

**Sizes:**
- `default`: Standard size
- `sm`: Small
- `lg`: Large
- `icon`: Square icon button

**Example:**
```tsx
<Button variant="outline" size="sm">
  Click Me
</Button>
```

### `<Input>`

Text input with validation.

**Location:** `components/ui/input.tsx`

**Types:**
- text
- email
- password
- number
- search

**Features:**
- Built-in validation
- Error states
- Disabled states
- Placeholder support

### `<Textarea>`

Multi-line text input.

**Location:** `components/ui/textarea.tsx`

**Features:**
- Auto-resize
- Character count
- Max length
- Resize handles

### `<Select>`

Dropdown select component.

**Location:** `components/ui/select.tsx`

**Example:**
```tsx
<Select value={value} onValueChange={setValue}>
  <SelectTrigger>
    <SelectValue placeholder="Select..." />
  </SelectTrigger>
  <SelectContent>
    <SelectItem value="option1">Option 1</SelectItem>
    <SelectItem value="option2">Option 2</SelectItem>
  </SelectContent>
</Select>
```

### `<Dialog>`

Modal dialog component.

**Location:** `components/ui/alert-dialog.tsx`

**Example:**
```tsx
<AlertDialog>
  <AlertDialogTrigger>Delete</AlertDialogTrigger>
  <AlertDialogContent>
    <AlertDialogHeader>
      <AlertDialogTitle>Are you sure?</AlertDialogTitle>
      <AlertDialogDescription>
        This action cannot be undone.
      </AlertDialogDescription>
    </AlertDialogHeader>
    <AlertDialogFooter>
      <AlertDialogCancel>Cancel</AlertDialogCancel>
      <AlertDialogAction>Delete</AlertDialogAction>
    </AlertDialogFooter>
  </AlertDialogContent>
</AlertDialog>
```

### `<Tooltip>`

Hover tooltip component.

**Location:** `components/ui/tooltip.tsx`

**Example:**
```tsx
<Tooltip>
  <TooltipTrigger>Hover me</TooltipTrigger>
  <TooltipContent>
    <p>Tooltip content</p>
  </TooltipContent>
</Tooltip>
```

### `<ScrollArea>`

Custom scrollable area with styled scrollbars.

**Location:** `components/ui/scroll-area.tsx`

**Example:**
```tsx
<ScrollArea className="h-[400px]">
  {/* Long content */}
</ScrollArea>
```

### `<Separator>`

Horizontal or vertical divider.

**Location:** `components/ui/separator.tsx`

**Example:**
```tsx
<Separator orientation="horizontal" />
<Separator orientation="vertical" className="h-8" />
```

### `<Badge>`

Small label component.

**Location:** `components/ui/badge.tsx`

**Variants:**
- default
- secondary
- destructive
- outline

**Example:**
```tsx
<Badge variant="secondary">New</Badge>
```

### `<Card>`

Container with border and shadow.

**Location:** `components/ui/card.tsx`

**Example:**
```tsx
<Card>
  <CardHeader>
    <CardTitle>Title</CardTitle>
    <CardDescription>Description</CardDescription>
  </CardHeader>
  <CardContent>
    {/* Content */}
  </CardContent>
  <CardFooter>
    {/* Footer */}
  </CardFooter>
</Card>
```

---

## Message Components

### `<CodeBlock>`

Syntax-highlighted code display in messages.

**Location:** `components/elements/code-block.tsx`

**Props:**
```typescript
{
  code: string;
  language: string;
  showLineNumbers?: boolean;
}
```

**Features:**
- Syntax highlighting with Prism
- Copy button
- Language badge
- Line numbers (optional)

### `<Image>`

Image display in messages.

**Location:** `components/elements/image.tsx`

**Props:**
```typescript
{
  url: string;
  alt: string;
}
```

**Features:**
- Lazy loading
- Click to enlarge
- Loading placeholder

### `<Tool>`

Displays tool call results in messages.

**Location:** `components/elements/tool.tsx`

**Props:**
```typescript
{
  toolName: string;
  toolInput: any;
  toolOutput: any;
}
```

**Supported Tools:**
- Weather display
- Document creation
- Document updates
- Suggestions

### `<Reasoning>`

Displays AI reasoning steps (for reasoning model).

**Location:** `components/elements/reasoning.tsx`

**Props:**
```typescript
{
  reasoning: string[];
}
```

**Features:**
- Collapsible steps
- Step numbers
- Time indicators

### `<Loader>`

Loading animation for streaming messages.

**Location:** `components/elements/loader.tsx`

**Variants:**
- Dots
- Spinner
- Pulse

### `<Suggestion>`

Inline editing suggestion display.

**Location:** `components/elements/suggestion.tsx`

**Props:**
```typescript
{
  suggestion: Suggestion;
  onAccept: () => void;
  onReject: () => void;
}
```

**Features:**
- Diff view (before/after)
- Accept/reject buttons
- Description

---

## Sidebar Components

### `<SidebarHistory>`

Chat history list in sidebar.

**Location:** `components/sidebar-history.tsx`

**Features:**
- Infinite scroll
- Search/filter
- Delete chats
- Grouped by date

### `<SidebarHistoryItem>`

Individual chat item in history.

**Location:** `components/sidebar-history-item.tsx`

**Props:**
```typescript
{
  chat: Chat;
  isActive: boolean;
}
```

**Features:**
- Title truncation
- Last message preview
- Timestamp
- Delete button on hover

### `<SidebarUserNav>`

User navigation and settings.

**Location:** `components/sidebar-user-nav.tsx`

**Features:**
- User avatar
- Settings menu
- Sign out button
- Profile link

### `<SidebarToggle>`

Button to collapse/expand sidebar.

**Location:** `components/sidebar-toggle.tsx`

**Features:**
- Animated icon
- Keyboard shortcut (Cmd/Ctrl + \\)
- Persisted state

---

## Specialized Components

### `<ModelSelector>`

Dropdown for selecting AI models.

**Location:** `components/model-selector.tsx`

**Features:**
- Model descriptions
- Capability indicators
- Optimistic updates
- Cookie persistence

### `<VisibilitySelector>`

Toggle for public/private chats.

**Location:** `components/visibility-selector.tsx`

**Features:**
- Icon toggle
- Tooltip descriptions
- Server sync
- Optimistic updates

### `<DataStreamHandler>`

Processes streaming data from AI.

**Location:** `components/data-stream-handler.tsx`

**Handles:**
- Text deltas
- Code deltas
- Image deltas
- Sheet deltas
- Suggestions
- Metadata

### `<DataStreamProvider>`

Context provider for data stream.

**Location:** `components/data-stream-provider.tsx`

**Usage:**
```tsx
<DataStreamProvider>
  <Chat />
</DataStreamProvider>
```

### `<ThemeProvider>`

Dark/light theme management.

**Location:** `components/theme-provider.tsx`

**Features:**
- System preference detection
- Theme persistence
- CSS variable switching

### `<Weather>`

Weather display component.

**Location:** `components/weather.tsx`

**Props:**
```typescript
{
  data: WeatherData;
}
```

**Displays:**
- Current temperature
- Conditions
- Sunrise/sunset
- Hourly forecast

---

## Component Patterns

### Memoization

Components use `memo` for performance:

```tsx
export const Message = memo(PureMessage, (prev, next) => {
  return prev.message.id === next.message.id &&
         prev.isLoading === next.isLoading;
});
```

### Server Components

Components that fetch data use Server Components:

```tsx
// app/chat/[id]/page.tsx
export default async function ChatPage({ params }) {
  const messages = await getMessagesByChatId({ id: params.id });
  return <Chat initialMessages={messages} />;
}
```

### Client Components

Interactive components use `'use client'`:

```tsx
'use client';

export function InteractiveButton() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

### Compound Components

Complex components expose sub-components:

```tsx
<Card>
  <CardHeader>
    <CardTitle>Title</CardTitle>
  </CardHeader>
  <CardContent>Content</CardContent>
</Card>
```

### Render Props

Flexibility through render props:

```tsx
<DataProvider>
  {({ data, loading }) => (
    loading ? <Spinner /> : <DataView data={data} />
  )}
</DataProvider>
```

---

## Styling

### Tailwind CSS

All components use Tailwind utility classes:

```tsx
<div className="flex items-center gap-2 p-4 rounded-lg bg-background">
```

### CSS Variables

Theme colors use CSS variables:

```css
:root {
  --background: 0 0% 100%;
  --foreground: 222.2 47.4% 11.2%;
  --primary: 221.2 83.2% 53.3%;
}
```

### Responsive Design

Mobile-first breakpoints:

```tsx
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3">
```

### Dark Mode

Automatic dark mode support:

```tsx
<div className="bg-white dark:bg-gray-900">
```

---

## Accessibility

All components follow WCAG 2.1 AA standards:

- Semantic HTML
- ARIA labels
- Keyboard navigation
- Focus indicators
- Screen reader support

**Example:**
```tsx
<button
  aria-label="Close dialog"
  aria-pressed={isOpen}
  role="button"
  tabIndex={0}
>
  <CloseIcon aria-hidden="true" />
</button>
```

---

## Testing Components

### Unit Tests

```typescript
import { render, screen } from '@testing-library/react';
import { Message } from '@/components/message';

test('renders message content', () => {
  render(<Message message={mockMessage} />);
  expect(screen.getByText('Hello')).toBeInTheDocument();
});
```

### Integration Tests

```typescript
import { render, fireEvent } from '@testing-library/react';
import { Chat } from '@/components/chat';

test('sends message on submit', async () => {
  const { getByTestId } = render(<Chat {...props} />);
  
  const input = getByTestId('multimodal-input');
  fireEvent.change(input, { target: { value: 'Hello' } });
  
  const submit = getByTestId('submit-button');
  fireEvent.click(submit);
  
  // Assert message was sent
});
```

---

## Performance

### Code Splitting

Components are lazy loaded:

```tsx
const HeavyComponent = lazy(() => import('./HeavyComponent'));

<Suspense fallback={<Spinner />}>
  <HeavyComponent />
</Suspense>
```

### Virtualization

Long lists use virtualization:

```tsx
<VirtualScroller items={messages} itemHeight={80}>
  {(message) => <Message message={message} />}
</VirtualScroller>
```

### Image Optimization

Next.js Image component:

```tsx
<Image
  src="/image.jpg"
  alt="Description"
  width={800}
  height={600}
  loading="lazy"
/>
```

---

**Last Updated:** 2025-10-30
