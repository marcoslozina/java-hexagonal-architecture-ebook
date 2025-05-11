# Hexagonal Architecture: Core Principles

Hexagonal (or Ports & Adapters) architecture centers the domain logic and surrounds it with “ports” (interfaces) and “adapters” (technical implementations).

## 🧱 Layers

- **Domain**: pure business logic
- **Application**: use case orchestration
- **Adapters**: input/output (web, DB, messaging)

## 💡 Benefits

- Low coupling
- High testability
- Easy to switch technologies

## 🖼 Basic Diagram

```plaintext
[ Web Adapter ]    [ DB Adapter ]  
       |                  |  
       |          [ Application Layer ]  
       +--------> [ Domain Layer ] <--------+
