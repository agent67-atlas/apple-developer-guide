# Key Apple Frameworks

> Essential frameworks for iOS/macOS development, with modern patterns.

---

## Foundation

Core types, networking, file system, JSON, date/time, localization.

```swift
// JSON Decoding
struct User: Codable {
    let id: UUID
    let name: String
    let email: String
    let createdAt: Date
}

let decoder = JSONDecoder()
decoder.dateDecodingStrategy = .iso8601
let user = try decoder.decode(User.self, from: data)

// Networking
let (data, response) = try await URLSession.shared.data(from: url)

// Date formatting
let formatter = Date.FormatStyle()
    .day().month(.wide).year()
    .hour().minute()
let dateString = Date().formatted(formatter)

// File Manager
let docs = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask).first!
let fileURL = docs.appending(path: "data.json")
try data.write(to: fileURL)
```

📖 [Foundation](https://developer.apple.com/documentation/foundation)

---

## SwiftData

Modern persistence framework replacing Core Data (iOS 17+).

```swift
import SwiftData

@Model
class Transaction {
    var id: UUID
    var amount: Decimal
    var chain: String
    var timestamp: Date
    var status: TransactionStatus
    
    @Relationship(deleteRule: .cascade)
    var receipts: [Receipt]
    
    init(amount: Decimal, chain: String) {
        self.id = UUID()
        self.amount = amount
        self.chain = chain
        self.timestamp = Date()
        self.status = .pending
    }
}

// Setup
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .modelContainer(for: [Transaction.self, Receipt.self])
    }
}

// Query in views
struct TransactionList: View {
    @Query(sort: \Transaction.timestamp, order: .reverse)
    var transactions: [Transaction]
    
    @Query(filter: #Predicate<Transaction> { $0.status == .pending })
    var pending: [Transaction]
    
    var body: some View {
        List(transactions) { tx in
            TransactionRow(tx: tx)
        }
    }
}

// CRUD
struct DetailView: View {
    @Environment(\.modelContext) var context
    
    func create() {
        let tx = Transaction(amount: 100, chain: "solana")
        context.insert(tx)
    }
    
    func delete(_ tx: Transaction) {
        context.delete(tx)
    }
}
```

📖 [SwiftData](https://developer.apple.com/documentation/swiftdata)

---

## CloudKit

iCloud backend — sync data across user's devices.

```swift
import CloudKit

// Save record
let record = CKRecord(recordType: "Wallet")
record["address"] = "0x1234..." as CKRecordValue
record["chain"] = "ethereum" as CKRecordValue

let container = CKContainer.default()
let database = container.privateCloudDatabase
try await database.save(record)

// Fetch
let predicate = NSPredicate(format: "chain == %@", "ethereum")
let query = CKQuery(recordType: "Wallet", predicate: predicate)
let (results, _) = try await database.records(matching: query)
```

**SwiftData + CloudKit:** SwiftData models can sync via CloudKit automatically with `ModelConfiguration(cloudKitDatabase:)`.

📖 [CloudKit](https://developer.apple.com/documentation/cloudkit)

---

## StoreKit 2

In-app purchases and subscriptions (modern API).

```swift
import StoreKit

// Fetch products
let products = try await Product.products(for: ["pro_monthly", "pro_yearly"])

// Purchase
let result = try await product.purchase()
switch result {
case .success(let verification):
    let transaction = try checkVerified(verification)
    // Deliver content
    await transaction.finish()
case .userCancelled:
    break
case .pending:
    // Awaiting approval (e.g., Ask to Buy)
    break
@unknown default:
    break
}

// Check entitlements
for await result in Transaction.currentEntitlements {
    let transaction = try checkVerified(result)
    // User has this product
}

// Subscription status
let statuses = try await product.subscription?.status ?? []
let isSubscribed = statuses.contains { $0.state == .subscribed }
```

📖 [StoreKit](https://developer.apple.com/documentation/storekit)

---

## WidgetKit

Home screen, Lock Screen, and StandBy widgets.

```swift
import WidgetKit
import SwiftUI

struct WalletWidget: Widget {
    var body: some WidgetConfiguration {
        StaticConfiguration(
            kind: "wallet_balance",
            provider: WalletProvider()
        ) { entry in
            WalletWidgetView(entry: entry)
                .containerBackground(.fill.tertiary, for: .widget)
        }
        .configurationDisplayName("Wallet Balance")
        .description("See your balance at a glance.")
        .supportedFamilies([.systemSmall, .systemMedium, .accessoryCircular])
    }
}

struct WalletEntry: TimelineEntry {
    let date: Date
    let balance: Decimal
    let chain: String
}

struct WalletProvider: TimelineProvider {
    func placeholder(in context: Context) -> WalletEntry {
        WalletEntry(date: .now, balance: 0, chain: "ethereum")
    }
    
    func getSnapshot(in context: Context, completion: @escaping (WalletEntry) -> Void) {
        completion(WalletEntry(date: .now, balance: 1234.56, chain: "ethereum"))
    }
    
    func getTimeline(in context: Context, completion: @escaping (Timeline<WalletEntry>) -> Void) {
        // Fetch real data, set refresh policy
        let entry = WalletEntry(date: .now, balance: fetchBalance(), chain: "ethereum")
        let timeline = Timeline(entries: [entry], policy: .after(.now.addingTimeInterval(900)))
        completion(timeline)
    }
}
```

📖 [WidgetKit](https://developer.apple.com/documentation/widgetkit)

---

## App Intents & Shortcuts

Expose app functionality to Siri, Shortcuts, and Spotlight.

```swift
import AppIntents

struct SendPayment: AppIntent {
    static var title: LocalizedStringResource = "Send Payment"
    static var description = IntentDescription("Send a crypto payment")
    
    @Parameter(title: "Amount")
    var amount: Decimal
    
    @Parameter(title: "Recipient")
    var recipient: String
    
    func perform() async throws -> some IntentResult {
        let receipt = try await PaymentService.send(amount: amount, to: recipient)
        return .result(dialog: "Sent \(amount) to \(recipient)")
    }
}

// Register
struct MyShortcuts: AppShortcutsProvider {
    static var appShortcuts: [AppShortcut] {
        AppShortcut(
            intent: SendPayment(),
            phrases: ["Send payment with \(.applicationName)"],
            shortTitle: "Send Payment",
            systemImageName: "paperplane"
        )
    }
}
```

📖 [App Intents](https://developer.apple.com/documentation/appintents)

---

## MapKit

Maps, annotations, directions, Look Around.

```swift
import MapKit

struct MapView: View {
    @State private var position = MapCameraPosition.region(
        MKCoordinateRegion(
            center: CLLocationCoordinate2D(latitude: 33.4484, longitude: -112.0740),
            span: MKCoordinateSpan(latitudeDelta: 0.1, longitudeDelta: 0.1)
        )
    )
    
    var body: some View {
        Map(position: $position) {
            Marker("BlocLabs HQ", coordinate: .init(latitude: 33.4484, longitude: -112.0740))
            
            ForEach(nodes) { node in
                Annotation(node.name, coordinate: node.coordinate) {
                    NodePin(node: node)
                }
            }
        }
        .mapStyle(.standard(elevation: .realistic))
    }
}
```

📖 [MapKit](https://developer.apple.com/documentation/mapkit)

---

## Core ML / Create ML

On-device machine learning.

```swift
import CoreML

// Load model
let model = try MyClassifier(configuration: MLModelConfiguration())

// Predict
let input = MyClassifierInput(features: inputData)
let prediction = try model.prediction(input: input)
print(prediction.label, prediction.labelProbability)
```

📖 [Core ML](https://developer.apple.com/documentation/coreml) · [Create ML](https://developer.apple.com/documentation/createml)

---

## AVFoundation

Audio/video capture, playback, editing.

📖 [AVFoundation](https://developer.apple.com/documentation/avfoundation)

## LocalAuthentication

Face ID / Touch ID biometric authentication.

```swift
import LocalAuthentication

let context = LAContext()
var error: NSError?

if context.canEvaluatePolicy(.deviceOwnerAuthenticationWithBiometrics, error: &error) {
    let success = try await context.evaluatePolicy(
        .deviceOwnerAuthenticationWithBiometrics,
        localizedReason: "Authenticate to send payment"
    )
}
```

📖 [LocalAuthentication](https://developer.apple.com/documentation/localauthentication)

## Security Framework

Keychain access, cryptographic operations, secure enclave.

📖 [Security](https://developer.apple.com/documentation/security)

## Network Framework

Low-level networking — TCP/UDP/QUIC connections, path monitoring.

📖 [Network](https://developer.apple.com/documentation/network)

---

## Framework Decision Guide

| Need | Framework |
|------|-----------|
| UI | SwiftUI (default) or UIKit (complex custom views) |
| Persistence | SwiftData (iOS 17+) or Core Data (legacy) |
| Cloud sync | CloudKit + SwiftData |
| Payments | StoreKit 2 |
| Widgets | WidgetKit |
| Siri/Shortcuts | App Intents |
| Maps | MapKit |
| Camera/Audio | AVFoundation |
| ML on-device | Core ML |
| Auth/biometrics | LocalAuthentication |
| Secure storage | Security (Keychain) |
| Calendar | EventKit |
| Networking | URLSession (high-level) or Network (low-level) |

---

*← [Design Resources](design-resources.md) · [Developer Program →](developer-program.md)*
