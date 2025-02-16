# Agents Reasoning Interface (ARI)

ARI is a lightweight specification for agent-to-agent communication that focuses on exchanging reasoning and thought processes rather than structured data. Unlike traditional APIs that exchange JSON payloads, ARI is designed for the age of Large Language Models where semantic understanding and reasoning weights are more important than data structure.

## Core Concepts

- **Reasoning-First Communication**: Agents exchange thoughts and reasoning processes instead of structured data
- **Multiple LLM Support**: Built-in support for OpenAI, Anthropic, and Gemini
- **Unified Error Handling**: Consistent error handling across different LLM providers
- **Type Safety**: Full TypeScript support with proper type checking

## Installation

```bash
# Clone the repository
git clone https://github.com/wansatya/ari.git

# Navigate to the project directory
cd ari

# Install dependencies
npm install

# Build the project
npm run build

# Run specific examples
npm run example:research
npm run example:simple
npm run example:multi

# Run all examples
npm run example:all

# Watch mode for development
npm run watch

# Run tests
npm test
```

## Project Structure

```
ari/
├── src/
│   ├── types/
│   │   └── index.ts          # Core type definitions
│   ├── llm/
│   │   ├── types.ts          # LLM-specific types
│   │   └── providers/        # LLM providers
│   │       └── index.ts      # Provider implementations
│   ├── core/
│   │   ├── ARIAgent.ts       # Base agent interface
│   │   └── ARIMessage.ts     # Message handling
│   └── index.ts              # Main exports
├── examples/
│   ├── simple.ts
│   ├── research.ts
│   └── multi-agent.ts
└── README.md
```

## Quick Start

```typescript
import { AnthropicProvider } from './llm/providers';
import { LLMMessage } from './llm/types';

// Initialize provider
const provider = new AnthropicProvider(process.env.ANTHROPIC_API_KEY);

// Create messages
const messages: LLMMessage[] = [
    {
        role: 'user',
        content: 'Analyze the impact of rising temperatures on urban planning'
    }
];

try {
    const response = await provider.generateResponse(messages);
    console.log(response.content);
} catch (error) {
    if (error instanceof AnthropicError) {
        console.error(`Error (${error.code}): ${error.message}`);
    }
}
```

## Provider-Specific Error Handling

Each provider implements its own error handling with consistent error codes:

```typescript
// Anthropic error handling
private isAnthropicAPIError(error: unknown): error is Anthropic.APIError {
    return (
        error instanceof Error &&
        'status' in error &&
        typeof (error as any).status === 'string'
    );
}

private mapAnthropicErrorCode(status: string): string {
    const errorMap: Record<string, string> = {
        'authentication_error': LLMErrorCodes.INVALID_API_KEY,
        'rate_limit_error': LLMErrorCodes.RATE_LIMIT_EXCEEDED,
        'context_length_exceeded': LLMErrorCodes.CONTEXT_LENGTH_EXCEEDED,
        'invalid_request_error': LLMErrorCodes.INVALID_REQUEST
    };
    return errorMap[status] || LLMErrorCodes.PROVIDER_ERROR;
}
```

## Common Error Codes

```typescript
export const LLMErrorCodes = {
    INVALID_API_KEY: 'INVALID_API_KEY',
    RATE_LIMIT_EXCEEDED: 'RATE_LIMIT_EXCEEDED',
    CONTEXT_LENGTH_EXCEEDED: 'CONTEXT_LENGTH_EXCEEDED',
    INVALID_REQUEST: 'INVALID_REQUEST',
    PROVIDER_ERROR: 'PROVIDER_ERROR',
    UNKNOWN_ERROR: 'UNKNOWN_ERROR'
} as const;
```

## Message Format

```typescript
interface LLMMessage {
    role: 'system' | 'user' | 'assistant';
    content: string;
}

interface LLMResponse {
    content: string;
    modelUsed: string;
    finishReason?: string;
    raw?: unknown;
}
```

## Provider Support

Currently supported LLM providers:
- OpenAI (GPT-4, GPT-3.5)
- Anthropic (Claude)
- Google (Gemini)

Each provider implements the ILLMProvider interface:

```typescript
interface ILLMProvider {
    generateResponse(messages: LLMMessage[]): Promise<LLMResponse>;
}
```

## Error Handling

```typescript
try {
    const response = await provider.generateResponse(messages);
    // Handle successful response
} catch (error) {
    if (error instanceof OpenAIError) {
        // Handle OpenAI specific errors
    } else if (error instanceof AnthropicError) {
        // Handle Anthropic specific errors
    } else if (error instanceof GeminiError) {
        // Handle Gemini specific errors
    } else {
        // Handle unknown errors
    }
}
```

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

MIT License - see the [LICENSE](LICENSE) file for details

## Roadmap

- [ ] Add streaming support
- [ ] Add token counting
- [ ] Add cost estimation
- [ ] Add more provider-specific features
- [ ] Add better error recovery strategies
