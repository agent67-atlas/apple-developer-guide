# iOS Keyboard Extensions

> Building custom keyboards for iOS — constraints, architecture, and best practices.
> Directly relevant to **KeyFlo** (BlocLabs).

## Official Resources

- [Custom Keyboard Documentation](https://developer.apple.com/documentation/uikit/keyboards_and_input/creating_a_custom_keyboard)
- [App Extension Programming Guide](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html)

---

## Architecture

A keyboard extension is an **App Extension** — a separate binary that runs inside the host app's process.

```
┌─────────────────────────────┐
│ Host App (Messages, Safari) │
│  ┌───────────────────────┐  │
│  │  Keyboard Extension   │  │
│  │  (Your code runs here)│  │
│  └───────────────────────┘  │
└─────────────────────────────┘
         ↕ (limited)
┌─────────────────────────────┐
│ Containing App (KeyFlo)     │
│ (Settings, onboarding, IAP) │
└─────────────────────────────┘
```

### Key Classes
- **`UIInputViewController`** — main controller for your keyboard
- **`UITextDocumentProxy`** — interface to the text field (insert/delete text, move cursor)

```swift
class KeyboardViewController: UIInputViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        // Build your keyboard UI here
    }
    
    // Insert text
    func type(_ text: String) {
        textDocumentProxy.insertText(text)
    }
    
    // Delete
    func deleteBackward() {
        textDocumentProxy.deleteBackward()
    }
    
    // Read context
    var beforeCursor: String? {
        textDocumentProxy.documentContextBeforeInput
    }
    
    // Switch to next keyboard
    func switchKeyboard() {
        advanceToNextInputMode()
    }
}
```

---

## Constraints (Critical)

### Memory
- **~50MB limit** — extension killed if exceeded
- Profile with Instruments → Allocations
- Lazy-load resources, compress images
- No large ML models without careful memory management

### Sandboxing
- **No shared filesystem** with containing app (unless App Groups)
- **No network access** by default
- Must declare `RequestsOpenAccess` in Info.plist for:
  - Network access
  - Shared App Group container
  - Address book access
  - Location services

### App Groups (Data Sharing)
```swift
// In both keyboard extension and containing app:
let sharedDefaults = UserDefaults(suiteName: "group.com.bloclabs.keyflo")
sharedDefaults?.set("value", forKey: "key")

// File sharing
let containerURL = FileManager.default.containerURL(
    forSecurityApplicationGroupIdentifier: "group.com.bloclabs.keyflo"
)
```

### What You CAN'T Do
- Access microphone (no voice input without open access)
- Access camera
- Access Game Center
- Play audio
- Access HealthKit
- Access the containing app's data directly
- Run background tasks

### What You CAN Do
- Draw any custom UI
- Access text before/after cursor via `textDocumentProxy`
- Network requests (with open access)
- Shared data via App Groups
- Haptic feedback
- Custom auto-correct / suggestions

---

## Open Access

Adding `RequestsOpenAccess = YES` to Info.plist:

**Gains:**
- Network access (API calls, sync)
- App Group shared container
- Address book (for contact suggestions)
- Location services

**Costs:**
- Users see a scary privacy warning during setup
- Apple reviews more carefully
- Users may not trust it — minimize what you request

**For KeyFlo:** Open access is required for payment APIs and blockchain interactions.

---

## UI with SwiftUI

You can use SwiftUI inside the keyboard extension:

```swift
class KeyboardViewController: UIInputViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let keyboardView = KeyFloKeyboard(
            proxy: textDocumentProxy,
            advanceToNextInputMode: { [weak self] in
                self?.advanceToNextInputMode()
            }
        )
        
        let hostingController = UIHostingController(rootView: keyboardView)
        addChild(hostingController)
        view.addSubview(hostingController.view)
        hostingController.view.translatesAutoresizingMaskIntoConstraints = false
        
        NSLayoutConstraint.activate([
            hostingController.view.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            hostingController.view.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            hostingController.view.topAnchor.constraint(equalTo: view.topAnchor),
            hostingController.view.bottomAnchor.constraint(equalTo: view.bottomAnchor),
        ])
        
        hostingController.didMove(toParent: self)
    }
}
```

---

## Height Management

```swift
// Set custom height
override func viewWillLayoutSubviews() {
    super.viewWillLayoutSubviews()
    
    // Standard keyboard height varies by device
    // Don't set too tall — user experience suffers
    let heightConstraint = view.heightAnchor.constraint(equalToConstant: 260)
    heightConstraint.priority = .defaultHigh
    heightConstraint.isActive = true
}
```

**Guidelines:**
- Match standard keyboard height when possible
- Expand only when needed (e.g., payment flow)
- Animate height changes smoothly
- Test on all device sizes (SE to Pro Max)

---

## Globe Key (Next Keyboard)

**Required:** Every keyboard must include a way to switch keyboards.

```swift
// System globe button
let globeButton = UIButton(type: .system)
globeButton.setImage(UIImage(systemName: "globe"), for: .normal)
globeButton.addTarget(self, action: #selector(handleInputModeList(from:with:)), for: .allTouchEvents)
```

If you don't include this, your keyboard will be **rejected** from the App Store.

---

## Performance

### Startup Time
- Keyboard must appear **instantly** — users notice any delay
- Lazy-load anything not needed for initial render
- Pre-warm in containing app if possible
- Avoid heavy `viewDidLoad` work

### Responsiveness
- Key taps must register < 16ms
- No blocking main thread
- Background any network/crypto work
- Use `Task.detached` for heavy computation

### Memory
```swift
override func didReceiveMemoryWarning() {
    super.didReceiveMemoryWarning()
    // Release caches, non-essential resources
    // You're about to be killed
}
```

---

## Security Considerations (KeyFlo-Specific)

For a payment keyboard:
1. **Never log keystrokes** — even for debugging
2. **Secure Enclave** for key storage when possible
3. **Biometric auth** via containing app (keyboard can't access LocalAuthentication directly)
4. **Network pinning** for API calls
5. **Minimal data in App Group** — encrypt sensitive shared data
6. **Clear sensitive data** from memory when switching away

---

## Testing

- Test on **physical devices** — Simulator doesn't accurately represent keyboard performance
- Test with **different text fields** — UITextField, UITextView, WKWebView, etc.
- Test **switching** to/from your keyboard
- Test **memory** under Instruments
- Test **startup time** — cold and warm
- Test with **accessibility** — VoiceOver, larger text

---

## App Store Considerations

- Include clear instructions for enabling the keyboard
- Explain why Open Access is needed (if used)
- Privacy policy must cover all data handling
- Screenshots should show the keyboard in action
- Don't mimic the system keyboard too closely

---

*← [Developer Program](developer-program.md)*
