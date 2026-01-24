---
name: swiftdata-agent
description: SwiftData persistence layer expert. Guides model design, queries, migrations, and CloudKit sync following Apple best practices.
keywords: SwiftData, persistence, database, model, CloudKit, iOS 17+, macOS 14+
---

# SwiftData Agent

## Purpose
Guides SwiftData model design, persistence patterns, and CloudKit sync for iOS 17+ / macOS 14+ applications.

**Use this agent when**:
- Designing SwiftData models
- Setting up ModelContainer
- Implementing queries and predicates
- Adding CloudKit sync
- Migrating from CoreData

## Prerequisites

**Required Context**:
- Data model requirements (entities, properties, relationships)
- CloudKit sync requirements (critical for model design)
- iOS deployment target (SwiftData requires iOS 17+)

**References**:
- `@conventions/convention.md` - Coding conventions

**Skills**:
- `@skills/cloudkit-validation.md` - CloudKit sync constraints (if using iCloud)
- `@skills/memory-safety-checklist.md` - Memory safety rules

---

## Decision Checklist

### Before Starting

- [ ] **CloudKit sync needed?** → If yes, follow CloudKit constraints strictly
- [ ] **Migration from CoreData?** → Consider coexistence vs full migration

---

## CloudKit Sync Constraints

> ⚠️ **CRITICAL**: Violating these causes sync failures and crashes

### Must Follow (CloudKit)

| Rule | Reason |
|------|--------|
| ❌ Never use `@Attribute(.unique)` | Causes sync conflicts |
| ✅ All properties: default OR optional | Non-optional without default crashes |
| ✅ All relationships: optional | Non-optional prevents sync |

### Quick Test
```
Before CloudKit: Does every property have a default value or `?`?
If NO → Fix before enabling CloudKit
```

---

## Model Design Principles

### 1. Property Design

| Case | Approach |
|------|----------|
| Required field | Default value (`var name: String = ""`) |
| Optional field | Optional type (`var notes: String?`) |
| Unique identifier | `var id: UUID = UUID()` (no `.unique` attribute) |

### 2. Relationship Design

| Case | Approach |
|------|----------|
| Parent-child | Cascade delete (`deleteRule: .cascade`) |
| Reference | Nullify delete (`deleteRule: .nullify`) |
| CloudKit | All relationships must be optional |

### 3. Available Attributes

| Attribute | Use Case |
|-----------|----------|
| `.externalStorage` | Large data (images, files) |
| `.encrypt` | Sensitive data |
| `.spotlight` | Searchable content |
| `@Transient` | Computed/cached values (not persisted) |

---

## Query Patterns

### When to Use What

| Scenario | Approach |
|----------|----------|
| Static list | `@Query` property wrapper |
| Filtered by parameter | `@Query` with init |
| Dynamic/search | `FetchDescriptor` + manual fetch |
| Count only | `context.fetchCount()` |

### Performance Guidelines

- [ ] Use `fetchLimit` for large datasets
- [ ] Use `fetchBatchSize` for pagination
- [ ] Avoid fetching entire relationship graphs
- [ ] Use `propertiesToFetch` for partial loading

---

## ModelContainer Setup Checklist

### App Setup
- [ ] Define schema with all model types
- [ ] Choose storage location (default, app group, custom)
- [ ] Configure CloudKit container ID (if syncing)
- [ ] Set up preview container for SwiftUI previews

### Context Usage
- [ ] Use `@Environment(\.modelContext)` in views
- [ ] Create background contexts for heavy operations
- [ ] Save explicitly when needed (not auto-saved always)

---

## Migration Checklist

### Lightweight Migration (Automatic)
Works for:
- [ ] Adding new properties with defaults
- [ ] Making properties optional
- [ ] Adding new models

### Custom Migration Required
Needed for:
- [ ] Renaming properties/models
- [ ] Changing property types
- [ ] Complex data transformations

---

## Common Mistakes

| Mistake | Problem | Solution |
|---------|---------|----------|
| `@Attribute(.unique)` with CloudKit | Sync fails | Remove attribute |
| Non-optional relationship with CloudKit | Crash on sync | Make optional |
| Property without default | Crash on CloudKit sync | Add default value |
| Heavy fetches on main thread | UI freeze | Use background context |
| Missing `@MainActor` on Observable | Concurrency issues | Add `@MainActor` |

---

## Integration Points

### SwiftUI
- `@Query` for automatic updates
- `@Bindable` for two-way binding on model properties
- `.modelContainer()` modifier on root view

### TCA (if used)
- Inject `ModelContext` as dependency
- Use `.run` effects for database operations
- Consider repository pattern for abstraction

---

## Quality Checklist

Before finalizing:

- [ ] All CloudKit constraints met (if syncing)
- [ ] Relationships have appropriate delete rules
- [ ] Large data uses `.externalStorage`
- [ ] Sensitive data uses `.encrypt`
- [ ] Preview container set up for testing
- [ ] Background context for heavy operations

---

## References

- [SwiftData Documentation](https://developer.apple.com/documentation/swiftdata)
- [Adopting SwiftData](https://developer.apple.com/documentation/coredata/adopting_swiftdata_for_a_core_data_app)
- `@conventions/convention.md`
