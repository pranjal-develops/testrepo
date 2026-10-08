## PrSummaryRepository Architectural Design Document

### Overview
PR #26 introduces `PrSummaryRepository`, a new Spring Data JPA repository interface extending `JpaRepository` to provide typed data access for the `PrSummary` entity. It adds four new query methods—`findByModuleAndProcessedFalse`, `countByModuleAndProcessedFalse`, `findByDeliveryId`, and `existsByDeliveryId`—enabling module-scoped unprocessed summary retrieval and delivery ID deduplication logic.

### System Architecture
The repository adheres to the Spring Data JPA repository pattern, serving as the typed data access layer between the application/service layer and the persistence layer. `PrSummaryRepository` extends `JpaRepository<PrSummary, Long>`, inheriting standard CRUD operations while declaring four custom query methods that Spring Data automatically translates into parameterized SQL queries. This design enables efficient module-scoped filtering of unprocessed records and dedup logic based on delivery IDs without writing native query implementations.

### Endpoints
The repository methods are designed to be consumed through the following REST endpoints, typically exposed via a service layer:
- `GET /pr-summaries?module={module}&processed=false` → invokes `findByModuleAndProcessedFalse`
- `GET /pr-summaries/count?module={module}&processed=false` → invokes `countByModuleAndProcessedFalse`
- `GET /pr-summaries/delivery/{deliveryId}` → invokes `findByDeliveryId`
- `GET /pr-summaries/exists?deliveryId={id}` → invokes `existsByDeliveryId`
These endpoints facilitate retrieval of unprocessed summaries per module and validation of delivery ID uniqueness.

### Data Model
The `PrSummary` entity comprises the following attributes, inferred from the repository's query method signatures:
- `id` (Long, primary key)
- `module` (String/UUID) — used by `findByModuleAndProcessedFalse` and `countByModuleAndProcessedFalse`
- `processed` (Boolean) — filter flag for the above methods
- `deliveryId` (String/Long) — used by `findByDeliveryId` and `existsByDeliveryId`
The entity may relate to a `Delivery` domain model via `deliveryId`, though the change log does not specify the exact mapping strategy.

### Dependencies
`PrSummaryRepository` relies on the following components, as dictated by its Spring Data JPA implementation:
- `spring-data-jpa` library
- Spring Boot `spring-boot-starter-data-jpa`
- The `PrSummary` entity class
- A relational database JDBC driver (e.g., PostgreSQL, H2, or MySQL)
- `JpaRepository` base interface from `org.springframework.data.jpa.repository`