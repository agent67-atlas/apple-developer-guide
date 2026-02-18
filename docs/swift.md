# Swift Language Reference

> Modern Swift fundamentals for Apple platform development. Swift 5.9+ / Swift 6.

## Overview

Swift is Apple's primary language — safe, fast, expressive. Open source since 2015. Designed for safety (type system, optionals, ARC) and performance (value types, copy-on-write, zero-cost abstractions).

## Official Resources

- [The Swift Programming Language Guide](https://docs.swift.org/swift-book/documentation/the-swift-programming-language) — definitive reference
- [A Swift Tour](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/guidedtour) — quick overview
- [Swift.org](https://www.swift.org/) — open source community
- [WWDC 2024: A Swift Tour](https://developer.apple.com/videos/play/wwdc2024/10184/)
- [Swift Pathway](https://developer.apple.com/swift/get-started/)

---

## The Basics

### Constants & Variables

```swift
let constant = "immutable"     // prefer let
var variable = "mutable"       // use var only when needed
```

**Rule:** Default to `let`. Use `var` only when mutation is required.

📖 [The Basics](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics)

### Type Safety & Inference

```swift
let name = "BlocLabs"          // inferred as String
let count: Int = 42            // explicit type annotation
let pi: Double = 3.14159
```

Swift catches type errors at compile time, not runtime.

📖 [Type Safety](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics#Type-Safety-and-Type-Inference)

### Optionals

The core of Swift's null safety:

```swift
var email: String? = nil       // might have a value, might be nil

// Safe unwrapping
if let email = email {
    print(email)               // only runs if email has a value
}

// Guard (early exit)
guard let email = email else {
    return                     // exit if nil
}
print(email)                   // guaranteed non-nil here

// Nil coalescing
let display = email ?? "No email"

// Optional chaining
let count = email?.count       // returns Int? — nil if email is nil
```

**Rule:** Never force-unwrap (`!`) unless you can prove it's safe. Prefer `guard let` for early exits, `if let` for branching.

---

## Collection Types

```swift
// Array — ordered, duplicates allowed
var names: [String] = ["Cris", "Maya", "ATLAS"]
names.append("Newton")
names[0]                       // "Cris"

// Dictionary — key-value pairs
var config: [String: Any] = ["port": 3000, "host": "localhost"]
config["port"]                 // Optional(3000)

// Set — unordered, unique values
var tags: Set<String> = ["swift", "ios", "swiftui"]
tags.insert("swift")           // no-op, already exists
```

📖 [Collection Types](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes)

---

## Control Flow

```swift
// Pattern matching with switch
switch statusCode {
case 200:
    print("OK")
case 400..<500:
    print("Client error")
case 500...:
    print("Server error")
default:
    print("Unknown")
}

// For-in with where clause
for user in users where user.isActive {
    print(user.name)
}

// Guard — early exit pattern
func process(_ data: Data?) throws {
    guard let data = data else {
        throw ProcessError.noData
    }
    // data is guaranteed non-nil here
}
```

📖 [Control Flow](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow)

---

## Functions & Closures

```swift
// Named parameters (Swift convention)
func greet(person name: String, from city: String) -> String {
    return "Hello \(name) from \(city)"
}
greet(person: "Cris", from: "Phoenix")

// Closures
let sorted = names.sorted { $0 < $1 }

// Trailing closure syntax
UIView.animate(withDuration: 0.3) {
    view.alpha = 0
}
```

📖 [Functions](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions) · [Closures](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/closures)

---

## Structures vs Classes

| Feature | Struct | Class |
|---------|--------|-------|
| Type | Value | Reference |
| Inheritance | ❌ | ✅ |
| ARC | ❌ | ✅ |
| Mutability | `mutating` keyword | Mutable by default |
| Thread safety | Inherently safe (copies) | Needs synchronization |

```swift
// Struct — default choice
struct Transaction {
    let id: UUID
    var amount: Decimal
    var status: Status
}

// Class — when you need identity, inheritance, or Objective-C interop
class NetworkManager {
    static let shared = NetworkManager()
    private init() {}
}
```

**Rule:** Default to structs. Use classes only for identity semantics, inheritance, or Objective-C bridging.

📖 [Choosing Between Structures and Classes](https://developer.apple.com/documentation/swift/choosing-between-structures-and-classes) · [Value vs Reference Types](https://www.swift.org/documentation/articles/value-and-reference-types.html)

---

## Protocols & Extensions

Protocol-oriented programming is Swift's paradigm:

```swift
protocol Payable {
    var amount: Decimal { get }
    func process() async throws -> Receipt
}

// Default implementation via extension
extension Payable {
    func formattedAmount() -> String {
        return "$\(amount)"
    }
}

// Conformance
struct CryptoPayment: Payable {
    let amount: Decimal
    let chain: Chain
    
    func process() async throws -> Receipt {
        // implementation
    }
}
```

📖 [Protocols](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/protocols) · [Extensions](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/extensions)

---

## Enumerations

Swift enums are first-class types with associated values:

```swift
enum NetworkError: Error {
    case noConnection
    case timeout(seconds: Int)
    case httpError(statusCode: Int, message: String)
}

// Pattern matching
switch error {
case .noConnection:
    retry()
case .timeout(let seconds) where seconds > 30:
    showAlert("Request took too long")
case .httpError(let code, _) where code == 401:
    refreshToken()
default:
    log(error)
}
```

📖 [Enumerations](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/enumerations)

---

## Generics

```swift
func fetch<T: Decodable>(from url: URL) async throws -> T {
    let (data, _) = try await URLSession.shared.data(from: url)
    return try JSONDecoder().decode(T.self, from: data)
}

// Usage — type inferred from context
let user: User = try await fetch(from: userURL)
let transactions: [Transaction] = try await fetch(from: txURL)
```

📖 [Generics](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/generics)

---

## Concurrency (async/await)

Modern structured concurrency (Swift 5.5+):

```swift
// Async function
func fetchUser(id: String) async throws -> User {
    let (data, response) = try await URLSession.shared.data(from: url)
    guard let http = response as? HTTPURLResponse, http.statusCode == 200 else {
        throw APIError.invalidResponse
    }
    return try JSONDecoder().decode(User.self, from: data)
}

// Parallel execution
async let user = fetchUser(id: "123")
async let transactions = fetchTransactions(userId: "123")
let (u, t) = try await (user, transactions)

// Task groups
let results = try await withThrowingTaskGroup(of: ChainData.self) { group in
    for chain in chains {
        group.addTask { try await indexChain(chain) }
    }
    return try await group.reduce(into: []) { $0.append($1) }
}

// Actor — thread-safe state
actor WalletManager {
    private var balance: Decimal = 0
    
    func deposit(_ amount: Decimal) {
        balance += amount
    }
    
    func getBalance() -> Decimal {
        return balance
    }
}
```

📖 [Concurrency](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/concurrency)

---

## Error Handling

```swift
enum IndexerError: LocalizedError {
    case chainNotSupported(String)
    case rpcFailed(chain: String, message: String)
    case decodingFailed
    
    var errorDescription: String? {
        switch self {
        case .chainNotSupported(let chain):
            return "Chain '\(chain)' is not supported"
        case .rpcFailed(let chain, let message):
            return "RPC failed for \(chain): \(message)"
        case .decodingFailed:
            return "Failed to decode response"
        }
    }
}

// Throwing & catching
do {
    let block = try await fetchBlock(chain: .solana, number: 12345)
} catch let error as IndexerError {
    logger.error("Indexer error: \(error.localizedDescription)")
} catch {
    logger.error("Unexpected: \(error)")
}

// Result type for callbacks
func validate(_ tx: Transaction) -> Result<Receipt, ValidationError> {
    guard tx.amount > 0 else {
        return .failure(.invalidAmount)
    }
    return .success(Receipt(tx: tx))
}
```

📖 [Error Handling](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/errorhandling)

---

## Property Wrappers

```swift
// Built-in (SwiftUI)
@State private var count = 0
@Binding var isPresented: Bool
@Environment(\.dismiss) var dismiss
@AppStorage("theme") var theme: String = "system"

// Custom property wrapper
@propertyWrapper
struct Clamped<Value: Comparable> {
    private var value: Value
    let range: ClosedRange<Value>
    
    var wrappedValue: Value {
        get { value }
        set { value = min(max(newValue, range.lowerBound), range.upperBound) }
    }
    
    init(wrappedValue: Value, _ range: ClosedRange<Value>) {
        self.range = range
        self.value = min(max(wrappedValue, range.lowerBound), range.upperBound)
    }
}

// Usage
@Clamped(0...100) var volume: Int = 50
```

📖 [Properties](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties)

---

## Macros (Swift 5.9+)

```swift
// Observable macro — replaces ObservableObject pattern
@Observable
class UserStore {
    var currentUser: User?
    var isLoading = false
}

// Preview macro
#Preview {
    ContentView()
        .environment(UserStore())
}

// Swift Testing
@Test func transactionProcessing() async throws {
    let tx = Transaction(amount: 100)
    let receipt = try await tx.process()
    #expect(receipt.status == .confirmed)
}
```

---

## Memory Management (ARC)

```swift
class Parent {
    var child: Child?
}

class Child {
    weak var parent: Parent?    // weak to prevent retain cycle
}

// Closure capture lists
class ViewModel {
    func loadData() {
        Task { [weak self] in
            guard let self else { return }
            self.data = try await fetchData()
        }
    }
}
```

**Rules:**
- Use `weak` for delegate references and parent back-references
- Use `[weak self]` in closures that outlive the object
- Use `unowned` only when you guarantee the reference outlives the closure

📖 [ARC](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting)

---

## Swift Style Guide (BlocLabs)

1. **Naming:** camelCase for everything. Types start uppercase. Functions/properties start lowercase.
2. **Access Control:** Default to `private`. Expose only what's needed.
3. **Immutability:** `let` over `var`. Always.
4. **Optionals:** Never force-unwrap. `guard let` for early exits.
5. **Structs over classes** unless you need reference semantics.
6. **Protocol conformance** in extensions for organization.
7. **One type per file** for public types.
8. **Error handling** is the main code path, not an afterthought.

---

*Next: [SwiftUI →](swiftui.md)*
