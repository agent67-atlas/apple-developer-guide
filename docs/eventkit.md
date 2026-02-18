# EventKit — Calendar & Reminders

> Programmatic access to Calendar and Reminders on all Apple platforms.

## Official Resources

- [EventKit Documentation](https://developer.apple.com/documentation/eventkit)
- [Creating Events and Reminders](https://developer.apple.com/documentation/eventkit/creating-events-and-reminders)
- [EventKitUI](https://developer.apple.com/documentation/eventkitui) — pre-built UI components

---

## Setup & Permissions

### Info.plist Keys (Required)

```xml
<key>NSCalendarsFullAccessUsageDescription</key>
<string>Access your calendar to create and manage events.</string>

<key>NSRemindersFullAccessUsageDescription</key>
<string>Access your reminders to create and manage tasks.</string>
```

### Request Access (iOS 17+ / macOS 14+)

```swift
import EventKit

let store = EKEventStore()

// Events
let granted = try await store.requestFullAccessToEvents()
guard granted else {
    throw CalendarError.accessDenied
}

// Reminders
let remindersGranted = try await store.requestFullAccessToReminders()
```

### Check Authorization Status

```swift
let status = EKEventStore.authorizationStatus(for: .event)
switch status {
case .fullAccess:
    // Good to go
case .notDetermined:
    // Request access
case .denied, .restricted:
    // Direct user to Settings
@unknown default:
    break
}
```

---

## Working with Calendars

```swift
// List all calendars
let calendars = store.calendars(for: .event)
for cal in calendars {
    print("\(cal.title) — \(cal.source.title) — \(cal.type.rawValue)")
}

// Default calendar
let defaultCal = store.defaultCalendarForNewEvents

// Find specific calendar
let workCal = calendars.first { $0.title == "Work" }
```

---

## Creating Events

### Basic Event

```swift
let event = EKEvent(eventStore: store)
event.title = "Team Standup"
event.startDate = Date()
event.endDate = Calendar.current.date(byAdding: .minute, value: 30, to: Date())!
event.calendar = store.defaultCalendarForNewEvents
event.notes = "Daily sync — review blockers and priorities"
event.location = "Zoom"

try store.save(event, span: .thisEvent)
```

### Event with Alarm

```swift
// Relative alarm — 15 minutes before
event.addAlarm(EKAlarm(relativeOffset: -900)) // seconds

// Absolute alarm
let alarmDate = Calendar.current.date(byAdding: .hour, value: -1, to: event.startDate)!
event.addAlarm(EKAlarm(absoluteDate: alarmDate))
```

### Event with Attendees

```swift
// Note: You cannot programmatically add attendees via EventKit.
// Attendees are managed by the calendar server (iCloud, Exchange, etc.)
// When you create an event on an iCloud/Exchange calendar and save it,
// the server handles invitations.
//
// For programmatic invitations, use:
// 1. AppleScript (macOS) — can add attendees to Calendar.app events
// 2. CalDAV API directly — for server-side event creation with attendees
// 3. EventKitUI — presents native UI for adding attendees
```

### All-Day Event

```swift
event.isAllDay = true
event.startDate = Calendar.current.startOfDay(for: Date())
event.endDate = Calendar.current.startOfDay(for: Date())
```

---

## Recurrence Rules

```swift
// Weekly on Monday and Wednesday
let rule = EKRecurrenceRule(
    recurrenceWith: .weekly,
    interval: 1,
    daysOfTheWeek: [
        EKRecurrenceDayOfWeek(.monday),
        EKRecurrenceDayOfWeek(.wednesday)
    ],
    daysOfTheMonth: nil,
    monthsOfTheYear: nil,
    weeksOfTheYear: nil,
    daysOfTheYear: nil,
    setPositions: nil,
    end: EKRecurrenceEnd(occurrenceCount: 52) // 1 year of weeks
)
event.addRecurrenceRule(rule)

// Daily
let daily = EKRecurrenceRule(
    recurrenceWith: .daily,
    interval: 1,
    end: nil // no end
)

// Monthly on the 15th
let monthly = EKRecurrenceRule(
    recurrenceWith: .monthly,
    interval: 1,
    daysOfTheWeek: nil,
    daysOfTheMonth: [15],
    monthsOfTheYear: nil,
    weeksOfTheYear: nil,
    daysOfTheYear: nil,
    setPositions: nil,
    end: EKRecurrenceEnd(end: endDate)
)

// Yearly
let yearly = EKRecurrenceRule(
    recurrenceWith: .yearly,
    interval: 1,
    end: nil
)
```

---

## Fetching Events

```swift
// Date range query
let startDate = Calendar.current.startOfDay(for: Date())
let endDate = Calendar.current.date(byAdding: .day, value: 7, to: startDate)!

let predicate = store.predicateForEvents(
    withStart: startDate,
    end: endDate,
    calendars: nil // nil = all calendars
)

let events = store.events(matching: predicate)
    .sorted { $0.startDate < $1.startDate }

for event in events {
    print("\(event.title ?? "No title") — \(event.startDate!)")
}
```

---

## Modifying & Deleting Events

```swift
// Update
event.title = "Updated Title"
try store.save(event, span: .thisEvent) // or .futureEvents for recurring

// Delete
try store.remove(event, span: .thisEvent)

// Span options for recurring events:
// .thisEvent — only this occurrence
// .futureEvents — this and all future occurrences
```

---

## Reminders

### Create Reminder

```swift
let reminder = EKReminder(eventStore: store)
reminder.title = "Review PR #42"
reminder.calendar = store.defaultCalendarForNewReminders()
reminder.priority = 1 // 1-4, 1 = highest

// Due date
reminder.dueDateComponents = Calendar.current.dateComponents(
    [.year, .month, .day, .hour, .minute],
    from: dueDate
)

// Alarm
reminder.addAlarm(EKAlarm(relativeOffset: 0)) // at due time

try store.save(reminder, commit: true)
```

### Fetch Reminders

```swift
let predicate = store.predicateForReminders(in: nil) // all calendars

let reminders = try await withCheckedThrowingContinuation { continuation in
    store.fetchReminders(matching: predicate) { reminders in
        if let reminders {
            continuation.resume(returning: reminders)
        } else {
            continuation.resume(throwing: CalendarError.fetchFailed)
        }
    }
}

// Filter incomplete
let incomplete = reminders.filter { !$0.isCompleted }
```

### Complete Reminder

```swift
reminder.isCompleted = true
reminder.completionDate = Date()
try store.save(reminder, commit: true)
```

---

## Observing Changes

```swift
// Listen for external changes (e.g., user edited in Calendar app)
NotificationCenter.default.addObserver(
    forName: .EKEventStoreChanged,
    object: store,
    queue: .main
) { _ in
    // Refetch events
    refreshEvents()
}
```

---

## EventKitUI — Native UI Components

When you want Apple's built-in UI:

```swift
import EventKitUI

// Event editor
struct EventEditor: UIViewControllerRepresentable {
    let store: EKEventStore
    let event: EKEvent?
    
    func makeUIViewController(context: Context) -> EKEventEditViewController {
        let controller = EKEventEditViewController()
        controller.eventStore = store
        controller.event = event
        controller.editViewDelegate = context.coordinator
        return controller
    }
    
    // ... coordinator for delegate
}

// Event detail
struct EventDetail: UIViewControllerRepresentable {
    let event: EKEvent
    
    func makeUIViewController(context: Context) -> EKEventViewController {
        let controller = EKEventViewController()
        controller.event = event
        controller.allowsEditing = true
        return controller
    }
}

// Calendar chooser
EKCalendarChooser(selectionStyle: .single, displayStyle: .allCalendars, entityType: .event, eventStore: store)
```

---

## calctl — BlocLabs CLI (BLOC-56)

We're building a Swift CLI wrapping EventKit for agent use:

```bash
calctl list --from today --to +7d
calctl create --title "Standup" --start "2026-02-19T09:00" --end "2026-02-19T09:30" --invite cris@email.com
calctl remind --title "Review PR" --due "2026-02-19T14:00"
calctl delete <eventId>
```

See Linear: [BLOC-56](https://linear.app/bloclabs/issue/BLOC-56/build-calctl-native-macos-calendar-cli-eventkit)

---

## Common Pitfalls

1. **Always request permission before accessing store** — crashes otherwise
2. **Events fetched by predicate are detached** — save changes back to store
3. **Recurring events span** — use `.thisEvent` vs `.futureEvents` carefully
4. **Calendar.current** — respects user's locale/timezone. Use it.
5. **Thread safety** — `EKEventStore` is not thread-safe. Use from main thread or serialize access.
6. **iCloud sync delay** — events may take seconds to sync across devices

---

*← [SwiftUI](swiftui.md) · [HIG →](hig.md)*
