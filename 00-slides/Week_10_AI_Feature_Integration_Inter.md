---
marp: true
theme: default
paginate: true
size: 16:9
---

<style>
section {
  font-size: 20px;
  padding: 70px 70px 64px 70px;
  color: #1b1b1b;
  background:
    linear-gradient(90deg, #f7941d 0%, #fdb913 100%) 0 0 / 100% 10px no-repeat,
    linear-gradient(155deg, #ffffff 55%, #eef0f2 100%);
  background-repeat: no-repeat;
}
section h1 { font-size: 34px; color: #111; margin: 0.35em 0; border-bottom: none; }
section h2 { font-size: 28px; color: #333; margin: 0.35em 0; }
section h3 { font-size: 22px; color: #1b2a4a; margin: 0.35em 0; }
section p, section ul, section ol { margin: 0.3em 0; }
section table { font-size: 18px; }
section pre, section code { font-size: 16px; }
section blockquote { font-size: 19px; }
section::before {
  content: "POLITEKNIK NEGERI MALANG\ADepartment of Information Technology";
  position: absolute;
  top: 26px;
  right: 36px;
  width: 360px;
  text-align: right;
  padding-right: 92px;
  white-space: pre;
  font-size: 12px;
  font-weight: 600;
  line-height: 1.45;
  color: #1b2a4a;
  background: url('jti-polinema-logo.png') no-repeat right center / 96px 48px;
  z-index: 10;
}
section::after {
  content: "D4 Teknik Informatika  ·  " counter(page);
  position: absolute;
  left: 0; right: 0; bottom: 0;
  height: 30px;
  padding-left: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #ffffff;
  font-size: 13px;
  background:
    url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="200" height="30"><text x="100" y="20" text-anchor="middle" font-family="Arial" font-size="13" font-weight="bold" fill="%231b2a4a">jti.polinema.ac.id</text></svg>') no-repeat left top / 200px 30px,
    linear-gradient(to right, #f7941d 0 200px, #1b2a4a 200px 100%);
}
section.lead { justify-content: center; text-align: left; padding-left: 80px; }
section.lead h1 { font-size: 46px; font-weight: 800; color: #111; border: none; }
section.lead h2 { font-size: 32px; font-weight: 600; color: #666; margin-top: 0; }
section.lead h3 { font-size: 19px; font-weight: 500; color: #222; margin-top: 18px; }
section.lead p { color: #333; }
section.fit { display: flex; flex-direction: column; justify-content: center; }
section.fit h1 { font-size: 30px; margin: 0.25em 0; }
section.fit h2 { font-size: 24px; margin: 0.25em 0; }
section.fit h3 { font-size: 20px; margin: 0.25em 0; }
section.fit p, section.fit ul, section.fit ol { margin: 0.25em 0; }
section.fit pre { margin: 0.3em 0; line-height: 1.35; }
section.fit pre, section.fit code { font-size: 15px; }
section.fit blockquote { font-size: 17px; margin: 0.3em 0; }
section.fit table { font-size: 17px; }
</style>

<!-- _class: lead -->

# Week 10
## AI Feature Integration

**Mobile Programming**

Flutter | LLM API | Chat | OCR | STT | Image Understanding

> From AI experimentation to safe, production-ready features

---

# Learning Outcomes

After this session, students will be able to:

1. Explain the concepts of AI model integration into mobile apps.
2. Design a chat interface with streaming responses.
3. Integrate OCR, Speech-to-Text, and Image Understanding.
4. Apply safe and effective prompt design.
5. Handle privacy, cost, error handling, and fallback design.

---

# Why AI Feature Integration?

AI is no longer just an experiment — it is now a core part of modern applications:

- **Chat assistant** — real-time Q&A
- **OCR** — read text from images or documents
- **Speech-to-Text** — convert voice input to text
- **Image Understanding** — caption and analyze images
- **Recommendation** — personalized suggestions based on user data

AI features must be safe, well-tested, and have fallbacks when the service is unavailable.

---

# AI Integration Architecture (Mobile)

```text
+----------------+      HTTPS/wss      +----------------+
|   Flutter UI   | <----------------> |  AI API Layer  |
|  (Chat, OCR,   |      (Dio/http)     |  (LLM, OCR,    |
|   STT, Image)  |                     |   STT, Vision) |
+----------------+                     +----------------+
        |                                      |
        | API Key (secure storage)             | Response
        v                                      v
+----------------+      Optional      +----------------+
|  Local Cache   | <----------------> |   Backend    |
|  (Hive/SQLite) |     (batching)     |  (auth, log) |
+----------------+                     +----------------+
```

- API keys must never be stored directly in source code
- Use `flutter_secure_storage` to store tokens and keys
- Consider cost per request and rate limits

---

<!-- _class: fit -->

# Prompt Design for Mobile

| Component | Example | Purpose |
|-----------|---------|---------|
| System prompt | "You are a friendly campus assistant, answer in Indonesian, max 2 sentences" | Set persona and boundaries |
| User prompt | "Class schedule on Monday?" | User input |
| Context | Schedule data, user profile, location | Make response relevant |
| Limits | Do not share other users' private data | Privacy and security |

Tips: keep prompts short and specific, translate output if needed, and validate output before showing it to the user.

---

# Chat Interface and UX

```text
[User: Hello, any campus events tomorrow?]
        |
        v
[AI is typing...]  <-- streaming response (bubble animation)
        |
        v
[AI: Tomorrow there is Mobile Programming class at 08:00]
```

UX considerations:
- Streaming responses with `StreamBuilder`
- "Typing" indicator while waiting for a response
- Auto-scroll to latest message
- Regenerate and copy buttons
- Empty state when no conversation exists yet

---

# OCR, Speech-to-Text, and Image Understanding

**OCR (Optical Character Recognition):**
- Take photo from camera or gallery
- Send to AI API for text extraction
- Use for: student ID, receipts, documents, bulletin boards

**Speech-to-Text:**
- Record user voice
- Convert to text via AI API
- Use for: fast input, accessibility

**Image Understanding:**
- Upload image and ask AI for description
- Example: "What is in this image?"
- Response: "The image shows a student in the library holding a book."
- Use for: product description, object identification, accessibility for visually impaired users

```dart
final result = await aiService.recognizeText(imageFile);
final text = await aiService.speechToText(audioFile);
```

---

<!-- _class: fit -->

# Privacy, Cost, Error Handling, and Fallback

| Risk | Mitigation |
|------|------------|
| User data leakage | Never send sensitive data to AI API |
| Cost spike | Limit requests per user per day, use local cache |
| API error / down | Show friendly message, retry option, offline mode |
| Irrelevant response | Apply validation and fallback to local data |

Fallback example:
```dart
try {
  final response = await aiService.ask(question);
} catch (e) {
  showDialog(
    context: context,
    builder: (_) => AlertDialog(
      title: const Text('AI Service Busy'),
      content: const Text('Try again later or use offline mode.'),
    ),
  );
}
```

---

<!-- _class: fit -->

# Quick Quiz (1/2)

### 1. What should be avoided when sending data to AI API?

A. General non-sensitive data  
B. National ID, phone number, user address  
C. Public course names  
D. Campus assignment descriptions  

---

<!-- _class: fit -->

# Quick Quiz (2/2)

### 2. Which feature is suitable for an offline fallback?

A. Real-time chat streaming  
B. OCR from image  
C. Showing schedule from local cache  
D. Speech-to-Text  

---

<!-- _class: fit -->

# Quiz Answers

### 1 -> B

```text
Never send user identity data to AI API
Use anonymous data or internal IDs only
```

### 2 -> C

```text
Schedule from local cache is accessible without internet
AI features require connection, so a fallback is needed
```

---

<!-- _class: fit -->

# Deliverables

## Repository

```text
10-week-10-ai-feature-integration/
|-- lib/
|   |-- ai/
|   |   |-- ai_service.dart
|   |-- features/
|   |   |-- chat_screen.dart
|-- docs/
|   |-- ai-log.md
|-- test/
|-- screenshots/
|-- README.md
```

Minimum README content:

- AI feature + prompt used
- `flutter analyze` + `flutter test` results
- Screenshots of chat/OCR/STT feature
- Risk table + privacy mitigation
- User testing evidence (user feedback)

---

<!-- _class: fit -->

# Week 10 Rubric

| Component | Weight |
|-----------|-------:|
| AI Feature Integration (Chat/OCR/STT/Image) | 25% |
| Prompt Design and Response Handling | 20% |
| Privacy, Cost, and Error Handling | 20% |
| UX and Fallback Feature | 15% |
| Testing and Documentation | 10% |
| Presentation and Reflection | 10% |
| **Total** | **100%** |

---

<!-- _class: fit -->

# Checklist Before Submit

### AI Integration

- [ ] AI feature integrated into the app (chat or OCR or STT)
- [ ] System prompt and user prompt clearly defined
- [ ] Streaming response or loading indicator implemented

### Privacy and Security

- [ ] No sensitive data sent to AI API
- [ ] API key stored in `flutter_secure_storage`
- [ ] Error handling and fallback available

### Testing

- [ ] `flutter analyze` clean
- [ ] At least one widget test for the AI feature
- [ ] Screenshots of AI feature in the app

### Documentation

- [ ] `docs/ai-log.md` complete (prompts, responses, decisions)
- [ ] README explains the feature, how to test, and results

---

<!-- _class: fit -->

# Exit Ticket

Answer before class ends:

### 1. Which AI feature did you integrate and why?

### 2. How do you protect user privacy when using AI API?

### 3. What fallback did you provide if AI service errors?

### 4. One thing you learned about AI feature UX on mobile?

---

<!-- _class: fit -->

# Key Takeaways

## Integration

> AI is a feature, not a replacement for architecture.

## Privacy

> Never send sensitive data to AI API.

## UX

> Streaming + loading state = better experience.

## Fallback

> Always provide an exit when AI is unavailable.

## AI

> AI proposes responses.
> **Developers decide.**

---

# Next Week

## Week 11: Performance Optimization

```text
AI feature integrated
       |
       v
Widget rebuild processing
       |
       v
Image storage + lazy loading
       |
       v
Faster and more efficient app
```

### Goal

> Optimize app performance after AI features are added.

---

# Build -> Verify -> Explain

## The Mobile Learning Principle

1. AI proposes an answer
2. Developer verifies
3. Test to confirm
4. Fix if not accurate
5. Explain the reasoning behind decisions

## Do not just be able to use AI.

### Be a developer who can explain the AI features you build.

- Apply local cache to reduce repeated AI API calls
