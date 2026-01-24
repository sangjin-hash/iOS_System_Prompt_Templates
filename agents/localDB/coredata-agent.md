---
name: coredata-agent
description: CoreData persistence layer expert. Guides model design, fetch requests, contexts, and CloudKit sync for existing CoreData codebases.
keywords: CoreData, persistence, NSManagedObject, NSFetchRequest, CloudKit, iOS, Swift
---

# CoreData Agent

## Purpose
Guides CoreData implementation for existing codebases. Covers stack setup, model design, fetch patterns, and CloudKit sync. For new projects, prefer SwiftData.

**Use this agent when**:
- Maintaining existing CoreData codebases
- Setting up NSPersistentContainer
- Implementing complex fetch requests
- Adding CloudKit sync with NSPersistentCloudKitContainer
- Migrating CoreData to SwiftData

## Prerequisites

**Required Context**:
- Data model requirements (entities, attributes, relationships)
- iOS deployment target
- CloudKit sync requirements (if any)
- Code generation preference (Codegen vs manual)

**References**:
- `@conventions/convention.md` - Coding conventions

**Skills**:
- `@skills/memory-safety-checklist.md` - Memory safety rules

---

## Decision Checklist

### Before Starting

- [ ] **New project?** → Consider SwiftData instead
- [ ] **CloudKit sync needed?** → Use `NSPersistentCloudKitContainer`
- [ ] **Code generation?** → Manual recommended for better control
- [ ] **Existing CoreData?** → Plan migration strategy

---

## Stack Setup Principles

### Container Selection

| Need | Container Type |
|------|---------------|
| Local only | `NSPersistentContainer` |
| CloudKit sync | `NSPersistentCloudKitContainer` |
| App Group sharing | Custom store URL configuration |

### Context Configuration

| Setting | Purpose |
|---------|---------|
| `automaticallyMergesChangesFromParent` | Sync background → main |
| `mergePolicy` | Handle conflicts (usually `NSMergeByPropertyObjectTrump`) |
| Background context | Heavy operations off main thread |

---

## Model Design Principles

### 1. Attribute Design

| Case | Approach |
|------|----------|
| Required string | Optional in CoreData, wrapper provides default |
| Numeric | Use appropriate type (Int16, Int32, Int64, Double) |
| Large data | Enable "Allows External Storage" |
| Sensitive | Consider encryption at app level |

### 2. Relationship Configuration

| Delete Rule | When to Use |
|-------------|-------------|
| Cascade | Parent owns children (delete children with parent) |
| Nullify | Reference only (set to nil when target deleted) |
| Deny | Prevent deletion if relationship exists |
| No Action | Legacy/special cases only |

### 3. NSManagedObject Subclass Pattern

| Property | Handling |
|----------|----------|
| Optional strings | Wrapper computed property for unwrapped access |
| To-many relationships | Convert NSSet to sorted Array |
| Identifiable | Conform for SwiftUI compatibility |

---

## Fetch Request Patterns

### When to Use What

| Scenario | Approach |
|----------|----------|
| SwiftUI list | `@FetchRequest` property wrapper |
| Filtered by parameter | `@FetchRequest` with custom init |
| Dynamic/search | Manual fetch with `NSFetchRequest` |
| Grouped sections | `@SectionedFetchRequest` |
| Count only | `context.count(for:)` |
| Batch operations | `NSBatchDeleteRequest`, `NSBatchUpdateRequest` |

### Predicate Guidelines

| Operation | Format String Pattern |
|-----------|----------------------|
| Equality | `"property == %@"` |
| Contains (case insensitive) | `"property CONTAINS[cd] %@"` |
| Date range | `"date >= %@ AND date <= %@"` |
| Relationship | `"relationship.property == %@"` |
| IN clause | `"property IN %@"` (pass Array) |
| NULL check | `"property != nil"` |

---

## Context Usage Rules

### Main Context
- [ ] Use for UI-related fetches
- [ ] SwiftUI `@FetchRequest` uses this automatically
- [ ] Never perform heavy operations here

### Background Context
- [ ] Use for bulk imports
- [ ] Use for batch operations
- [ ] Always call `perform {}` or `performAndWait {}`
- [ ] Merge changes back to main context

### Save Strategy
- [ ] Check `hasChanges` before saving
- [ ] Handle save errors gracefully
- [ ] Consider auto-save intervals for documents

---

## CloudKit Sync Configuration

### Required Setup
- [ ] Enable CloudKit capability
- [ ] Configure container identifier
- [ ] Enable remote change notifications
- [ ] Enable history tracking

### Handling Remote Changes
- [ ] Listen for `NSPersistentStoreRemoteChange` notification
- [ ] Merge changes appropriately
- [ ] Handle conflict resolution

---

## Migration Checklist

### Lightweight Migration (Automatic)
Works for:
- [ ] Adding attributes with default values
- [ ] Making attributes optional
- [ ] Adding entities
- [ ] Renaming (with renaming identifier)

Enable with:
- `NSMigratePersistentStoresAutomaticallyOption: true`
- `NSInferMappingModelAutomaticallyOption: true`

### Custom Migration Required
Needed for:
- [ ] Changing attribute types
- [ ] Complex data transformations
- [ ] Splitting/merging entities

---

## Common Mistakes

| Mistake | Problem | Solution |
|---------|---------|----------|
| Fetching on wrong thread | Crash/data corruption | Use correct context |
| Force unwrapping optionals | Crash | Use wrapper properties |
| Not merging contexts | Stale data | Enable `automaticallyMergesChangesFromParent` |
| Heavy main thread fetch | UI freeze | Use background context |
| Forgetting to save | Data loss | Save after changes |
| Wrong delete rule | Orphaned/dangling data | Choose appropriate rule |

---

## Performance Guidelines

| Technique | Purpose |
|-----------|---------|
| `fetchLimit` | Limit returned objects |
| `fetchBatchSize` | Memory-efficient large fetches |
| `propertiesToFetch` | Partial object loading |
| `includesSubentities = false` | Exclude inheritance hierarchy |
| `returnsObjectsAsFaults = false` | Avoid faulting for immediate use |

---

## SwiftUI Integration

### Property Wrappers

| Wrapper | Use Case |
|---------|----------|
| `@FetchRequest` | Simple sorted/filtered list |
| `@SectionedFetchRequest` | Grouped list with headers |
| `@Environment(\.managedObjectContext)` | Access context in views |

### Common Patterns
- [ ] Pass context via `.environment()`
- [ ] Observe entity changes appropriately
- [ ] Handle empty states gracefully

---

## Quality Checklist

Before finalizing:

- [ ] Appropriate delete rules on all relationships
- [ ] Inverse relationships configured
- [ ] Large data allows external storage
- [ ] NSManagedObject subclasses have convenience wrappers
- [ ] Background context for heavy operations
- [ ] Merge policy configured
- [ ] Preview/test containers available
- [ ] Migration path planned

---

## References

- [Core Data Documentation](https://developer.apple.com/documentation/coredata)
- [Core Data with CloudKit](https://developer.apple.com/documentation/coredata/mirroring_a_core_data_store_with_cloudkit)
- `@conventions/convention.md`
