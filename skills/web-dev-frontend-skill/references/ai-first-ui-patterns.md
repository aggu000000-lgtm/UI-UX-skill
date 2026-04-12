# AI-First UI Patterns Reference

> **2026 STANDARDS**: AI-powered interfaces have evolved beyond chatbots. This guide covers patterns for designing AI-native interfaces that feel natural, trustworthy, and performant.

---

## STREAMING TEXT / TYPEWRITER EFFECTS

### CSS Implementation

```css
@keyframes typewriter {
  from { opacity: 0; }
  to { opacity: 1; }
}

.streaming-text span {
  opacity: 0;
  animation: typewriter 0.05s forwards;
}

.streaming-text span.visible {
  opacity: 1;
}
```

### JavaScript Implementation

```javascript
function streamText(container, text, speed = 20) {
  let index = 0;
  container.textContent = '';
  
  function type() {
    if (index < text.length) {
      container.textContent += text[index];
      index++;
      setTimeout(type, speed);
    }
  }
  
  type();
}

// Character-by-character for LLMs
async function streamAIResponse(container, fetchPromise) {
  const reader = (await fetchPromise).body.getReader();
  const decoder = new TextDecoder();
  
  while (true) {
    const { done, value } = await reader.read();
    if (done) break;
    
    const chunk = decoder.decode(value);
    container.textContent += chunk;
  }
}
```

---

## CONFIDENCE INDICATORS

### Visual Confidence Display

```css
.confidence-bar {
  height: 4px;
  background: #e5e7eb;
  border-radius: 2px;
  overflow: hidden;
}

.confidence-fill {
  height: 100%;
  transition: width 300ms ease-out;
}

.confidence-fill.high { background: #10b981; }  /* 80%+ */
.confidence-fill.medium { background: #f59e0b; } /* 50-80% */
.confidence-fill.low { background: #ef4444; }   /* <50% */
```

### Component

```jsx
function AIResponse({ content, confidence }) {
  const confidenceClass = 
    confidence >= 80 ? 'high' : 
    confidence >= 50 ? 'medium' : 'low';
  
  return (
    <div className="ai-response">
      <div className="response-content">
        {content}
      </div>
      {confidence !== undefined && (
        <div className="confidence-indicator">
          <span>Confidence: {confidence}%</span>
          <div className="confidence-bar">
            <div 
              className={`confidence-fill ${confidenceClass}`}
              style={{ width: `${confidence}%` }}
            />
          </div>
        </div>
      )}
    </div>
  );
}
```

---

## AMBIENT INTELLIGENCE

### Adaptive Layout

```javascript
// Track user behavior patterns
const userPreferences = {
  scrollSpeed: 0,
  hoverDuration: 0,
  clickFrequency: {},
  viewportFocus: {}
};

// Adapt interface based on patterns
function adaptLayout(userData) {
  const layout = document.querySelector('.adaptive-layout');
  
  if (userData.prefersDense) {
    layout.classList.add('compact');
    layout.classList.remove('spacious');
  } else {
    layout.classList.add('spacious');
    layout.classList.remove('compact');
  }
}

// Reorder navigation based on usage
function reorderNavigation(usageData) {
  const nav = document.querySelector('.main-nav');
  const items = [...nav.children];
  
  items.sort((a, b) => {
    return (usageData[b.dataset.action] || 0) - 
           (usageData[a.dataset.action] || 0);
  });
  
  items.forEach(item => nav.appendChild(item));
}
```

### Context-Aware Suggestions

```css
.ai-suggestion {
  position: absolute;
  opacity: 0;
  transform: translateY(8px);
  transition: all 200ms ease-out;
  pointer-events: none;
}

.ai-suggestion.visible {
  opacity: 1;
  transform: translateY(0);
  pointer-events: auto;
}
```

---

## VOICE-FIRST INTERFACES

### Voice Input Component

```jsx
function VoiceInput({ onTranscript }) {
  const [isListening, setIsListening] = useState(false);
  const recognition = useSpeechRecognition();
  
  const startListening = () => {
    setIsListening(true);
    recognition.start();
  };
  
  recognition.onResult = (event) => {
    const transcript = event.results[0][0].transcript;
    onTranscript(transcript);
  };
  
  return (
    <button
      onClick={startListening}
      className={isListening ? 'recording' : ''}
      aria-label="Voice input"
    >
      <MicIcon />
      {isListening && <WaveformAnimation />}
    </button>
  );
}
```

### CSS Voice Indicator

```css
@keyframes waveform {
  0%, 100% { transform: scaleY(0.5); }
  50% { transform: scaleY(1); }
}

.voice-button.recording {
  background: var(--accent-color);
}

.voice-button.recording svg {
  animation: waveform 0.5s ease-in-out infinite;
}

.waveform-bar {
  display: flex;
  gap: 2px;
  height: 20px;
  align-items: center;
}

.waveform-bar span {
  width: 3px;
  background: currentColor;
  animation: waveform 0.4s ease-in-out infinite;
}

.waveform-bar span:nth-child(2) { animation-delay: 0.1s; }
.waveform-bar span:nth-child(3) { animation-delay: 0.2s; }
```

---

