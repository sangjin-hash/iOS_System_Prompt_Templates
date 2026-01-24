---
name: cloudkit-validation
description: CloudKit sync validation rules for SwiftData. Critical constraints that prevent sync failures and crashes when using iCloud sync.
keywords: CloudKit, SwiftData, iCloud, sync, constraints, validation
---

# CloudKit Validation Skill

## Purpose
Critical validation rules for SwiftData models when using CloudKit sync.

**Use this skill when**:
- Designing SwiftData models with CloudKit sync
- Reviewing code that uses SwiftData + CloudKit
- Troubleshooting sync failures or crashes

---

## ⚠️ CRITICAL: CloudKit Constraints

Violating these rules causes sync failures and crashes.

### Rule 1: Never Use @Attribute(.unique)

```swift
// ❌ NEVER - Causes sync conflicts
@Model
class User {
    @Attribute(.unique) var email: String  // FORBIDDEN
}

// ✅ CORRECT - No unique constraint
@Model
class User {
    var email: String = ""  // Works with CloudKit
}
```

**Why**: CloudKit's conflict resolution doesn't handle unique constraints. Multiple devices creating records simultaneously will cause permanent sync failures.

### Rule 2: All Properties Must Have Defaults or Be Optional

```swift
// ❌ CRASH - Non-optional without default
@Model
class Item {
    var name: String  // Crashes on CloudKit sync
    var count: Int    // Crashes on CloudKit sync
}

// ✅ CORRECT - Defaults for non-optionals
@Model
class Item {
    var name: String = ""
    var count: Int = 0
}

// ✅ CORRECT - Optional properties
@Model
class Item {
    var name: String?
    var count: Int?
}
```

**Why**: CloudKit may deliver partial records. Properties without defaults or optional markers cause crashes when the value isn't present.

### Rule 3: All Relationships Must Be Optional

```swift
// ❌ CRASH - Non-optional relationship
@Model
class Order {
    var customer: Customer  // Crashes on sync
}

// ✅ CORRECT - Optional relationship
@Model
class Order {
    var customer: Customer?  // Works with CloudKit
}
```

**Why**: Related objects may not sync in order. A non-optional relationship causes crashes when the related record hasn't arrived yet.

---

## Quick Validation Checklist

Before enabling CloudKit sync:

| Check | Status |
|-------|--------|
| No `@Attribute(.unique)` on any property | ⬜ |
| All non-optional properties have default values | ⬜ |
| All relationships are optional | ⬜ |

---

## Model Validation Examples

### ❌ Invalid Model (Will Fail)

```swift
@Model
class Task {
    @Attribute(.unique) var id: UUID        // ❌ Unique
    var title: String                        // ❌ No default
    var isCompleted: Bool                    // ❌ No default
    var project: Project                     // ❌ Non-optional relationship
    var createdAt: Date                      // ❌ No default
}
```

### ✅ Valid Model (CloudKit Safe)

```swift
@Model
class Task {
    var id: UUID = UUID()                    // ✅ Default
    var title: String = ""                   // ✅ Default
    var isCompleted: Bool = false            // ✅ Default
    var project: Project?                    // ✅ Optional relationship
    var createdAt: Date = Date()             // ✅ Default
    var notes: String?                       // ✅ Optional
}
```

---

## Property Design Guide

| Property Type | CloudKit Safe Pattern |
|---------------|----------------------|
| String | `var name: String = ""` or `var name: String?` |
| Int/Double | `var count: Int = 0` or `var count: Int?` |
| Bool | `var isActive: Bool = false` |
| Date | `var createdAt: Date = Date()` or `var date: Date?` |
| UUID | `var id: UUID = UUID()` |
| Enum | `var status: Status = .pending` (with default case) |
| Array | `var items: [Item] = []` |
| Relationship | `var parent: Parent?` (always optional) |

---

## Relationship Design

### One-to-Many

```swift
// Parent
@Model
class Project {
    var name: String = ""
    @Relationship(deleteRule: .cascade, inverse: \Task.project)
    var tasks: [Task]? = []  // Optional array
}

// Child
@Model
class Task {
    var title: String = ""
    var project: Project?  // Optional reference
}
```

### Many-to-Many

```swift
@Model
class Tag {
    var name: String = ""
    @Relationship(inverse: \Item.tags)
    var items: [Item]? = []
}

@Model
class Item {
    var title: String = ""
    var tags: [Tag]? = []
}
```

---

## Troubleshooting

### Symptom: Sync Stops Working

**Check**:
1. Look for `@Attribute(.unique)` - remove it
2. Check console for CloudKit errors
3. Verify all properties have defaults

### Symptom: App Crashes on Launch After Sync

**Check**:
1. Non-optional properties without defaults
2. Non-optional relationships
3. Recently added required properties

### Symptom: Data Missing After Sync

**Check**:
1. Relationships might be nil (expected behavior)
2. Use `if let` to safely access relationships
3. Handle missing related data gracefully in UI

---

## Testing CloudKit Models

### Pre-CloudKit Checklist

```swift
func validateModel<T: PersistentModel>(_ type: T.Type) -> Bool {
    // 1. No @Attribute(.unique)
    // 2. All properties have defaults or are optional
    // 3. All relationships are optional
    return true  // Implement actual validation
}
```

### Manual Validation

Before CloudKit sync:
```
1. Can you create an instance with just `ModelName()`?
2. If NO → Add defaults to all required properties
3. Are all relationships optional?
4. If NO → Make them optional
```

---

## References

- [SwiftData + CloudKit Documentation](https://developer.apple.com/documentation/swiftdata/syncing-model-data-across-a-persons-devices)
- `@agents/localDB/swiftdata-agent.md` - SwiftData patterns
