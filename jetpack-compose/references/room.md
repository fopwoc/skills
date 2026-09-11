# Data flow

For persistent backend data used by UI, prefer a local-first reactive flow:

`ViewModel → Network → Room Repository → Room Observer → Route → View`

Room is the source of truth for persistent backend data.

- ViewModel initiates network operations. Successful persistent results are written through the Room repository.
- Do not copy Room-backed data into the screen `Model`. Keep persisted data and ViewModel-owned UI state as separate state channels.
- ViewModel owns dedicated Room observers for the persisted data required by the screen. A screen may have one or several observers.
- Room observers may be parameterized when the observed query depends on current screen state or arguments.
- Each observer exposes a subscribable value backed by a repository `Flow`.
- Route subscribes to the screen `Model` and Room observers independently, then passes their current values into View.
- View receives plain prepared values and remains unaware of Room, repositories, and network access.
- Repositories expose convenient, purpose-specific `Flow`s rather than requiring ViewModel or UI code to assemble raw database state.
- Do not persist inherently transient data merely to satisfy this pattern.

# Room

Use a consistent structure for persisted types:

`DatabaseInterface → DatabaseModel / DatabaseEntity → Dao → Repository`

## Models

- Define a small `*DatabaseInterface` containing the value shared by database representations.
- `*DatabaseModel` represents the database-facing value outside Room.
- `*DatabaseEntity` is the Room entity and implements the same interface.
- Keep explicit conversion between DatabaseModel and DatabaseEntity.
- Use `kotlin.uuid.Uuid` for UUID identifiers.
- Keep the Room table name as `TABLE_NAME` on the entity.

## DAO

Keep DAOs focused on database operations.

Provide only operations required by the domain, commonly:

- singular and collection reads;
- `Flow` variants for observable reads;
- insert/update with `OnConflictStrategy.REPLACE`;
- singular and bulk deletion.

Prefer Room `Flow` queries for observable data rather than manually invalidating or refreshing cached state.

## Repository

Hide DAOs behind a repository.

- Repository APIs expose domain values, not Room entities.
- Convert entities at the repository boundary.
- Expose convenient typed `Flow`s already mapped into values useful to consumers.
- Keep Room-specific details inside the database layer.
- Treat a missing value as an explicit domain/database condition rather than leaking nullable Room results when absence is exceptional.
- Log meaningful database reads, writes, deletes, and Flow updates at debug/verbose level.

## Complex values

For complex serializable values stored as a single database field:

- Prefer a typed Room converter backed by CBOR serialization.
- Keep converters generic/reusable where practical.
- Reference actual Kotlin types rather than hardcoded type names.
