# Firefox Architecture Analysis

## 1. Context & Background

## 2. Development View

### 2.1 System Components

### 2.2 Component Diagram

### 2.3 Dependencies

### 2.4 Codeline Model

### 2.5 Testing & Configuration

## 3. Applied Perspective: Evolution

### What is the Evolution Perspective?

The **Evolution perspective** focuses on how easily a software system can be modified to accommodate future changes. For a project as large and long-lived as Firefox — with over 20 years of continuous development, thousands of contributors, and millions of lines of code — evolution is not just a quality attribute; it's a survival requirement.

### Key Concerns

The primary concerns of the Evolution perspective in Firefox include:

| Concern | Description |
|---------|-------------|
| **Modifiability** | How easily can a developer add a new feature or fix a bug without unintended side effects? |
| **Extensibility** | Can new functionality be added without modifying existing core code (Open/Closed Principle)? |
| **Testability** | Does the architecture support automated testing to prevent regressions during evolution? |
| **Decoupling** | Are components isolated so that changes in one area don't cascade unpredictably? |
| **Documentation** | Is the architecture well-documented so new contributors can understand it? |

### Relevance to Firefox

For the scope of this analysis — focusing on the `devtools` (Developer Tools) component — these concerns are particularly relevant. Devtools evolves rapidly to support new web platform features, debugger improvements, and performance profiling enhancements.

A well-evolved architecture allows Mozilla's developers and external contributors to:
- Add new panels to DevTools
- Extend existing debugging capabilities
- Fix bugs with confidence
- Trust that automated tests will catch regressions
- Rely on changes being isolated to specific modules

### Architecture Diagram

![Architecture Diagram](image/architecture_diagram.drawio.png)

**Caption:** The DevTools architecture uses stable core interfaces that allow new panels to be added without modifying existing ones. Each panel is independently extensible, supporting the Evolution perspective through decoupling and well-defined boundaries.

### Current State Assessment

The Firefox DevTools architecture demonstrates several evolution-friendly characteristics:

- **Modular panel system:** Each tool (Inspector, Console, Debugger) is a separate module
- **Clear extension points:** New panels can be registered without touching core code
- **Comprehensive test suite:** Mochitests and xpcshell tests catch regressions
- **Documented APIs:** Developer documentation supports new contributors

However, challenges remain:
- Cross-panel dependencies can create coupling
- Legacy code paths (pre-e10s, pre-Firefox 57) add complexity
- Some areas lack automated test coverage

### Next Steps (For Final Report)

The final submission will expand this section with:

1. **Specific code evidence** showing evolution support (e.g., `nsIObserver` for extensibility)
2. **Concrete examples** of past evolutionary changes (e.g., adding Network Monitor, multiprocess support)
3. **Assessment** of strengths and weaknesses in the current architecture
4. **Recommendations** for improving evolvability

---

*This section is at ~25% completion for the checkpoint and will be expanded for the final submission.*

## 4. Architectural Styles & Patterns

### 4.1 Architectural Style

### 4.2 Design Patterns

#### Observer Pattern (Behavioral)

##### Location

`devtools/server/actors/highlighters/eye-dropper.js`

| File | Evidence |
|------|----------|
| `eye-dropper.js` | Extends `EventEmitter` (comment: *"Make classes extend EventEmitter"*) |
| `paused-debugger.js` | Extends `EventEmitter` to observe debugger state changes |
| `remote-node-picker-notice.js` | Same observer pattern |
| `measuring-tool.js` | Observer pattern present (refactored from static EventEmitter) |
| `rulers.js` | Same observer pattern |

##### Description

The Observer pattern is implemented in Firefox DevTools highlighters via the **`EventEmitter`** class. Files in `devtools/server/actors/highlighters/` such as `eye-dropper.js`, `paused-debugger.js`, and `remote-node-picker-notice.js` extend `EventEmitter`, allowing them to:

1. **Emit events** — notify listeners when something changes in the page (scroll, resize, DOM mutation)
2. **Listen for events** — react to changes and update visual overlays accordingly

##### Why It Matters for Evolution

The Observer pattern supports the **Evolution perspective** by:
- **Decoupling** the highlighter UI from the page events it responds to
- **Extensibility** — new highlighters can be added without modifying existing event logic
- **Testability** — events can be simulated in isolation

##### Code Evidence

The EyeDropper class implements the Observer pattern by extending `EventEmitter`:

```javascript
const EventEmitter = require("resource://devtools/shared/event-emitter.js");

class EyeDropper extends EventEmitter {
  constructor(highlighterEnv) {
    super();
    // ...
  }
}
```

## 5. Architectural Assessment
