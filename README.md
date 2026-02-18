# 🍎 Apple Developer Guide

> A comprehensive, community-friendly reference for Apple platform development.
> Built and maintained by [BlocLabs](https://bloclabs.com).

---

## Start Here

### 🧠 [Mental Models for World-Class Apple Development](docs/mental-models.md)
The thinking frameworks that separate elite iOS/macOS engineers from the rest. Engineering principles, design philosophy, architecture patterns, performance budgets, and security models. **Read this first.**

---

## Reference Guides

| Guide | Description |
|-------|-------------|
| [**Mental Models**](docs/mental-models.md) | **Engineering + design thinking frameworks — the WHY behind everything** |
| [Swift Language](docs/swift.md) | Modern Swift fundamentals, concurrency, protocols, macros |
| [SwiftUI](docs/swiftui.md) | Declarative UI, state management, navigation, animations |
| [EventKit](docs/eventkit.md) | Calendar events, reminders, alarms, recurrence, attendees |
| [Human Interface Guidelines](docs/hig.md) | Design principles, platform patterns, accessibility |
| [Design Resources](docs/design-resources.md) | UI kits, SF Symbols, fonts, Icon Composer, color system |
| [Key Frameworks](docs/frameworks.md) | Foundation, SwiftData, CloudKit, StoreKit, WidgetKit, and more |
| [Developer Program](docs/developer-program.md) | Certificates, provisioning, TestFlight, App Store distribution |
| [iOS Keyboard Extensions](docs/keyboard-extensions.md) | Building custom keyboards — constraints, best practices |

## Why This Exists

Finding good Apple developers is hard. The official documentation is sprawling. This repo consolidates the essential knowledge into digestible, opinionated guides with direct links to official Apple docs.

**This is not a replacement for Apple's documentation** — it's a map to navigate it efficiently.

## Philosophy

- **Opinionated** — SwiftUI over UIKit. @Observable over ObservableObject. SwiftData over Core Data. async/await over Combine.
- **Modern** — Swift 5.9+, iOS 17+, macOS 14+. We don't cover legacy patterns unless necessary.
- **Practical** — Code examples that compile. Patterns that ship.
- **Linked** — Every concept links to the official Apple documentation.

## Quick Start

New to Apple development?

1. Start with [Swift Language](docs/swift.md) — learn the language
2. Move to [SwiftUI](docs/swiftui.md) — build your first UI
3. Read the [HIG](docs/hig.md) — make it feel native
4. Explore [Key Frameworks](docs/frameworks.md) — add capabilities

Building a specific feature?

- Calendar/Reminders → [EventKit](docs/eventkit.md)
- Custom keyboard → [Keyboard Extensions](docs/keyboard-extensions.md)
- Data persistence → [SwiftData section](docs/frameworks.md#swiftdata)
- In-app purchases → [StoreKit section](docs/frameworks.md#storekit-2)

## Contributing

PRs welcome. Keep it practical, keep it linked, keep it current.

## License

MIT — use it, share it, improve it.

---

*Maintained by [BlocLabs](https://bloclabs.com) 🧱*
