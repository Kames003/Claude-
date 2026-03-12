# Streaming a Real-time Updates

Implementácia streaming AI odpovedi a real-time UI.

---

## Streaming v UIGen

UIGen používa Vercel AI SDK pre streaming:

```typescript
// src/app/api/chat/route.ts

import { streamText } from 'ai'
import { anthropic } from '@ai-sdk/anthropic'

export async function POST(req: Request) {
  const { messages, projectId } = await req.json()

  const result = await streamText({
    model: anthropic('claude-sonnet-4-20250514'),
    messages,
    tools: { /* ... */ },

    // Callback po dokončení
    onFinish: async ({ response }) => {
      await saveToDatabase(projectId, response)
    }
  })

  // Vráť streaming response
  return result.toDataStreamResponse()
}
```

---

## Client-side Handling

### useChat Hook

```typescript
// src/lib/contexts/chat-context.tsx

import { useChat } from '@ai-sdk/react'

export function ChatProvider({ children, projectId }) {
  const {
    messages,
    input,
    handleInputChange,
    handleSubmit,
    isLoading,
    error
  } = useChat({
    api: '/api/chat',
    body: { projectId },

    // Real-time updates
    onResponse: (response) => {
      console.log('Stream started:', response.status)
    },

    onFinish: (message) => {
      console.log('Stream finished:', message)
    },

    onError: (error) => {
      console.error('Stream error:', error)
    }
  })

  return (
    <ChatContext.Provider value={{
      messages,
      input,
      handleInputChange,
      handleSubmit,
      isLoading
    }}>
      {children}
    </ChatContext.Provider>
  )
}
```

---

## Message Parts

Claude responses obsahujú rôzne časti:

```typescript
// src/components/chat/MessageList.tsx

function MessageContent({ message }) {
  return (
    <>
      {message.parts.map((part, index) => {
        switch (part.type) {
          case 'text':
            return <TextPart key={index} content={part.text} />

          case 'reasoning':
            return <ReasoningPart key={index} content={part.reasoning} />

          case 'tool-invocation':
            return (
              <ToolInvocationPart
                key={index}
                toolName={part.toolInvocation.toolName}
                args={part.toolInvocation.args}
                result={part.toolInvocation.result}
              />
            )

          case 'step-start':
            return <StepIndicator key={index} />

          default:
            return null
        }
      })}
    </>
  )
}
```

---

## Tool Invocation States

```typescript
type ToolInvocationState =
  | 'pending'    // Tool je volaný
  | 'streaming'  // Výsledok sa streamuje
  | 'complete'   // Dokončené
  | 'error'      // Chyba

function ToolInvocationPart({ toolInvocation }) {
  const { state, toolName, args, result } = toolInvocation

  if (state === 'pending') {
    return (
      <div className="flex items-center gap-2">
        <Spinner />
        <span>Executing {toolName}...</span>
      </div>
    )
  }

  if (state === 'error') {
    return (
      <div className="text-red-500">
        Error in {toolName}: {result.error}
      </div>
    )
  }

  return (
    <div className="bg-gray-100 p-2 rounded">
      <div className="font-mono text-sm">{toolName}</div>
      <pre className="text-xs">{JSON.stringify(result, null, 2)}</pre>
    </div>
  )
}
```

---

## Progress Indicators

### Typing Indicator

```typescript
function TypingIndicator() {
  return (
    <div className="flex items-center gap-1">
      <span className="animate-bounce">.</span>
      <span className="animate-bounce delay-100">.</span>
      <span className="animate-bounce delay-200">.</span>
    </div>
  )
}

// V MessageList
{isLoading && messages[messages.length - 1]?.role === 'user' && (
  <TypingIndicator />
)}
```

### Stream Progress

```typescript
function StreamProgress({ message }) {
  const charCount = message.content?.length || 0

  return (
    <div className="text-xs text-gray-400">
      {charCount} characters generated
    </div>
  )
}
```

---

## Cancellation

```typescript
function ChatInput() {
  const { handleSubmit, stop, isLoading } = useChat()

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" /* ... */ />

      {isLoading ? (
        <button type="button" onClick={stop}>
          Stop generating
        </button>
      ) : (
        <button type="submit">Send</button>
      )}
    </form>
  )
}
```

---

## Error Recovery

```typescript
function ChatInterface() {
  const { error, reload, messages } = useChat()

  if (error) {
    return (
      <div className="p-4 bg-red-50 border border-red-200 rounded">
        <p className="text-red-700">
          Error: {error.message}
        </p>
        <button
          onClick={() => reload()}
          className="mt-2 px-4 py-2 bg-red-500 text-white rounded"
        >
          Retry last message
        </button>
      </div>
    )
  }

  return <MessageList messages={messages} />
}
```

---

## Optimistic Updates

```typescript
function useOptimisticChat() {
  const { messages, setMessages, handleSubmit } = useChat()

  const submitWithOptimistic = async (input: string) => {
    // Okamžite pridaj user message
    const userMessage = {
      id: crypto.randomUUID(),
      role: 'user',
      content: input
    }

    setMessages([...messages, userMessage])

    // Potom odošli na server
    await handleSubmit()
  }

  return { messages, submitWithOptimistic }
}
```

---

## Cvičenia

### Cvičenie 1: Progress Bar
```
Pridaj progress bar ktorý ukazuje približný progress generovania.
```

### Cvičenie 2: Reconnection
```
Implementuj automatické reconnection pri strate spojenia.
```

### Cvičenie 3: Message Buffering
```
Pridaj buffering pre pomalé spojenia.
```

---

## Ďalej

[Code Transformations](15-transformations.md)
