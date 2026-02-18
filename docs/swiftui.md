# SwiftUI Reference

> Declarative UI framework for all Apple platforms. iOS 17+ / macOS 14+ patterns.

## Official Resources

- [SwiftUI Documentation](https://developer.apple.com/documentation/swiftui)
- [SwiftUI Tutorials](https://developer.apple.com/tutorials/swiftui)
- [SwiftUI Pathway](https://developer.apple.com/pathways/swiftui/)

---

## App Structure

```swift
@main
struct MyApp: App {
    @State private var store = AppStore()
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environment(store)
        }
    }
}
```

Hierarchy: **App → Scene → View**

---

## Views

Everything is a `View`. Views are structs (value types). They describe UI — SwiftUI handles rendering.

```swift
struct ProfileView: View {
    let user: User
    
    var body: some View {
        VStack(alignment: .leading, spacing: 16) {
            AsyncImage(url: user.avatarURL) { image in
                image.resizable().scaledToFill()
            } placeholder: {
                ProgressView()
            }
            .frame(width: 80, height: 80)
            .clipShape(Circle())
            
            Text(user.name)
                .font(.title2.bold())
            
            Text(user.email)
                .font(.subheadline)
                .foregroundStyle(.secondary)
        }
        .padding()
    }
}
```

### Key Principles
- Views are **cheap** — recreate freely, SwiftUI diffs efficiently
- `body` is called when state changes — keep it pure (no side effects)
- Compose small views into larger ones
- Extract subviews when `body` exceeds ~30 lines

---

## State Management

This is the most critical concept in SwiftUI.

### @State — Local View State

```swift
struct CounterView: View {
    @State private var count = 0
    
    var body: some View {
        Button("Count: \(count)") {
            count += 1
        }
    }
}
```

- Owned by the view
- Private — never pass `@State` to child views directly
- For value types only

### @Binding — Two-Way Connection

```swift
struct ToggleRow: View {
    let title: String
    @Binding var isOn: Bool
    
    var body: some View {
        Toggle(title, isOn: $isOn)
    }
}

// Parent passes binding with $
struct SettingsView: View {
    @State private var darkMode = false
    
    var body: some View {
        ToggleRow(title: "Dark Mode", isOn: $darkMode)
    }
}
```

### @Observable (iOS 17+) — Recommended

Replaces `ObservableObject`/`@Published`/`@StateObject`/`@ObservedObject`:

```swift
@Observable
class WalletStore {
    var balance: Decimal = 0
    var transactions: [Transaction] = []
    var isLoading = false
    
    func refresh() async throws {
        isLoading = true
        defer { isLoading = false }
        transactions = try await api.fetchTransactions()
        balance = transactions.reduce(0) { $0 + $1.amount }
    }
}

// In view — just use it
struct WalletView: View {
    @State private var store = WalletStore()
    
    var body: some View {
        List(store.transactions) { tx in
            TransactionRow(tx: tx)
        }
        .overlay {
            if store.isLoading { ProgressView() }
        }
        .task { try? await store.refresh() }
    }
}
```

### @Environment — Dependency Injection

```swift
// Pass down the tree
ContentView()
    .environment(store)

// Access anywhere below
struct ChildView: View {
    @Environment(WalletStore.self) var store
    
    var body: some View {
        Text("Balance: \(store.balance)")
    }
}
```

### @AppStorage — UserDefaults

```swift
@AppStorage("hasCompletedOnboarding") var onboarded = false
@AppStorage("selectedChain") var chain = "ethereum"
```

### State Management Decision Tree

```
Is it local to one view?
  → @State

Does a child need to write to it?
  → @Binding

Is it shared across many views?
  → @Observable + @Environment

Is it a simple user preference?
  → @AppStorage
```

---

## Navigation

### NavigationStack (iOS 16+)

```swift
struct AppView: View {
    @State private var path = NavigationPath()
    
    var body: some View {
        NavigationStack(path: $path) {
            ChainListView()
                .navigationDestination(for: Chain.self) { chain in
                    ChainDetailView(chain: chain)
                }
                .navigationDestination(for: Transaction.self) { tx in
                    TransactionDetailView(tx: tx)
                }
        }
    }
}

// Push programmatically
Button("View Solana") {
    path.append(Chain.solana)
}
```

### NavigationSplitView — Multi-Column

```swift
struct MainView: View {
    @State private var selectedChain: Chain?
    @State private var selectedTx: Transaction?
    
    var body: some View {
        NavigationSplitView {
            ChainSidebar(selection: $selectedChain)
        } content: {
            if let chain = selectedChain {
                TransactionList(chain: chain, selection: $selectedTx)
            }
        } detail: {
            if let tx = selectedTx {
                TransactionDetail(tx: tx)
            }
        }
    }
}
```

### Sheets & Alerts

```swift
struct ContentView: View {
    @State private var showSettings = false
    @State private var showAlert = false
    
    var body: some View {
        Button("Settings") { showSettings = true }
            .sheet(isPresented: $showSettings) {
                SettingsView()
            }
            .alert("Confirm", isPresented: $showAlert) {
                Button("Cancel", role: .cancel) {}
                Button("Delete", role: .destructive) { delete() }
            } message: {
                Text("This action cannot be undone.")
            }
    }
}
```

---

## Lists & Data Display

```swift
struct TransactionList: View {
    let transactions: [Transaction]
    
    var body: some View {
        List {
            ForEach(transactions) { tx in
                TransactionRow(tx: tx)
            }
            .onDelete(perform: delete)
        }
        .searchable(text: $searchText)
        .refreshable { await refresh() }
    }
}

// Sections
List {
    Section("Pending") {
        ForEach(pending) { tx in TransactionRow(tx: tx) }
    }
    Section("Confirmed") {
        ForEach(confirmed) { tx in TransactionRow(tx: tx) }
    }
}
```

### LazyVGrid

```swift
let columns = [GridItem(.adaptive(minimum: 150))]

ScrollView {
    LazyVGrid(columns: columns, spacing: 16) {
        ForEach(chains) { chain in
            ChainCard(chain: chain)
        }
    }
    .padding()
}
```

---

## Async Operations

```swift
struct ChainView: View {
    @State private var blocks: [Block] = []
    
    var body: some View {
        List(blocks) { block in
            BlockRow(block: block)
        }
        .task {
            // Runs when view appears, cancelled when it disappears
            blocks = try await fetchBlocks()
        }
        .task(id: selectedChain) {
            // Re-runs when selectedChain changes
            blocks = try await fetchBlocks(chain: selectedChain)
        }
    }
}
```

---

## Animations

```swift
// Implicit
Text("Hello")
    .opacity(isVisible ? 1 : 0)
    .animation(.easeInOut, value: isVisible)

// Explicit
Button("Toggle") {
    withAnimation(.spring(duration: 0.3)) {
        isExpanded.toggle()
    }
}

// Transitions
if showDetail {
    DetailView()
        .transition(.move(edge: .bottom).combined(with: .opacity))
}

// matchedGeometryEffect — hero animations
@Namespace private var animation

// In source view
Image(chain.icon)
    .matchedGeometryEffect(id: chain.id, in: animation)

// In destination view
Image(chain.icon)
    .matchedGeometryEffect(id: chain.id, in: animation)
```

---

## Common Patterns

### View Model (when needed)

```swift
@Observable
class ChainDetailViewModel {
    private let api: BlockchainAPI
    var chain: Chain
    var blocks: [Block] = []
    var error: Error?
    
    init(chain: Chain, api: BlockchainAPI = .shared) {
        self.chain = chain
        self.api = api
    }
    
    func loadBlocks() async {
        do {
            blocks = try await api.fetchBlocks(chain: chain)
        } catch {
            self.error = error
        }
    }
}
```

### Custom View Modifiers

```swift
struct CardStyle: ViewModifier {
    func body(content: Content) -> some View {
        content
            .padding()
            .background(.regularMaterial)
            .clipShape(RoundedRectangle(cornerRadius: 12))
            .shadow(radius: 2)
    }
}

extension View {
    func cardStyle() -> some View {
        modifier(CardStyle())
    }
}

// Usage
ChainCard(chain: chain)
    .cardStyle()
```

### Conditional Content

```swift
var body: some View {
    Group {
        if let user = currentUser {
            DashboardView(user: user)
        } else {
            LoginView()
        }
    }
}

// ContentUnavailableView (iOS 17+)
if transactions.isEmpty {
    ContentUnavailableView(
        "No Transactions",
        systemImage: "tray",
        description: Text("Transactions will appear here.")
    )
}
```

---

## Accessibility

**Non-negotiable.** Every view must be accessible.

```swift
Image(systemName: "arrow.up.circle.fill")
    .accessibilityLabel("Send payment")

Button(action: send) {
    Label("Send", systemImage: "paperplane")
}
// Label automatically provides accessibility

// Custom actions
.accessibilityAction(named: "Copy address") {
    copyToClipboard()
}

// Dynamic Type — use system fonts
Text("Balance")
    .font(.headline)  // ✅ Scales with Dynamic Type
// .font(.system(size: 16))  // ❌ Fixed size, doesn't scale
```

📖 [Accessibility](https://developer.apple.com/documentation/accessibility)

---

## SwiftUI + UIKit Interop

When you need UIKit:

```swift
// Wrap UIKit view
struct ActivityIndicator: UIViewRepresentable {
    var isAnimating: Bool
    
    func makeUIView(context: Context) -> UIActivityIndicatorView {
        UIActivityIndicatorView(style: .large)
    }
    
    func updateUIView(_ uiView: UIActivityIndicatorView, context: Context) {
        isAnimating ? uiView.startAnimating() : uiView.stopAnimating()
    }
}

// Wrap UIKit view controller
struct MailComposer: UIViewControllerRepresentable {
    // ...
}
```

---

## Best Practices (BlocLabs)

1. **@Observable over ObservableObject** — less boilerplate, better performance
2. **NavigationStack over NavigationView** — type-safe, programmatic
3. **`.task` over `.onAppear`** — automatic cancellation
4. **Small, composable views** — extract early, extract often
5. **Preview everything** — `#Preview` is your best friend
6. **System components first** — don't rebuild what Apple provides
7. **Accessibility from day one** — not an afterthought
8. **Dynamic Type always** — use system fonts, test with largest text
9. **Dark mode always** — use semantic colors, test both appearances
10. **No hardcoded strings** — use `String(localized:)` for all user-facing text

---

*← [Swift](swift.md) · [EventKit →](eventkit.md)*