## AI STATE PATTERNS

### Thinking/Processing Indicator

```css
@keyframes pulse {
  0%, 100% { opacity: 0.4; }
  50% { opacity: 1; }
}

.ai-thinking {
  display: flex;
  gap: 6px;
  padding: 16px;
}

.ai-thinking span {
  width: 8px;
  height: 8px;
  background: var(--text-muted);
  border-radius: 50%;
  animation: pulse 1.4s ease-in-out infinite;
}

.ai-thinking span:nth-child(2) { animation-delay: 0.2s; }
.ai-thinking span:nth-child(3) { animation-delay: 0.4s; }
```

### Contextual AI Suggestions

```jsx
function AIChat({ context }) {
  const suggestions = getSuggestions(context);
  
  return (
    <div className="ai-context-suggestions">
      {suggestions.map((suggestion, i) => (
        <button
          key={i}
          className="suggestion-chip"
          onClick={() => submitQuery(suggestion.query)}
        >
          {suggestion.icon && <Icon>{suggestion.icon}</Icon>}
          {suggestion.text}
        </button>
      ))}
    </div>
  );
}
```

---

## REGENERATION & EDITING

### Regenerate Response

```jsx
function AIRegenerate({ onRegenerate, isLoading }) {
  return (
    <div className="ai-actions">
      <button 
        className="regenerate-btn"
        onClick={onRegenerate}
        disabled={isLoading}
      >
        <RefreshIcon />
        Regenerate
      </button>
      
      <button className="edit-btn">
        <EditIcon />
        Edit
      </button>
      
      <button className="copy-btn">
        <CopyIcon />
        Copy
      </button>
    </div>
  );
}
```

### Inline Editing

```css
.ai-editable {
  position: relative;
}

.ai-editable:hover .edit-trigger {
  opacity: 1;
}

.edit-trigger {
  position: absolute;
  top: 8px;
  right: 8px;
  opacity: 0;
  transition: opacity 200ms;
}
```

---

## PROGRESSIVE AI REVEAL

```css
@keyframes reveal-up {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.ai-message {
  animation: reveal-up 400ms ease-out forwards;
}

.ai-message:nth-child(2) { animation-delay: 100ms; }
.ai-message:nth-child(3) { animation-delay: 200ms; }
```

### Chunked Content Loading

```javascript
async function loadAIResponse(container, fullText) {
  const chunks = splitIntoChunks(fullText, 50); // 50 chars each
  
  for (const chunk of chunks) {
    container.textContent += chunk;
    await sleep(30); // Simulate streaming
    scrollToBottom(container);
  }
}
```

---

## TRUST & TRANSPARENCY

### Show AI Attribution

```css
.ai-attribution {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 12px;
  color: var(--text-muted);
  margin-top: 8px;
}

.ai-badge {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  padding: 2px 8px;
  background: var(--bg-tertiary);
  border-radius: 4px;
  font-size: 11px;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}
```

### Model Information

```jsx
function ModelInfo({ model, temperature, maxTokens }) {
  return (
    <details className="model-info">
      <summary>AI Model Details</summary>
      <dl>
        <dt>Model</dt>
        <dd>{model}</dd>
        <dt>Temperature</dt>
        <dd>{temperature}</dd>
        <dt>Max Tokens</dt>
        <dd>{maxTokens}</dd>
      </dl>
    </details>
  );
}
```

---

## ERROR STATES

```css
.ai-error {
  background: var(--error-bg, #fef2f2);
  border: 1px solid var(--error-border, #fecaca);
  border-radius: 8px;
  padding: 16px;
}

.ai-error-content {
  display: flex;
  gap: 12px;
  align-items: flex-start;
}

.ai-error-actions {
  display: flex;
  gap: 8px;
  margin-top: 12px;
}
```

### Retry Pattern

```jsx
function AIErrorBoundary({ children, fallback }) {
  const [hasError, setHasError] = useState(false);
  const [retryCount, setRetryCount] = useState(0);
  
  const handleRetry = () => {
    setRetryCount(c => c + 1);
    setHasError(false);
  };
  
  if (hasError && retryCount < 3) {
    return (
      <div className="ai-error">
        <p>Something went wrong. Try again?</p>
        <button onClick={handleRetry}>Retry</button>
      </div>
    );
  }
  
  return hasError ? fallback : children;
}
```

---

## ACCESSIBILITY

```css
/* Respect reduced motion */
@media (prefers-reduced-motion: reduce) {
  .streaming-text span,
  .ai-thinking span,
  .waveform-bar span {
    animation: none;
  }
  
  .ai-thinking span {
    opacity: 0.7;
  }
}
```

```jsx
/* Screen reader announcements */
function AIStatus({ status, message }) {
  return (
    <div 
      role="status" 
      aria-live="polite"
      className="sr-only"
    >
      {message}
    </div>
  );
}
```

---

## TOOLS & RESOURCES

- **Web Speech API**: https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API
- **TensorFlow.js**: https://www.tensorflow.org/js
- **Transformers.js**: https://huggingface.co/docs/transformers.js
- **AI UI Patterns**: https://uxdesign.cc/ai-ui-patterns

---

**Last Updated**: 2026-04-12
