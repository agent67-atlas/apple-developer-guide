# Human Interface Guidelines (HIG)

> Apple's definitive guide to designing native, intuitive apps across all platforms.

## Official Resources

- [HIG Home](https://developer.apple.com/design/human-interface-guidelines/)
- [Getting Started](https://developer.apple.com/design/human-interface-guidelines/getting-started)
- [Foundations](https://developer.apple.com/design/human-interface-guidelines/foundations)
- [Patterns](https://developer.apple.com/design/human-interface-guidelines/patterns)
- [Components](https://developer.apple.com/design/human-interface-guidelines/components)
- [Inputs](https://developer.apple.com/design/human-interface-guidelines/inputs)
- [Technologies](https://developer.apple.com/design/human-interface-guidelines/technologies)

---

## Core Design Principles

### 1. Clarity
- Text is legible at every size
- Icons are precise and lucid
- Adornments are subtle and appropriate
- Function drives design

### 2. Deference
- Fluid motion and crisp interface
- Content is paramount
- UI helps people understand and interact with content — never competes with it

### 3. Depth
- Distinct visual layers communicate hierarchy
- Realistic motion conveys vitality
- Touch and discoverability heighten delight

### 4. Consistency
- Standard controls and predictable behavior
- System-provided icons and interface elements
- Platform conventions respected

### 5. Direct Manipulation
- Screen content responds to gestures immediately
- Real-time feedback for every action

### 6. Feedback
- Acknowledge every action
- Show progress for long-running tasks
- Animate transitions to provide context

---

## Platform-Specific Guidelines

### iOS / iPadOS
- **Touch-first** — minimum tap target 44×44pt
- **Tab bars** — primary navigation (max 5 tabs)
- **Swipe gestures** — back navigation, delete, actions
- **Dynamic Island** — live status updates
- **Live Activities** — Lock Screen real-time info
- **Safe areas** — respect notch, home indicator, Dynamic Island
- [iOS Guidelines](https://developer.apple.com/design/human-interface-guidelines/designing-for-ios)

### macOS
- **Keyboard & mouse first** — hover states, right-click menus
- **Menu bar** — primary app commands
- **Window management** — resizable, multiple windows
- **Toolbars** — contextual actions
- **Sidebar** — navigation pattern
- [macOS Guidelines](https://developer.apple.com/design/human-interface-guidelines/designing-for-macos)

### watchOS
- **Glanceable** — information at a glance
- **Digital Crown** — scrolling and input
- **Complications** — watch face data
- **Brief interactions** — seconds, not minutes
- [watchOS Guidelines](https://developer.apple.com/design/human-interface-guidelines/designing-for-watchos)

### tvOS
- **Focus-based** — no touch, arrow navigation
- **Siri Remote** — swipe and click
- **10-foot UI** — readable from across the room
- **Parallax effects** — depth and dimension
- [tvOS Guidelines](https://developer.apple.com/design/human-interface-guidelines/designing-for-tvos)

### visionOS
- **Spatial computing** — windows, volumes, full spaces
- **Eye tracking** — gaze as primary input
- **Hand gestures** — tap, pinch, drag
- **Ergonomics** — comfortable in 3D space
- [visionOS Guidelines](https://developer.apple.com/design/human-interface-guidelines/designing-for-visionos)

---

## Typography

### System Fonts
| Font | Use Case |
|------|----------|
| SF Pro | iOS, macOS, tvOS, visionOS system font |
| SF Compact | watchOS system font |
| SF Mono | Code, monospaced content |
| New York | Serif, reading-heavy contexts |

### Text Styles
Use semantic text styles for Dynamic Type support:

```swift
Text("Title")
    .font(.title)          // Large title

Text("Headline")
    .font(.headline)       // Bold, prominent

Text("Body text")
    .font(.body)           // Default reading size

Text("Caption")
    .font(.caption)        // Small, secondary
```

**Rules:**
- Always use system text styles — they scale with Dynamic Type
- Never hardcode font sizes
- Test with the largest accessibility text size
- Support Bold Text accessibility setting

📖 [Typography](https://developer.apple.com/design/human-interface-guidelines/typography)

---

## Color

### Semantic Colors
```swift
Text("Primary")
    .foregroundStyle(.primary)     // Adapts to light/dark

Text("Secondary")
    .foregroundStyle(.secondary)   // Lower emphasis

Button("Action") {}
    .tint(.accentColor)            // App accent color
```

### System Colors
All adapt to light/dark mode and accessibility:
- `.red`, `.orange`, `.yellow`, `.green`, `.mint`, `.teal`, `.cyan`, `.blue`, `.indigo`, `.purple`, `.pink`, `.brown`

### Rules
- Use semantic colors — they adapt automatically
- Ensure 4.5:1 contrast ratio for text (WCAG AA)
- Don't use color as the only means of conveying information
- Define light and dark appearances for all custom colors
- Test with Increase Contrast accessibility setting

📖 [Color](https://developer.apple.com/design/human-interface-guidelines/color)

---

## Layout

### Key Concepts
- **Safe Areas** — respect system UI (notch, home indicator, keyboard)
- **Margins** — system margins adapt to device size
- **Spacing** — consistent 8pt grid system
- **Alignment** — left-align text in LTR, use leading/trailing not left/right

### Responsive Layout
```swift
// Adapt to size class
@Environment(\.horizontalSizeClass) var sizeClass

var body: some View {
    if sizeClass == .compact {
        // iPhone portrait — stack vertically
        VStack { content }
    } else {
        // iPad/landscape — side by side
        HStack { content }
    }
}
```

### Rules
- Never clip content — allow scrolling
- Respect Dynamic Type — layouts must accommodate large text
- Support both orientations (unless specific reason not to)
- Use system spacing and margins

📖 [Layout](https://developer.apple.com/design/human-interface-guidelines/layout)

---

## Icons & Images

### SF Symbols
6,900+ symbols that scale with text:

```swift
Image(systemName: "arrow.up.circle.fill")
    .font(.title)                    // Scales with text style
    .symbolRenderingMode(.palette)   // Multi-color
    .foregroundStyle(.white, .blue)

// Variable value (0.0 - 1.0)
Image(systemName: "wifi", variableValue: 0.7)
```

### App Icons
- Provide a single 1024×1024 source image
- System generates all sizes
- Use Icon Composer for layered Liquid Glass icons (iOS 26+)
- No text in icons (doesn't localize)
- No photos (too detailed at small sizes)

📖 [App Icons](https://developer.apple.com/design/human-interface-guidelines/app-icons)

---

## Accessibility — Non-Negotiable

Every app must support:

1. **VoiceOver** — screen reader support
2. **Dynamic Type** — text scaling up to 310%
3. **Color contrast** — 4.5:1 minimum for text
4. **Reduce Motion** — alternative animations
5. **Bold Text** — heavier font weights
6. **Switch Control** — sequential focus navigation
7. **Voice Control** — voice-based interaction

### Testing Checklist
- [ ] VoiceOver can navigate every screen
- [ ] All images have accessibility labels
- [ ] All controls have accessible names
- [ ] Dynamic Type works at every size
- [ ] Dark mode looks correct
- [ ] Reduce Motion alternative exists for animations
- [ ] No information conveyed by color alone

📖 [Accessibility](https://developer.apple.com/design/human-interface-guidelines/accessibility)

---

## Common Components

### Navigation
- **Tab Bar** — 3-5 primary destinations (iOS)
- **Sidebar** — hierarchical navigation (iPadOS/macOS)
- **Toolbar** — contextual actions
- **Search** — searchable content

### Content
- **Lists** — scrollable rows of data
- **Grids** — visual content in columns
- **Cards** — grouped related content
- **Empty states** — helpful when no content

### Input
- **Text Fields** — with clear buttons, validation
- **Pickers** — date, selection, color
- **Toggles** — boolean settings
- **Sliders** — continuous values
- **Steppers** — discrete increments

### Feedback
- **Alerts** — important decisions (destructive actions)
- **Sheets** — supplementary content
- **Toasts** — brief status messages
- **Progress** — loading states (determinate/indeterminate)

📖 [Components](https://developer.apple.com/design/human-interface-guidelines/components)

---

## App Store Review Considerations

Your app will be **rejected** if:
- Crashes or has obvious bugs
- Broken links or placeholder content
- Missing privacy descriptions (Info.plist)
- Non-standard UI that confuses users
- Accessibility violations (VoiceOver doesn't work)
- Misleading app description or screenshots
- Uses private APIs

📖 [App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/)

---

*← [EventKit](eventkit.md) · [Design Resources →](design-resources.md)*
