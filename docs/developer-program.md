# Apple Developer Program & Distribution

> From code to App Store — certificates, signing, TestFlight, and distribution.

## Official Resources

- [Apple Developer Program](https://developer.apple.com/programs/) — $99/year
- [App Store Connect](https://appstoreconnect.apple.com/)
- [Developer Pathway](https://developer.apple.com/pathways/developer/)
- [App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/)

---

## Getting Started (Free)

Even without the paid program:
- Download and use **Xcode**
- Access **prerelease software** (betas)
- Use **Apple Developer Forums**
- Submit **Feedback Assistant** reports
- Run apps on **Simulator**
- Deploy to **personal device** (7-day provisioning)

## Apple Developer Program ($99/year)

Required for:
- App Store distribution
- TestFlight beta testing
- Advanced capabilities (Push Notifications, CloudKit, App Groups, etc.)
- Access to app analytics

### Fee Waiver
Eligible organizations (nonprofits, educational, government) can apply: [Fee Waiver](https://developer.apple.com/support/fee-waiver/)

---

## Certificates & Signing

### Certificate Types
| Type | Purpose |
|------|---------|
| **Development** | Run on physical devices during development |
| **Distribution** | Sign for App Store / TestFlight |

### Provisioning Profiles
Link together: App ID + Certificate + Device(s)

- **Development profile** — for testing on devices
- **Distribution profile** — for App Store submission
- **Ad Hoc profile** — for limited device distribution

### Automatic Signing (Recommended)
Xcode handles everything:
1. Project Settings → Signing & Capabilities
2. Check "Automatically manage signing"
3. Select your team
4. Done

### Manual Signing (When Needed)
- [Certificates overview](https://developer.apple.com/help/account/certificates/certificates-overview/)
- Create via Xcode or developer.apple.com
- Export/share via `.p12` files for CI/CD

---

## Capabilities & Entitlements

Enable in Xcode → Signing & Capabilities → `+ Capability`:

| Capability | Use Case |
|------------|----------|
| Push Notifications | Remote alerts |
| App Groups | Share data between app and extensions |
| Keychain Sharing | Share credentials between apps |
| iCloud | CloudKit, key-value storage |
| In-App Purchase | StoreKit |
| Sign in with Apple | Apple ID authentication |
| HealthKit | Health data access |
| Network Extensions | VPN, content filter |
| Custom Keyboard | Keyboard extension (KeyFlo) |

---

## TestFlight

Beta distribution — up to **10,000 external testers**.

### Internal Testing
- Up to 100 members of your team
- No App Store Review required
- Automatic builds from Xcode Cloud

### External Testing
- Up to 10,000 testers
- Requires Beta App Review (lighter than full review)
- Invite via email or public link
- Testers install TestFlight app from App Store

### Workflow
1. Archive in Xcode → Upload to App Store Connect
2. App Store Connect → TestFlight → Add testers
3. Testers receive notification → install → test → submit feedback

---

## App Store Submission

### Pre-Submission Checklist
- [ ] App tested on real devices
- [ ] All Info.plist privacy descriptions present
- [ ] App icons for all sizes (or single 1024×1024)
- [ ] Launch screen configured
- [ ] Accessibility tested (VoiceOver, Dynamic Type)
- [ ] Performance tested (no memory leaks, reasonable battery)
- [ ] Screenshots prepared for required device sizes
- [ ] App description, keywords, privacy policy URL

### Submission Flow
1. Archive in Xcode (Product → Archive)
2. Upload to App Store Connect
3. Fill metadata: description, screenshots, pricing, privacy
4. Submit for review
5. Review takes 24-48 hours typically
6. Approved → Release (manual or automatic)

### Common Rejection Reasons
- Crashes / bugs
- Broken links / placeholder content
- Missing privacy descriptions
- Guideline 4.3 — spam / duplicate apps
- Guideline 2.1 — app completeness
- Guideline 5.1.1 — data collection without purpose

📖 [Review Guidelines](https://developer.apple.com/app-store/review/guidelines/)

---

## Business Models

| Model | Description | Best For |
|-------|-------------|----------|
| **Free** | No charge, ad-supported or data-driven | Social, utility, enterprise |
| **Freemium** | Free with IAP for premium features | Games, productivity |
| **Paid** | One-time purchase | Premium tools, niche apps |
| **Paymium** | Paid + IAP/subscriptions | Pro tools with expansion |

### Subscription Tiers
- Auto-renewable subscriptions (most revenue)
- Apple takes 30% year 1, 15% year 2+ (Small Business Program: 15% from day 1 if <$1M revenue)
- Manage in App Store Connect → Subscriptions

📖 [Business Models](https://developer.apple.com/app-store/business-models/)

---

## Xcode Cloud

CI/CD built into Xcode and App Store Connect:
- Automatic builds on push/PR
- Run tests on Apple hardware
- Deploy to TestFlight automatically
- Included minutes with Apple Developer Program

---

## Learning Resources

### Pathways
- [Developer Pathway](https://developer.apple.com/pathways/developer/) — complete beginner path
- [SwiftUI Pathway](https://developer.apple.com/pathways/swiftui/) — UI framework deep dive
- [Games Pathway](https://developer.apple.com/games/get-started/) — game development

### WWDC
- [All Videos](https://developer.apple.com/videos/) — searchable archive
- [Design Videos](https://developer.apple.com/videos/design)
- Sessions, labs, and digital lounges

### Community
- [Apple Developer Forums](https://developer.apple.com/forums/)
- [Developer Events](https://developer.apple.com/events/) — workshops, labs, appointments
- [Apple Design Awards](https://developer.apple.com/design/awards/)
- [Swift.org Forums](https://forums.swift.org/)

### Tools
- **Xcode** — IDE
- **Swift Playgrounds** — learning + ship apps from iPad/Mac
- **Instruments** — performance profiling
- **Accessibility Inspector** — audit accessibility
- **SF Symbols** — symbol browser
- **Icon Composer** — app icon creation
- **FileMerge** — diff tool
- **Simulator** — virtual devices

---

*← [Key Frameworks](frameworks.md) · [Keyboard Extensions →](keyboard-extensions.md)*
