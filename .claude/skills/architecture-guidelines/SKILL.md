---
name: architecture-guidelines
description: "Mobile architecture guidelines for the team — MVVM, repository pattern, navigation, and dependency injection. Load when reviewing a diff for architecture decisions."
---

## Guidelines

- **MVVM** — Screen logic lives in a `ViewModel`. Views observe state and dispatch events
  only; Views contain no business logic or data access.
- **Repository pattern** — ViewModels obtain data through a `Repository` only. A ViewModel
  that calls an API client, network service, or DAO directly bypasses the repository layer.
- **Navigation** — Navigation goes through the Coordinator (iOS) or NavController (Android).
  Screens do not navigate directly to one another.
- **Dependency injection** — Dependencies are injected (Hilt on Android, a custom DI
  container on iOS). The ServiceLocator pattern is not allowed.

When citing a violation, name the guideline (for example "violates the Repository pattern").
The canonical decision records live in `docs/adr/`.
