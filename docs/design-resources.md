# Apple Design Resources

> Templates, fonts, symbols, and tools for Apple platform design.

## Official Resources

- [Apple Design Resources](https://developer.apple.com/design/resources/)
- [SF Symbols](https://developer.apple.com/sf-symbols/)
- [Icon Composer](https://developer.apple.com/icon-composer/)
- [Design Whats New](https://developer.apple.com/design/whats-new/)

---

## UI Kits

Design templates for building native-looking interfaces:

| Platform | Latest | Formats |
|----------|--------|---------|
| iOS / iPadOS 26 | UI Kit | Figma, Sketch |
| macOS 26 | UI Kit | Figma, Sketch |
| watchOS 26 | UI Kit | Figma, Sketch |
| visionOS 26 | UI Kit | Figma, Sketch |
| tvOS 18 | UI Kit | Figma, Sketch |

Each kit includes:
- System controls and components
- Navigation bars, tab bars, toolbars
- Keyboards, alerts, action sheets
- Status bars, home indicators
- Dark mode variants

---

## SF Symbols

Library of **6,900+ symbols** designed for Apple platforms.

### Key Features
- 9 weights × 3 scales
- Automatic text alignment
- Variable color (0.0 - 1.0)
- 4 rendering modes: monochrome, hierarchical, palette, multicolor
- Animation effects (bounce, pulse, variable color, replace, draw)
- Categories: communication, weather, commerce, gaming, etc.

### Usage in SwiftUI
```swift
// Basic
Image(systemName: "star.fill")

// Sized with font
Image(systemName: "chart.line.uptrend.xyaxis")
    .font(.title2)

// Rendering modes
Image(systemName: "cloud.sun.fill")
    .symbolRenderingMode(.multicolor)

Image(systemName: "battery.75percent")
    .symbolRenderingMode(.palette)
    .foregroundStyle(.green, .gray)

// Variable value
Image(systemName: "speaker.wave.3.fill", variableValue: volume)

// Animations (iOS 17+)
Image(systemName: "bell.fill")
    .symbolEffect(.bounce, value: notificationCount)
```

### SF Symbols App
Download from [developer.apple.com/sf-symbols](https://developer.apple.com/sf-symbols/) to:
- Browse all symbols
- Search by name or category
- Preview rendering modes
- Export custom symbols
- Create custom symbols from templates

### Custom Symbols
- Export a template from SF Symbols app
- Edit in vector tool (Figma, Sketch, Illustrator)
- Import back — inherits all weights, scales, rendering modes

---

## Fonts

| Font | Download | Purpose |
|------|----------|---------|
| **SF Pro** | [Download](https://developer.apple.com/fonts/) | System font — iOS, macOS, tvOS, visionOS |
| **SF Compact** | [Download](https://developer.apple.com/fonts/) | watchOS system font |
| **SF Mono** | [Download](https://developer.apple.com/fonts/) | Code, monospaced content |
| **New York** | [Download](https://developer.apple.com/fonts/) | Serif, reading contexts |
| **SF Arabic** | [Download](https://developer.apple.com/fonts/) | Arabic script |
| **SF Armenian** | [Download](https://developer.apple.com/fonts/) | Armenian script |
| **SF Georgian** | [Download](https://developer.apple.com/fonts/) | Georgian script |
| **SF Hebrew** | [Download](https://developer.apple.com/fonts/) | Hebrew script |

### Usage Rules
- Use system fonts for all UI text
- SF Pro for interface, New York for reading
- Always use text styles (`.body`, `.headline`) not fixed sizes
- Font smoothing is automatic on Apple displays

---

## Icon Composer

New tool for iOS 26+ layered Liquid Glass icons:

- Create multi-layer icon compositions
- Dynamic lighting effects preview
- Adjust Liquid Glass properties
- Light and dark mode annotation
- Seamless Xcode integration
- Export flattened versions for marketing

[Download Icon Composer](https://developer.apple.com/icon-composer/)

### App Icon Guidelines
- Single 1024×1024 source (Xcode generates all sizes)
- Simple, recognizable silhouette
- No text (doesn't localize)
- No screenshots or photos
- Consistent across platforms

---

## Color System

### System Colors (adapt to appearance)
```
red · orange · yellow · green · mint · teal
cyan · blue · indigo · purple · pink · brown
```

### Semantic Colors
```
.primary · .secondary · .tertiary · .quaternary
.accent · .background · .fill
```

### Calendar Event Colors
For EventKit integration — 11 standard event colors:

| ID | Color |
|----|-------|
| 1 | #a4bdfc (lavender) |
| 2 | #7ae7bf (sage) |
| 3 | #dbadff (grape) |
| 4 | #ff887c (flamingo) |
| 5 | #fbd75b (banana) |
| 6 | #ffb878 (tangerine) |
| 7 | #46d6db (peacock) |
| 8 | #e1e1e1 (graphite) |
| 9 | #5484ed (blueberry) |
| 10 | #51b749 (basil) |
| 11 | #dc2127 (tomato) |

### Custom Colors
- Define in Asset Catalog with light + dark appearances
- Use `Color("customName")` in SwiftUI
- Always ensure WCAG AA contrast (4.5:1 for text)
- Test with Increase Contrast setting enabled

---

## Technology Templates

Apple provides design templates for specific technologies:

- **App Clips** — design template for App Clip cards
- **Live Activities** — Lock Screen / Dynamic Island layouts
- **Siri & Shortcuts** — App Intents design patterns
- **Apple Pay** — payment button and sheet designs
- **Sign in with Apple** — button assets and guidelines
- **WidgetKit** — widget size templates (small, medium, large, extra large)
- **Messages** — iMessage app / sticker design
- **TipKit** — in-app tip presentation
- **Games / Game Center** — achievement and leaderboard design

---

## Product Bezels

For marketing materials — device frames for:
- iPhone 17, iPhone 16
- iPad Pro M4, iPad Air M2, iPad mini
- MacBook Pro M4, MacBook Air M3
- iMac
- Apple Watch Series 11, Ultra 3
- Apple TV

Available in Figma, Sketch, and PNG formats.

[Download All Bezels](https://developer.apple.com/design/resources/)

---

## Design Learning Path

1. **Start:** [Design Get Started](https://developer.apple.com/design/get-started/)
2. **Deep dive:** [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
3. **Tools:** [SF Symbols](https://developer.apple.com/sf-symbols/) + [Design Resources](https://developer.apple.com/design/resources/)
4. **Inspiration:** [Apple Design Awards](https://developer.apple.com/design/awards/)
5. **Videos:** [WWDC Design Videos](https://developer.apple.com/videos/design)

---

*← [HIG](hig.md) · [Key Frameworks →](frameworks.md)*
