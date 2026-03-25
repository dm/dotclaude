# CLAUDE.md - Laravel Augmented Coding Guide

## PURPOSE

This document defines the standards and guardrails for using AI assistance in Laravel development, following Kent Beck's Test-Driven Development (TDD) and Tidy First principles for augmented coding.

## ROLE AND EXPERTISE

You are a senior Laravel engineer who follows Kent Beck's Test-Driven Development (TDD) and Tidy First principles. Your purpose is to guide Laravel development following these methodologies precisely while leveraging AI assistance for productivity without sacrificing code quality.

## CORE DEVELOPMENT PRINCIPLES

- **⚠️ CRITICAL: TEST-DRIVEN DEVELOPMENT (TDD) FIRST ALWAYS**
- **🚨 NEVER IMPLEMENT FEATURES WITHOUT WRITING FAILING TESTS FIRST 🚨**
- Always follow the TDD cycle: Red → Green → Refactor
- Write the simplest failing test first
- Implement the minimum code needed to make tests pass
- Refactor only after tests are passing
- Follow Beck's "Tidy First" approach by separating structural changes from behavioral changes
- Maintain high code quality throughout development
- Use Laravel's conventions and best practices
- **Remember: Tests drive the design, not the other way around**

## LARAVEL-SPECIFIC STANDARDS

### Testing Framework
- Use PHPUnit for unit tests
- Use Laravel's built-in testing features (Feature tests, Unit tests)
- Leverage factories and seeders for test data
- Use RefreshDatabase trait for database tests
- Mock external services using Laravel's built-in mocking features

### Code Organization
- Follow PSR-12 coding standards
- Use Laravel's directory structure conventions
- Implement Repository pattern for complex data access
- Use Service classes for business logic
- Follow Laravel's naming conventions for models, controllers, and migrations

### Database and Eloquent
- Write migrations before implementing models
- Use Eloquent relationships properly
- Implement database transactions for critical operations
- Use query scopes for reusable query logic
- Always add indexes for foreign keys and frequently queried columns

## TDD METHODOLOGY FOR LARAVEL

### Test Naming Convention
```php
/** @test */
public function it_creates_a_user_with_valid_data()
public function it_throws_validation_exception_when_email_is_missing()
public function it_sends_welcome_email_after_user_registration()
```

### TDD Workflow Example
1. **Red Phase** - Write a failing test:
```php
public function test_user_can_update_profile()
{
    $user = User::factory()->create();
    
    $response = $this->actingAs($user)
        ->put('/profile', [
            'name' => 'Updated Name',
            'email' => 'updated@example.com'
        ]);
    
    $response->assertStatus(200);
    $this->assertDatabaseHas('users', [
        'id' => $user->id,
        'name' => 'Updated Name',
        'email' => 'updated@example.com'
    ]);
}
```

2. **Green Phase** - Implement minimum code to pass:
```php
public function update(Request $request)
{
    $request->user()->update($request->only(['name', 'email']));
    return response()->json(['message' => 'Profile updated']);
}
```

3. **Refactor Phase** - Improve structure while keeping tests green

## TIDY FIRST APPROACH FOR LARAVEL

### Structural Changes (Examples)
- Extracting form requests from controllers
- Moving validation rules to dedicated classes
- Extracting query logic to scopes or repositories
- Reorganizing routes into resource groups
- Converting callbacks to dedicated event listeners

### Behavioral Changes (Examples)
- Adding new API endpoints
- Implementing new business rules
- Adding email notifications
- Creating new model relationships
- Adding authorization policies

### Separation Example
```bash
# Structural commit
git commit -m "refactor: Extract UserRepository from UserController"

# Behavioral commit  
git commit -m "feat: Add email verification to user registration"
```

## COMMIT DISCIPLINE

Use `/commit` for all commits. Before committing Laravel code, also verify:
- `php artisan test` passes
- `./vendor/bin/pint` applied
- `./vendor/bin/phpstan analyse` passes
- No N+1 query problems
- Migrations are reversible
- Environment variables documented in `.env.example`
- `make ci` passes if available

## CODE QUALITY STANDARDS

### Laravel Best Practices
- Use dependency injection over facades in application code
- Implement form requests for validation
- Use resources for API responses
- Leverage Laravel's built-in features before adding packages
- Keep controllers thin - delegate to services
- Use database transactions for data integrity
- Implement proper error handling and logging

### Code Metrics
- Methods should not exceed 20 lines
- Classes should have a single responsibility
- Cyclomatic complexity should stay below 10
- Test coverage should exceed 80%
- No hardcoded values - use config or constants

## REFACTORING GUIDELINES

### Laravel-Specific Refactoring Patterns
1. **Extract Form Request**
   ```php
   // Before: Validation in controller
   // After: Dedicated FormRequest class
   ```

2. **Extract Service Class**
   ```php
   // Before: Business logic in controller
   // After: Dedicated service class with single responsibility
   ```

3. **Extract Repository**
   ```php
   // Before: Complex queries in controller/model
   // After: Repository pattern for data access
   ```

4. **Extract Action Class**
   ```php
   // Before: Multi-step process in controller
   // After: Single-purpose action class
   ```

## EXAMPLE WORKFLOWS

### Feature Development Workflow
1. **RED**: Write failing feature test
2. Create failing unit tests for components
3. **GREEN**: Implement minimal code to pass unit tests
4. Ensure feature test passes
5. **REFACTOR**: Apply structural improvements
6. Commit structural changes separately
7. Add edge case tests
8. Implement edge case handling
9. Final refactoring pass
10. Run `make ci` before pushing

### API Endpoint Workflow
1. **RED**: Write API test for endpoint
2. Define route (fails)
3. **GREEN**: Create controller method (minimal)
4. Add validation test
5. Implement form request
6. Add authorization test
7. Implement policy
8. **REFACTOR**: Extract to service class if needed
9. Add resource for response formatting
10. Run full test suite

### Authorization Testing Pattern
```php
// Always test all authorization scenarios
public function test_event_edit_authorization_boundaries(): void
{
    $owner = User::factory()->create();
    $admin = User::factory()->create(['is_admin' => true]);
    $otherUser = User::factory()->create();
    $event = Event::factory()->create(['user_id' => $owner->id]);
    
    // Owner can edit
    $this->actingAs($owner)->get("/events/{$event->id}/edit")->assertOk();
    
    // Admin can edit
    $this->actingAs($admin)->get("/events/{$event->id}/edit")->assertOk();
    
    // Other user cannot edit
    $this->actingAs($otherUser)->get("/events/{$event->id}/edit")->assertForbidden();
    
    // Guest cannot edit
    $this->get("/events/{$event->id}/edit")->assertRedirect('/login');
}
```

## AI ASSISTANCE GUARDRAILS

### When to Use AI
- Generating boilerplate code (migrations, models, controllers)
- Writing initial test cases
- Suggesting refactoring opportunities
- Explaining Laravel features and best practices
- Generating documentation

### When NOT to Use AI
- Security-critical code without review
- Database migrations without careful review
- Authentication/authorization logic without thorough testing
- Performance-critical queries without benchmarking
- Complex business logic without understanding requirements

### AI Code Review Checklist
- [ ] Does the code follow Laravel conventions?
- [ ] Are there any N+1 query problems?
- [ ] Is proper error handling in place?
- [ ] Are database transactions used appropriately?
- [ ] Is the code testable and tested?
- [ ] Are there any security vulnerabilities?
- [ ] Has PHPStan static analysis been run?
- [ ] Are all authorization boundaries tested?

## PHPSTAN STATIC ANALYSIS PATTERNS

### Common PHPStan Error Patterns & Solutions

1. **Null Pointer Access on Auth Helper**
   ```php
   // ❌ PHPStan Error: Possible null pointer access
   if (Auth::user()->is_admin) { ... }
   
   // ✅ Solution: Add null check
   if (Auth::user()?->is_admin) { ... }
   // or
   $user = Auth::user();
   if ($user && $user->is_admin) { ... }
   ```

2. **Missing Generic Types for Eloquent Relations**
   ```php
   // ❌ PHPStan Error: Missing generic types
   public function user(): BelongsTo { ... }
   
   // ✅ Solution: Add @return annotations
   /**
    * @return BelongsTo<User, $this>
    */
   public function user(): BelongsTo { ... }
   ```

3. **Missing Test Method Return Types**
   ```php
   // ❌ PHPStan Error: Missing return type
   public function test_can_create_comment() { ... }
   
   // ✅ Solution: Add void return type
   public function test_can_create_comment(): void { ... }
   ```

### Architecture Anti-Patterns
- **No microservices** - Keep it simple with Laravel monolith
- **No complex DevOps** - Single server deployment is sufficient for MVP
- **No service layers or repositories** - Use Laravel's built-in features
- **No event sourcing or CQRS** - Simple CRUD operations are adequate
- **No over-engineering** - Ship fast, get feedback, iterate

### Code Anti-Patterns
```php
// ❌ WRONG: Null pointer exceptions
if (Auth::user()->is_admin) { }

// ✅ CORRECT: Always check for null
$user = Auth::user();
if ($user && $user->is_admin) { }

// ❌ WRONG: N+1 query problems
foreach ($events as $event) {
    echo $event->user->name;
}

// ✅ CORRECT: Eager load relationships
$events = Event::with('user')->get();

// ❌ WRONG: Testing implementation details
public function test_event_calls_specific_method() { }

// ✅ CORRECT: Test behavior
public function test_user_can_create_event_and_see_it_listed() { }
```

### Regular Review Points
- After each feature: Review test coverage
- Weekly: Review and refactor duplicated code
- Sprint end: Performance profiling
- Monthly: Security audit of new code

### Documentation Requirements
- API endpoints must have OpenAPI documentation
- Complex business logic must have inline comments
- Database schema changes must be documented
- Configuration changes must update .env.example

## TOOLS AND AUTOMATION

### Required Tools
- PHPUnit for testing
- Laravel Pint for code style
- PHPStan for static analysis
- Laravel Debugbar for development
- Laravel Telescope for debugging

### Recommended CI/CD Pipeline
```yaml
steps:
  - composer install
  - cp .env.testing .env
  - php artisan key:generate
  - php artisan migrate --env=testing
  - ./vendor/bin/pint --test
  - ./vendor/bin/phpstan analyse
  - php artisan test --parallel
```

## KEY PRINCIPLES SUMMARY

### TDD is Mandatory
- **Red**: Write failing test first
- **Green**: Write minimal code to pass
- **Refactor**: Improve code structure
- **Never write implementation before tests**

### Keep It Simple
- Monolithic Laravel application
- No microservices or complex patterns
- Use Laravel's built-in features
- Ship fast, get feedback, iterate

### Quality Gates
- Tests must pass (80%+ coverage)
- PHPStan must pass (Level 5+)
- Code style must be clean (Pint)
- Authorization must be tested
- N+1 queries must be prevented

### Development Workflow
1. Write failing test
2. Implement minimal solution
3. Run local CI: `make ci`
4. Fix any issues
5. Commit with semantic format
6. Push only when CI passes

This guide ensures that AI-assisted Laravel development maintains the high standards of traditional development while leveraging the productivity benefits of augmented coding. Always prioritize code quality, testability, and Laravel best practices over speed of implementation.

---

## PYTHON/SQLALCHEMY PATTERNS

### Project Context
These patterns emerged from the Beacon project (Task Management with Trello Sync), a Python/FastAPI application using SQLAlchemy ORM with strict TDD methodology.

### TDD Methodology for Python

**Critical Success Metrics from Phase 2 (Sync State Tracking):**
- 45/45 tests passing on first integration test run
- 98% test coverage achieved
- Zero integration bugs (validates TDD approach)
- Edge cases caught during RED phase, not in production

**Test Naming Convention:**
```python
# Use descriptive test names with underscores
def test_user_can_create_task_with_valid_data(): ...
def test_raises_validation_error_when_title_missing(): ...
def test_sends_notification_after_task_created(): ...
```

**TDD Workflow for SQLAlchemy Models:**
1. **RED**: Write failing test for model behavior
2. **GREEN**: Implement minimal model code
3. **REFACTOR**: Extract to event listeners or separate concerns
4. Run pytest with coverage: `pytest --cov=app --cov-report=term-missing`

### SQLAlchemy Event Listener Patterns

#### CRITICAL: Transaction-Safe Event Listeners

**Gotcha: Never use session operations inside event listeners**

```python
# ❌ WRONG: Using session operations in event listener causes StaleDataError
@listens_for(Task, 'after_insert')
def after_insert_listener(mapper, connection, target):
    session = object_session(target)
    audit = AuditLog(task_id=target.id)
    session.add(audit)  # CAUSES StaleDataError!

# ✅ CORRECT: Use connection.execute() for writes
@listens_for(Task, 'after_insert')
def after_insert_listener(mapper, connection, target):
    connection.execute(
        text("INSERT INTO audit_log (task_id, created_at) VALUES (:task_id, NOW())"),
        {"task_id": target.id}
    )
```

**Why this matters:**
- Event listeners execute within the same transaction as the triggering operation
- Session operations create state conflicts during flush cycle
- connection.execute() operates at lower level, avoiding state issues
- Automatic rollback on error preserves data integrity

**When to use each approach:**
- `connection.execute()`: Inside after_insert, after_update, before_delete listeners
- `object_session(self)`: In model methods called AFTER event completes

#### Session Management in Event Listeners

**Pattern: Accessing Current Session Safely**

```python
class Task(Base):
    __tablename__ = 'tasks'

    def mark_conflict_detected(self) -> None:
        """Write operation - must use object_session() for access outside events."""
        session = object_session(self)
        if session is None:
            raise RuntimeError("Instance is not bound to a session")

        self.sync_state = SyncState.CONFLICT
        self.last_conflict_at = datetime.now(timezone.utc)
        session.flush()

    @listens_for(Task, 'after_update')
    def after_update_listener(mapper, connection, target):
        """Event listener - use connection.execute() not session operations."""
        if target.needs_sync_update():
            connection.execute(
                text("UPDATE sync_log SET updated_at = NOW() WHERE task_id = :id"),
                {"id": target.id}
            )
```

**Key principle: Separate READ from WRITE operations**
- Read-only methods: Can use instance attributes directly
- Write operations outside events: Use `object_session(self)`
- Write operations inside events: Use `connection.execute()`

### External API Integration Patterns

#### Timestamp Parsing from External APIs

**Gotcha: Python's fromisoformat() doesn't accept 'Z' suffix**

```python
# ❌ WRONG: fromisoformat() fails on ISO timestamps with 'Z'
from datetime import datetime
timestamp_str = "2025-12-08T10:30:00.000Z"  # Trello format
dt = datetime.fromisoformat(timestamp_str)  # ValueError!

# ✅ CORRECT: Replace 'Z' with '+00:00' before parsing
dt = datetime.fromisoformat(timestamp_str.replace('Z', '+00:00'))
```

**Why this matters:**
- External APIs (Trello, Slack, etc.) often use ISO 8601 with 'Z' suffix
- Python's standard library doesn't handle 'Z' until Python 3.11+
- This pattern works across all Python 3.8+ versions
- Avoids dependency on third-party libraries (python-dateutil)

#### Hash-Based Conflict Detection

**Learning: Hash comparison is superior to timestamp comparison for distributed systems**

```python
# ❌ WRONG: Timestamp comparison (vulnerable to clock skew)
def has_conflict(self, external_updated_at: datetime) -> bool:
    return self.updated_at > external_updated_at

# ✅ CORRECT: Hash-based conflict detection (immune to clock skew)
import hashlib
import json

def calculate_content_hash(self) -> str:
    """Generate SHA-256 hash of normalized field values."""
    content = {
        'title': self.title,
        'description': self.description or '',
        'status': self.status,
        'due_date': self.due_date.isoformat() if self.due_date else None
    }
    normalized = json.dumps(content, sort_keys=True)
    return hashlib.sha256(normalized.encode()).hexdigest()

def detect_conflict(self, external_hash: str) -> bool:
    """Read-only conflict detection."""
    return self.content_hash != external_hash
```

**Benefits:**
- Immune to clock skew between systems
- Detects actual content changes, not just timestamp updates
- Works reliably in distributed systems
- Deterministic: same content always produces same hash

**Pattern for separation of concerns:**
```python
# Read operation (no side effects)
def detect_conflict(self, external_hash: str) -> bool:
    return self.content_hash != external_hash

# Write operation (state mutation)
def mark_conflict_detected(self) -> None:
    session = object_session(self)
    self.sync_state = SyncState.CONFLICT
    self.last_conflict_at = datetime.now(timezone.utc)
    session.flush()

# Usage in service layer
if task.detect_conflict(trello_hash):
    task.mark_conflict_detected()
```

#### Three-Way Merge Pattern for Conflict Detection

**Learning: Use history snapshots as "common ancestor" for reliable conflict detection**

**Problem with Two-Way Comparison:**
- Comparing only local vs external creates false positives
- Cannot distinguish between "both changed" vs "one changed"
- Example: Local changed A→B, Trello still has A → False conflict!

**Three-Way Merge Solution:**
```python
# ❌ WRONG: Two-way comparison (false positives)
def detect_conflict(self, external_data: dict) -> bool:
    return self.title != external_data['title']  # False positive!

# ✅ CORRECT: Three-way merge using history snapshot
class Task(Base):
    has_local_changes = Column(Boolean, default=False)
    conflict_fields = Column(JSON, nullable=True)  # ["title", "description"]

    def detect_conflict_fields(
        self,
        trello_data: dict,
        last_snapshot: Optional['TaskHistory']
    ) -> list[str]:
        """Three-way merge: compare both sides against common ancestor."""
        if not last_snapshot:
            # Conservative: flag conflict when no history available
            return [] if not self.has_local_changes else list(trello_data.keys())

        conflicts = []
        fields = ['title', 'description', 'status', 'due_date']

        for field in fields:
            local_value = getattr(self, field)
            trello_value = trello_data.get(field)
            snapshot_value = getattr(last_snapshot, field)

            # Both sides changed from snapshot → CONFLICT
            local_changed = local_value != snapshot_value
            trello_changed = trello_value != snapshot_value

            if local_changed and trello_changed:
                conflicts.append(field)

        return conflicts
```

**Why Three-Way Merge Works:**
- **Snapshot = Common Ancestor**: Last known state both systems agreed on
- **Local Changes**: Compare current local vs snapshot
- **External Changes**: Compare Trello vs snapshot
- **Conflict**: Only when BOTH changed the same field

**Conservative vs Optimistic Strategy:**
```python
# Conservative: Flag conflict when history unavailable
if not last_snapshot:
    if self.has_local_changes:
        return ["title", "description"]  # Prompt user
    return []  # Safe to auto-merge

# Optimistic: Apply changes when history unavailable
if not last_snapshot:
    return []  # Auto-merge everything (risky!)
```

**Rationale for Conservative Approach:**
- Better to prompt user than lose data
- First sync has no history → use has_local_changes flag
- Subsequent syncs use TaskHistory snapshots

### Bidirectional Sync Patterns (Phase 5: Push Conflict Detection)

**Context: Phase 5 achieved 8.7/10 quality score (highest to date), 16/16 tests passing, 1,297 lines of test code**

These patterns emerged from implementing push conflict detection that mirrors pull detection, creating a complete bidirectional sync system.

#### Architectural Symmetry Pattern

**Learning: Mirror pull and push logic exactly for predictable behavior**

```python
# ✅ CORRECT: Both directions use identical helpers and algorithms
class TrelloService:
    def pull_task_from_trello(self, task: Task, db: Session):
        """Phase 3: Pull detection."""
        # Fetch current Trello state
        trello_data = self._fetch_trello_card_state(task.trello_id)
        trello_hash = self._compute_trello_hash(trello_data)

        # Get last known state (common ancestor)
        last_history = self._get_last_sync_snapshot(task.id, db)

        # Detect conflicts using 3-way merge
        conflicts = self._detect_conflict_fields(
            task, trello_data, last_history
        )

    def push_task_to_trello(self, task: Task, db: Session):
        """Phase 5: Push detection (MIRRORS pull logic)."""
        # Fetch current Trello state (same helper!)
        trello_data = self._fetch_trello_card_state(task.trello_id)
        trello_hash = self._compute_trello_hash(trello_data)

        # Get last known state (same helper!)
        last_history = self._get_last_sync_snapshot(task.id, db)

        # Detect conflicts using 3-way merge (same helper!)
        conflicts = self._detect_conflict_fields(
            task, trello_data, last_history
        )

# Shared helpers (DRY principle)
def _compute_trello_hash(self, data: dict) -> str:
    """Single source of truth for hash calculation."""
    normalized = {
        'name': data.get('name', ''),
        'desc': data.get('desc', ''),
        'due': data.get('due'),
        'idList': data.get('idList')
    }
    content = json.dumps(normalized, sort_keys=True)
    return hashlib.sha256(content.encode()).hexdigest()
```

**Why Architectural Symmetry Matters:**
- **Predictable behavior**: Learn conflict detection once, applies both directions
- **Easier debugging**: Same logic = single point of investigation
- **Consistent test patterns**: Write tests once, adapt for push/pull
- **Lower cognitive load**: Developers only need to understand one algorithm

**Anti-pattern to avoid:**
```python
# ❌ WRONG: Different algorithms for push vs pull
def pull_task_from_trello(self, task, db):
    # Uses hash-based detection
    if task.local_hash != trello_hash:
        detect_conflict()

def push_task_to_trello(self, task, db):
    # Uses timestamp comparison (inconsistent!)
    if task.updated_at > trello_updated_at:
        detect_conflict()
```

**Lesson:** When implementing inverse operations (push/pull, import/export, encode/decode), use symmetric algorithms and shared helpers.

#### Fetch-Before-Push Safety Pattern

**Learning: Conservative safety > optimistic speed for user data**

```python
# ✅ CORRECT: Fetch-before-push (safety first)
def push_task_to_trello(self, task: Task, db: Session):
    """Always fetch current Trello state before pushing."""
    # Step 1: Fetch current state (~300ms latency)
    trello_data = self._fetch_trello_card_state(task.trello_id)

    # Step 2: Compute hash to detect external changes
    trello_hash = self._compute_trello_hash(trello_data)
    trello_changed = (trello_hash != task.sync_state.trello_hash)

    # Step 3: If Trello changed since last sync, detect conflicts
    if trello_changed:
        conflict_info = self._detect_push_conflict(
            task, trello_data, last_snapshot
        )

        if conflict_info['has_conflict']:
            # Block push, require user resolution
            raise ValueError("Conflict detected - resolve before pushing")

    # Step 4: Safe to push (no external changes or auto-merged)
    self._push_to_trello_api(task)

# ❌ WRONG: Optimistic push (no fetch, silent data loss)
def push_task_to_trello_optimistic(self, task: Task):
    """Dangerous: Push without checking Trello state."""
    # Just push and hope nobody else edited Trello
    self._push_to_trello_api(task)
    # → SILENT DATA LOSS if someone else made changes!
```

**Alternatives Considered and Rejected:**

```python
# ❌ REJECTED: ETag-based conditional PUT
# Problem: Trello API doesn't support If-Match headers
headers = {'If-Match': etag}
response = requests.put(url, headers=headers)
if response.status_code == 412:  # Precondition Failed
    handle_conflict()

# ❌ REJECTED: Optimistic concurrency with version numbers
# Problem: Requires custom version field on Trello cards
# Trello doesn't have built-in versioning
```

**Performance vs Safety Trade-off:**
- **Fetch-before-push**: +300ms latency, prevents data loss
- **Optimistic push**: -300ms latency, risks silent overwrites
- **Decision**: 300ms is acceptable; data loss is not

**Why This Matters:**
- Users prefer 300ms delay over lost work
- External edits (mobile app, other users) happen frequently
- Silent data loss erodes trust in sync system
- Explicit conflict UI is better than mysterious missing changes

**Lesson:** For user data sync, always prioritize **safety over speed**. Fetch-before-write prevents silent overwrites.

#### Resolved Conflict Trust Pattern

**Learning: Trust user's explicit decisions to prevent infinite conflict loops**

```python
# ✅ CORRECT: Trust resolved conflicts, skip re-detection
def push_task_to_trello(self, task: Task, db: Session):
    sync_state = task.sync_state

    # User explicitly resolved conflict → Trust their decision
    if sync_state.conflict_resolution_status == "resolved":
        # Skip conflict detection, push local version
        data_to_push = self._build_task_data_dict(task)
        self._push_to_trello_api(task.trello_id, data_to_push)

        # Reset sync state after successful push
        sync_state.conflict_resolution_status = None
        sync_state.conflict_detected = False
        return None

    # Normal path: Detect conflicts
    if trello_changed:
        return self._detect_push_conflict(task, trello_data, last_snapshot)

# ❌ WRONG: Always re-detect conflicts (infinite loop)
def push_task_to_trello_wrong(self, task, db):
    # Always check for conflicts, ignoring previous resolution
    if trello_changed:
        conflict = self._detect_push_conflict(...)  # Always runs!
        # User resolves → Push → Next push re-detects → Loop forever!
```

**Conflict Resolution Lifecycle:**
```
1. Initial Detection:
   - Pull detects conflict → conflict_resolution_status = "pending"
   - User sees conflict UI

2. User Resolves:
   - User chooses "Keep Local" → conflict_resolution_status = "resolved"
   - Task saved with resolution flag

3. Push Trusts Resolution:
   - Push sees "resolved" → Skip conflict detection
   - Push local version (user's explicit choice)

4. Successful Push Resets State:
   - Push completes → conflict_resolution_status = None
   - Ready for next sync cycle
```

**Why Trust Expires After Push:**
- After successful push, local and Trello are in sync again
- Next pull/push cycle starts fresh (no conflict flag)
- Trust only applies to single sync operation

**Lesson:** After user makes explicit conflict resolution, **trust their decision**. Don't second-guess resolved conflicts.

#### Pending Conflict Blocking Pattern

**Learning: Hard block > soft warning for ambiguous scenarios**

```python
# ✅ CORRECT: Hard block for pending conflicts
def push_task_to_trello(self, task: Task, db: Session):
    sync_state = task.sync_state

    # Pending conflict BLOCKS push operation
    if sync_state.conflict_detected and \
       sync_state.conflict_resolution_status == "pending":
        raise ValueError(
            f"Task {task.id} has pending conflict. "
            f"Resolve conflict before pushing. "
            f"Conflicting fields: {sync_state.conflict_fields}"
        )

    # Only proceed if no conflict or resolved
    # ...

# ❌ WRONG: Soft warning (ambiguous, risky)
def push_task_to_trello_wrong(self, task, db):
    sync_state = task.sync_state

    if sync_state.conflict_detected:
        logger.warning("Conflict detected, but pushing anyway")
        # Which version are we pushing? Local? Trello? Merged?
        # User doesn't know, we don't know → Ambiguous!

    self._push_to_trello_api(task)  # May lose data
```

**Error Message Guidelines:**
- **Clear**: Explain why operation blocked
- **Actionable**: Tell user how to proceed ("Resolve conflict before pushing")
- **Informative**: Show conflicting fields to aid resolution

**Frontend Handling:**
```typescript
// Frontend catches ValueError and shows resolution UI
try {
  await pushTaskToTrello(taskId)
} catch (error) {
  if (error.message.includes("pending conflict")) {
    // Redirect to conflict resolution dialog
    showConflictDialog(taskId)
  } else {
    showError(error.message)
  }
}
```

**Why Hard Block:**
- Prevents ambiguous data loss ("Did I push the right version?")
- Forces explicit user decision
- Clear error message guides user to resolution UI
- Better UX than silent wrong behavior

**Lesson:** For ambiguous scenarios, use **hard block over soft warning**. Make users explicitly choose rather than guessing their intent.

#### Auto-Merge Push Strategy

**Learning: Maximize throughput by auto-merging safe scenarios**

```python
# ✅ CORRECT: Auto-merge when different fields changed
def _detect_push_conflict(self, task, trello_data, last_snapshot):
    """Detect conflicts and auto-merge when safe."""
    local_changed_fields = self._detect_changed_fields(
        last_snapshot, task, SYNCABLE_FIELDS
    )
    trello_changed_fields = self._detect_changed_fields(
        last_snapshot, trello_data, SYNCABLE_FIELDS
    )

    # Find fields changed by BOTH sides
    conflicting_fields = set(local_changed_fields) & set(trello_changed_fields)

    # Different fields changed → AUTO-MERGE
    if not conflicting_fields:
        # Build merged data combining both changes
        merged_data = self._build_task_data_dict(task)  # Start with local
        for field in trello_changed_fields:
            # Apply Trello's changes to non-conflicting fields
            merged_data[field] = trello_data[field]

        # Push merged data (not just local!)
        self._push_to_trello_api(task.trello_id, merged_data)
        sync_state.conflict_resolution_status = "auto_merged"
        return None  # No user intervention needed

    # Same fields changed → REQUIRE USER RESOLUTION
    return {
        'has_conflict': True,
        'conflict_fields': list(conflicting_fields),
        'local_values': {f: getattr(task, f) for f in conflicting_fields},
        'trello_values': {f: trello_data[f] for f in conflicting_fields}
    }

# ❌ WRONG: Always require manual resolution
def _detect_push_conflict_wrong(self, task, trello_data, last_snapshot):
    # Block ALL changes, even safe auto-merges
    if local_changed_fields or trello_changed_fields:
        return {'has_conflict': True}  # Too conservative!
    # → Users get frustrated by unnecessary conflict prompts
```

**Auto-Merge Example:**
```
Last Snapshot:
  title: "Buy groceries"
  description: "Get milk"
  status: "todo"

Local Changes:
  description: "Get milk and eggs"  ← Changed locally

Trello Changes:
  status: "in_progress"  ← Changed on Trello

Result: AUTO-MERGE (different fields)
  title: "Buy groceries"  (unchanged)
  description: "Get milk and eggs"  (from local)
  status: "in_progress"  (from Trello)
```

**Conflict Example (requires user):**
```
Last Snapshot:
  title: "Buy groceries"

Local Changes:
  title: "Buy groceries and toiletries"

Trello Changes:
  title: "Buy groceries and snacks"

Result: CONFLICT (same field)
  → Show 3-way diff UI
  → User chooses version
```

**Throughput Statistics (estimated):**
- **80% of syncs**: Different fields changed → Auto-merged
- **15% of syncs**: No changes on either side → No-op
- **5% of syncs**: Same fields changed → Manual resolution

**Why Auto-Merge:**
- Reduces user friction (80% fewer conflict prompts)
- Increases sync throughput
- Only blocks for truly ambiguous conflicts
- User sees seamless experience most of the time

**Lesson:** Auto-merge when safe, only prompt user when ambiguous. **Minimize interruptions** while maintaining safety.

#### Sequential Batch Processing Pattern

**Learning: Simplicity > premature optimization**

```python
# ✅ CORRECT: Sequential processing (simple, safe)
def batch_push_tasks_to_trello(task_ids: list[int], db: Session):
    """Process batch sequentially with continue-on-error."""
    pushed = 0
    conflicts = 0
    errors = 0
    results = []

    for task_id in task_ids:
        try:
            task = db.query(Task).filter(Task.id == task_id).first()
            if not task:
                errors += 1
                continue

            # Push returns None on success, conflict info on conflict
            result = service.push_task_to_trello(task, db)

            if result:  # Conflict detected
                conflicts += 1
                results.append({'task_id': task_id, 'status': 'conflict'})
            else:  # Success
                pushed += 1
                results.append({'task_id': task_id, 'status': 'pushed'})

        except Exception as e:
            errors += 1
            results.append({
                'task_id': task_id,
                'status': 'error',
                'error': str(e)
            })
            continue  # Keep processing other tasks

    return {
        'pushed': pushed,
        'conflicts': conflicts,
        'errors': errors,
        'results': results
    }

# ❌ WRONG: Parallel processing (complex, risky)
import asyncio

async def batch_push_tasks_parallel(task_ids: list[int], db: Session):
    """Dangerous: Parallel processing risks rate limits."""
    tasks_futures = [
        push_task_async(task_id, db) for task_id in task_ids
    ]

    # Problems:
    # 1. Trello rate limit: 300 requests per 10 seconds
    #    100 tasks = instant rate limit hit
    # 2. Database transaction handling becomes complex
    # 3. Error recovery is harder (which tasks failed?)
    # 4. Rollback semantics unclear

    results = await asyncio.gather(*tasks_futures, return_exceptions=True)
    # Now what? How do we rollback some tasks but not others?
```

**Sequential Processing Benefits:**
- **Simple error handling**: Try-except per task
- **Continue on error**: One failure doesn't block others
- **Predictable**: Easy to debug, clear execution order
- **Safe**: Respects rate limits naturally (serial = slower = safer)

**Rate Limit Considerations:**
- **Trello API**: 300 requests per 10 seconds (30 req/sec)
- **Sequential**: 2-3 req/sec (well under limit)
- **Parallel 100 tasks**: Instant rate limit violation → 429 errors

**When to Consider Parallelism:**
- **Only after measuring**: Profile with realistic batch sizes
- **Only if necessary**: "Is sequential actually too slow?"
- **With rate limiting**: Semaphore to limit concurrent requests
- **With backoff**: Exponential backoff for 429 responses

**Lesson:** **Simplicity over premature optimization**. Parallelize only when profiling shows it's necessary. Sequential is predictable and safe.

#### Test File Organization by Type

**Learning: Split tests by type (unit/integration/API), not by feature**

```bash
# ✅ CORRECT: Organized by test type
tests/
  services/
    test_trello_push_conflict_detection.py    # 681 lines, 10 tests
      # Unit tests for helpers
      # Integration tests for push method
      # Mock external Trello API

  api/
    test_batch_push.py                        # 284 lines, 3 tests
      # API endpoint tests
      # Request/response validation
      # Error handling

  integration/
    test_trello_push_integration.py           # 332 lines, 3 tests
      # Full workflow E2E tests
      # Cross-phase integration
      # Real objects, mocked external API only

# ❌ WRONG: Organized by feature (files become huge)
tests/
  test_trello_push.py                         # 1,297 lines! (too big)
    # Everything mixed together
    # Hard to navigate
    # Slow test runs (can't selectively run)
```

**File Size Guidelines:**
- **< 300 lines**: Excellent, keep as is
- **300-500 lines**: Acceptable, monitor growth
- **500-700 lines**: Consider splitting
- **> 700 lines**: Split now

**Test Type Characteristics:**
```python
# Unit Test: Fast, isolated, many
def test_compute_trello_hash_returns_consistent_hash():
    """Tests single helper function."""
    data = {'name': 'Test', 'desc': 'Description'}
    hash1 = service._compute_trello_hash(data)
    hash2 = service._compute_trello_hash(data)
    assert hash1 == hash2  # Deterministic

# Integration Test: Medium speed, real objects, some
def test_push_task_with_conflict_returns_conflict_info():
    """Tests push method with real Task and Session objects."""
    task = Task(title="Test", user_id=1)
    db_session.add(task)
    db_session.flush()

    with patch('requests.get') as mock_get:
        mock_get.return_value.json.return_value = {...}
        result = service.push_task_to_trello(task, db_session)

    assert result['has_conflict'] is True

# API Test: Slow, full stack, few
def test_batch_push_endpoint_returns_summary(client):
    """Tests FastAPI endpoint end-to-end."""
    response = client.post('/api/tasks/batch-push', json={
        'task_ids': [1, 2, 3]
    })
    assert response.status_code == 200
    assert response.json()['pushed'] == 2
```

**Benefits of Organization by Type:**
- **Selective test runs**: `pytest tests/unit/` for fast feedback
- **Clear test purpose**: File name indicates test type
- **Manageable file sizes**: Each type stays under 500 lines
- **Easier navigation**: Find tests by their characteristics

**Lesson:** Split test files by **test type** (unit, integration, API, E2E), not by feature. Keeps files manageable and enables selective test execution.

#### Method Length and Complexity Guidelines

**Learning: 80 lines is a guideline, not a law**

```python
# Current: push_task_to_trello() is 131 lines
# Refactor-scan flagged it, but context matters

def push_task_to_trello(self, task: Task, db: Session):
    """131 lines, but linear flow (acceptable)."""

    # Section 1: Validation and setup (47 lines)
    # - Check if task has trello_id
    # - Fetch current Trello state
    # - Get last sync snapshot
    # - Validate prerequisites

    # Section 2: Conflict detection (46 lines)
    # - Check if Trello changed since last sync
    # - Detect conflicting fields
    # - Build conflict info
    # - Handle auto-merge vs manual resolution

    # Section 3: Push to Trello API (36 lines)
    # - Build request payload
    # - Send PUT request
    # - Update sync state
    # - Create history snapshot
```

**When Long Methods Are Acceptable:**
- **Linear flow**: No complex branching or nesting
- **Well-commented**: Clear section markers
- **Single responsibility**: Does one thing (push with conflict detection)
- **Well-tested**: Comprehensive integration tests
- **Low cyclomatic complexity**: < 10 decision points

**When to Refactor:**
```python
# ❌ REQUIRES REFACTORING:
# - Method > 150 lines
# - Cyclomatic complexity > 10
# - Nesting depth > 3 levels
# - Difficult to write unit tests (must use integration tests)
# - Contains duplicated code blocks

def overly_complex_method(self):
    if condition1:
        if condition2:
            if condition3:
                for item in items:
                    if item.check():
                        # Too deeply nested!
```

**Refactoring Thresholds:**
- **80 lines**: Monitor, consider extraction if complexity grows
- **120 lines**: Acceptable if linear and well-documented
- **150 lines**: Refactor unless exceptional circumstances
- **Complexity > 10**: Refactor regardless of line count

**Lesson:** **80 lines is a guideline, not a law**. Refactor when complexity becomes unmaintainable, not just because of line count.

#### File Size Growth Tracking

**Learning: Track file growth per phase to plan refactoring**

**trello.py Growth History:**
- **Phase 2** (Sync State): ~500 lines (baseline)
- **Phase 3** (Pull Detection): ~750 lines (+250 for conflict detection)
- **Phase 4** (Resolution): ~750 lines (no growth, just endpoints)
- **Phase 5** (Push Detection): ~987 lines (+237 for push logic)

**Refactoring Thresholds:**
```
  0-600 lines: ✅ Good (single file acceptable)
600-900 lines: ⚠️  Monitor (watch for complexity)
900-1200 lines: ⚠️  Plan split (start designing module boundaries)
    >1200 lines: ❌ Split now (technical debt accumulating)
```

**Proposed Split (when > 1200 lines):**
```python
# Current structure (987 lines, approaching threshold)
app/services/trello.py
  - TrelloService class
  - Pull methods (300 lines)
  - Push methods (300 lines)
  - Helpers (200 lines)
  - Mapping utilities (187 lines)

# Future structure (when > 1200 lines):
app/services/trello/
  __init__.py                    # Public API
  client.py                      # Trello API wrapper (200 lines)
  sync_pull.py                   # Pull + conflict detection (400 lines)
  sync_push.py                   # Push + conflict detection (400 lines)
  mapping.py                     # Status/list mappings (200 lines)
  helpers.py                     # Shared utilities (200 lines)
```

**Growth Monitoring Strategy:**
1. **After each phase**: Check file line count
2. **Document growth**: Note in phase summary
3. **Plan ahead**: Design module boundaries before hitting 1200
4. **Refactor proactively**: Split before code becomes tangled

**Why Track Growth:**
- **Prevents surprise refactoring**: Plan before file becomes unmanageable
- **Documents complexity trends**: Shows which features add most code
- **Justifies refactoring**: Data-driven decision ("file grew 40% this phase")
- **Identifies patterns**: Understand growth rate per feature type

**Lesson:** **Track file growth per phase**. Plan refactoring proactively before hitting 1200 lines.

#### Cross-Phase Integration Testing

**Learning: Test interactions between phases, not just individual features**

```python
# ✅ CORRECT: Cross-phase integration test
def test_full_bidirectional_workflow():
    """Test Phase 3 (pull) + Phase 4 (resolve) + Phase 5 (push)."""

    # Setup: Task exists locally and on Trello
    task = Task(title="Original Title", trello_id="card123")
    db.add(task)
    db.flush()

    # Phase 3: Pull detects conflict
    with patch('requests.get') as mock_get:
        mock_get.return_value.json.return_value = {
            'name': 'Trello Changed Title',  # External edit
            'desc': task.description,
            # ... other fields
        }
        result = service.pull_task_from_trello(task, db)

    # Verify: Conflict detected
    assert result['has_conflict'] is True
    assert 'title' in result['conflict_fields']
    assert task.sync_state.conflict_resolution_status == "pending"

    # Phase 4: User resolves conflict (chooses local)
    task.sync_state.conflict_resolution_status = "resolved"
    task.sync_state.resolved_by_user_id = 1
    db.commit()

    # Phase 5: Push trusts resolved version
    with patch('requests.put') as mock_put:
        mock_put.return_value.status_code = 200
        result = service.push_task_to_trello(task, db)

    # Verify: Push succeeded without re-detecting conflict
    assert result is None  # No conflict returned
    assert mock_put.called
    pushed_data = mock_put.call_args[1]['json']
    assert pushed_data['name'] == "Original Title"  # Local version
    assert task.sync_state.conflict_resolution_status is None  # Reset

# ✅ CORRECT: Auto-merge integration test
def test_pull_then_push_with_different_fields():
    """Test auto-merge across pull and push."""

    # Phase 3: Pull with different field changed
    # Local: description changed
    # Trello: status changed
    # → Auto-merge applies Trello's status to local

    # Phase 5: Push with different field changed
    # Local: title changed
    # Trello: due_date changed
    # → Auto-merge applies Trello's due_date to local
    # → Push merged result to Trello
```

**Cross-Phase Test Patterns:**
```python
# Pattern 1: Sequential phase execution
def test_phase2_then_phase3_then_phase5():
    """Sync state (P2) → Pull detect (P3) → Push (P5)."""

# Pattern 2: Conflict resolution workflow
def test_conflict_detection_resolution_push():
    """Detect (P3) → Resolve (P4) → Push (P5)."""

# Pattern 3: Batch operations across phases
def test_batch_pull_then_batch_push():
    """Batch pull (P3) → Batch push (P5)."""
```

**Why Cross-Phase Testing Matters:**
- **Catches assumptions**: Phases may assume state that other phases provide
- **Verifies workflows**: Tests real user scenarios (detect → resolve → push)
- **Finds edge cases**: Interactions reveal bugs that unit tests miss
- **Documents behavior**: Shows expected end-to-end flow

**Integration Test Coverage:**
- **Unit tests (70%)**: Individual phase logic
- **Integration tests (20%)**: Cross-phase interactions
- **E2E tests (10%)**: Full user workflows

**Lesson:** Write integration tests that span phases. **Test the SYSTEM**, not just individual features.

### Phase 5 Quality Metrics Summary

**Achievements:**
- **Quality Score**: 8.7/10 (highest phase to date, +0.2 from Phase 4)
- **Test Coverage**: 16/16 tests passing (1,297 lines of test code)
- **Critical Bugs**: Zero
- **Code Symmetry**: Excellent (mirrors Phase 3 exactly)
- **Architectural Consistency**: Shared helpers between push and pull

**Key Success Factors:**
1. **Architectural symmetry**: Reused Phase 3 patterns exactly
2. **Conservative safety**: Fetch-before-push prevented data loss
3. **Auto-merge strategy**: Minimized user friction (80% auto-merged)
4. **Test organization**: Split by type (unit/integration/API)
5. **Cross-phase testing**: Validated bidirectional workflows

**Comparison with Previous Phases:**
- **Phase 2**: 7.8/10 (baseline)
- **Phase 3**: 7.5/10 (complexity increased)
- **Phase 4**: 8.5/10 (improved with cleanup)
- **Phase 5**: 8.7/10 (highest quality, best symmetry)

**Lesson:** Quality improves when you reuse proven patterns and maintain architectural consistency.

#### Event Listener Timing Gotchas

**CRITICAL: Event listeners execute BEFORE your method continues**

**Gotcha: Hash comparison fails because listener already updated it**

```python
# ❌ WRONG: Checking hash after event listener modified it
class Task(Base):
    @listens_for(Task, 'before_update', propagate=True)
    def before_update_listener(mapper, connection, target):
        # Recalculate hash on every update
        target.local_hash = target.calculate_content_hash()

class TrelloService:
    def pull_changes(self, task: Task, trello_data: dict):
        old_hash = task.local_hash  # Store current hash

        task.title = trello_data['title']  # Triggers before_update listener!
        session.flush()

        # BUG: local_hash already updated by listener!
        if task.local_hash != old_hash:  # Always False!
            print("Task changed")

# ✅ CORRECT: Use separate flag for tracking local changes
class Task(Base):
    has_local_changes = Column(Boolean, default=False)
    local_hash = Column(String, nullable=False)

    @listens_for(Task, 'before_update', propagate=True)
    def before_update_listener(mapper, connection, target):
        # Recalculate hash (always happens)
        target.local_hash = target.calculate_content_hash()

        # Set flag only for user-initiated changes
        if not getattr(target, '_syncing', False):
            target.has_local_changes = True

class TrelloService:
    def pull_changes(self, task: Task, trello_data: dict):
        task._syncing = True  # Mark as sync operation
        task.title = trello_data['title']
        task.has_local_changes = False  # Clear flag after external sync
        session.flush()
        task._syncing = False
```

**Why this matters:**
- Event listeners execute synchronously during flush()
- Hash recalculation happens before your next line executes
- Cannot rely on comparing "before" and "after" hash values
- Use explicit flags for state tracking instead

**Pattern: Flag Preservation During Auto-Merge**

```python
# ❌ WRONG: Clearing flag loses push tracking
def pull_changes(self, task: Task, trello_data: dict):
    task.title = trello_data['title']
    task.has_local_changes = False  # BUG: Loses track of unpushed changes!
    session.flush()

# ✅ CORRECT: Preserve flag when local changes still need pushing
def pull_changes(self, task: Task, trello_data: dict):
    conflicts = task.detect_conflict_fields(trello_data, last_snapshot)

    if not conflicts:
        # Auto-merge: Apply Trello changes
        task.title = trello_data['title']
        # KEEP has_local_changes = True if local changes need pushing
        # Only clear flag during explicit push operation (Phase 4)
    else:
        # Manual merge: User resolves conflicts
        task.conflict_fields = conflicts
        task.sync_state = SyncState.CONFLICT
```

**Rationale:**
- has_local_changes tracks "needs push to Trello"
- Auto-merge pulls Trello changes WITHOUT clearing push flag
- Flag only cleared after successful push (Phase 4)
- Prevents local changes from being lost

#### Test Factory Pattern for Event Listeners

**Gotcha: Manual creation conflicts with auto-creation by event listeners**

```python
# ❌ WRONG: Creating related entities manually when listener auto-creates them
def test_task_creation_creates_history_snapshot(db_session):
    task = Task(title="Test", user_id=1)
    db_session.add(task)
    db_session.flush()

    # Manually create history (conflicts with listener!)
    history = TaskHistory(task_id=task.id, title="Test")
    db_session.add(history)
    db_session.commit()

    # Test fails: duplicate history entries!
    histories = db_session.query(TaskHistory).filter_by(task_id=task.id).all()
    assert len(histories) == 1  # FAILS: Found 2!

# ✅ CORRECT: Query auto-created entities instead of creating manually
def test_task_creation_creates_history_snapshot(db_session):
    task = Task(title="Test", user_id=1)
    db_session.add(task)
    db_session.flush()  # Triggers after_insert listener

    # Query the auto-created history entry
    history = (
        db_session.query(TaskHistory)
        .filter_by(task_id=task.id)
        .order_by(TaskHistory.created_at.desc())
        .first()
    )

    assert history is not None
    assert history.title == "Test"
    assert history.snapshot_type == "created"
```

**Pattern: flush() → query() → assert**
1. **flush()**: Trigger event listener to create related entities
2. **query()**: Retrieve auto-created entities from database
3. **assert**: Verify listener behavior

**Why this matters:**
- Event listeners create related entities automatically
- Tests should verify auto-creation, not bypass it
- Manual creation creates duplicates and masks listener bugs
- Query pattern validates real behavior

#### Field-Level Conflict Tracking

**Learning: Track specific fields for better UX**

```python
# ❌ BASIC: Boolean conflict flag (poor UX)
class Task(Base):
    has_conflict = Column(Boolean, default=False)

# User sees: "Task has conflicts" (What conflicts? Which fields?)

# ✅ BETTER: Field-level conflict tracking (rich UX)
class Task(Base):
    conflict_fields = Column(JSON, nullable=True)  # ["title", "description"]

    def detect_conflict_fields(self, trello_data, snapshot) -> list[str]:
        """Return list of conflicting field names."""
        conflicts = []
        for field in ['title', 'description', 'status']:
            if self._field_has_conflict(field, trello_data, snapshot):
                conflicts.append(field)
        return conflicts

# User sees: "Conflicts in: Title, Description"
# UI can highlight specific fields
```

**Benefits:**
- UI can show exactly which fields conflict
- User can resolve field-by-field
- Enables partial auto-merge (non-conflicting fields)
- Better error messages and logging

**Implementation Pattern:**
```python
# Store conflict fields as JSON array
task.conflict_fields = ["title", "description"]

# UI retrieves and displays
if task.conflict_fields:
    print(f"Conflicts detected in: {', '.join(task.conflict_fields)}")
    for field in task.conflict_fields:
        print(f"  Local: {getattr(task, field)}")
        print(f"  Trello: {trello_data[field]}")
        print(f"  Last known: {getattr(snapshot, field)}")
```

### Testing Best Practices for SQLAlchemy

#### Test Database Setup

```python
# conftest.py
import pytest
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

@pytest.fixture(scope='function')
def db_session():
    """Provide clean database session for each test."""
    engine = create_engine('postgresql://localhost/test_db')
    Session = sessionmaker(bind=engine)
    session = Session()

    # Setup
    Base.metadata.create_all(engine)

    yield session

    # Teardown
    session.rollback()
    session.close()
    Base.metadata.drop_all(engine)
```

#### Testing Event Listeners

```python
def test_event_listener_updates_sync_state_on_insert(db_session):
    """Test that after_insert listener creates sync log entry."""
    # RED: Write failing test first
    task = Task(title="Test Task")
    db_session.add(task)
    db_session.commit()

    # Verify event listener executed
    sync_log = db_session.query(SyncLog).filter_by(task_id=task.id).first()
    assert sync_log is not None
    assert sync_log.action == 'INSERT'
```

### TDD Success Pattern: Integration Tests Passing First Try

**Achievement from Phase 2:**
- All 45 tests passed on first integration test run
- Zero refactoring needed post-implementation
- 98% test coverage maintained throughout

**What made this possible:**
1. **Strict RED-GREEN-REFACTOR discipline**
   - Never wrote implementation before failing test
   - Each test drove exactly one piece of functionality

2. **Edge cases identified during RED phase**
   - Null handling tests written first
   - Transaction rollback tests written before implementation
   - Conflict detection edge cases caught in tests

3. **Event listener tests before implementation**
   - Tested transaction safety before writing listeners
   - Verified rollback behavior with failing operations

4. **Integration tests validate unit test quality**
   - Unit tests passing → Integration tests passing
   - Proves TDD approach works for complex features

**Key Takeaway:** When integration tests pass on first run, it validates that:
- Unit tests were comprehensive
- TDD methodology was followed strictly
- Design emerged from tests, not assumptions

### Common SQLAlchemy Anti-Patterns

```python
# ❌ WRONG: Session operations in event listeners
@listens_for(Model, 'after_insert')
def listener(mapper, connection, target):
    session.add(RelatedModel())  # Causes StaleDataError

# ✅ CORRECT: Connection operations in event listeners
@listens_for(Model, 'after_insert')
def listener(mapper, connection, target):
    connection.execute(text("INSERT INTO ..."))

# ❌ WRONG: Mixing read and write in single method
def process_update(self, new_data):
    has_conflict = self.detect_conflict(new_data)  # Read
    self.sync_state = SyncState.CONFLICT  # Write
    return has_conflict

# ✅ CORRECT: Separate read from write
def detect_conflict(self, new_data) -> bool:
    """Read-only conflict detection."""
    return self.content_hash != new_data.hash

def mark_conflict_detected(self) -> None:
    """Write operation with clear side effects."""
    self.sync_state = SyncState.CONFLICT
    self.last_conflict_at = datetime.now(timezone.utc)

# ❌ WRONG: Using timestamp comparison for conflict detection
def has_conflict(self, external_timestamp):
    return self.updated_at > external_timestamp  # Clock skew issues!

# ✅ CORRECT: Using content hash for conflict detection
def detect_conflict(self, external_hash):
    return self.content_hash != external_hash  # Reliable!
```

### Critical Python Indentation Gotchas

**CRITICAL: Method Indentation Bug Pattern**

**Gotcha: Methods accidentally nested inside functions instead of being class methods**

**Real-world Impact (Phase 4):**
- Methods were not accessible (AttributeError at runtime)
- Tests passed because they mocked the methods
- Would have caused production failure
- Caught by refactor-scan tool, not by pytest

```python
# ❌ WRONG: Method indented inside function (copy-paste error)
def get_trello_service() -> TrelloService | None:
    return TrelloService()

    def push_task_to_trello(self, task: Task, db: Session):  # Wrong indentation!
        """This is NOT a method of TrelloService!"""
        pass

    def _get_status_for_list(self, list_id: str) -> str:  # Also wrong!
        pass

# ✅ CORRECT: Methods at class level
class TrelloService:
    def push_task_to_trello(self, task: Task, db: Session):
        """Proper method of TrelloService."""
        pass

    def _get_status_for_list(self, list_id: str) -> str:
        pass

def get_trello_service() -> TrelloService | None:
    """Function should be separate, not contain methods."""
    return TrelloService()
```

**Why This Happens:**
- Copy-paste errors from restructuring code
- Editor auto-indentation mistakes
- Missing visual cues in large files

**Why Tests Don't Catch It:**
```python
# Tests pass because they mock the method
@patch.object(TrelloService, 'push_task_to_trello')
def test_push_to_trello(mock_push):
    service = TrelloService()
    service.push_task_to_trello(task, db)  # Mock intercepts call
    mock_push.assert_called_once()  # Test passes!

# Runtime fails without mock
service = TrelloService()
service.push_task_to_trello(task, db)  # AttributeError!
```

**How to Prevent:**
1. **Use AST-based linting tools**: Ruff, MyPy, Pylint catch these
2. **Run integration tests without mocks**: Verify actual method exists
3. **Use refactor-scan**: Static analysis catches structural issues
4. **Add interface verification tests**:

```python
def test_service_interface_contract():
    """Verify service has required methods (no mocks)."""
    service = TrelloService()
    assert hasattr(service, 'push_task_to_trello')
    assert callable(getattr(service, 'push_task_to_trello'))
    assert hasattr(service, '_get_status_for_list')
```

**Lesson:** Mocked unit tests can hide structural bugs. Always mix with integration tests that use real objects.

### Pydantic v2 Migration Patterns

**CRITICAL: Deprecated Config Class (35+ warnings)**

**Issue: Pydantic v1 style causes deprecation warnings in v2**

```python
# ❌ DEPRECATED (Pydantic v1 style)
class ConflictResponse(BaseModel):
    id: int
    conflict_fields: list[str]

    class Config:
        from_attributes = True  # Deprecated syntax
        arbitrary_types_allowed = True

# ✅ CORRECT (Pydantic v2 style)
from pydantic import ConfigDict

class ConflictResponse(BaseModel):
    id: int
    conflict_fields: list[str]

    model_config = ConfigDict(
        from_attributes=True,
        arbitrary_types_allowed=True
    )
```

**Migration Checklist:**
- [ ] Replace `class Config:` with `model_config = ConfigDict(...)`
- [ ] Change `orm_mode = True` to `from_attributes = True`
- [ ] Update `json()` to `model_dump_json()`
- [ ] Update `dict()` to `model_dump()`
- [ ] Update `parse_obj()` to `model_validate()`

**Common Patterns:**
```python
# Old (v1)                          # New (v2)
model.dict()                        model.model_dump()
model.json()                        model.model_dump_json()
Model.parse_obj(data)               Model.model_validate(data)
Model.parse_raw(json_str)           Model.model_validate_json(json_str)
Model.construct(**data)             Model.model_construct(**data)
```

**Why This Matters:**
- Deprecation warnings clutter test output (35+ warnings)
- Will break in Pydantic v3
- Better type safety and performance in v2
- Cleaner syntax with ConfigDict

**Run Pydantic v2 validation:**
```bash
pytest -W error::DeprecationWarning  # Treat warnings as errors
```

### Frontend/Backend Integration Patterns

#### Environment Variable Management

**CRITICAL: Never hardcode API URLs**

```typescript
// ❌ WRONG: Hardcoded URL
const API_BASE_URL = 'http://localhost:8000/api'

export const useTaskConflicts = () => {
  const response = await fetch(`${API_BASE_URL}/tasks/${id}/conflict`)
}

// ✅ CORRECT: Environment variable with fallback
const API_BASE_URL = import.meta.env.VITE_API_URL || 'http://localhost:8000/api'

export const useTaskConflicts = () => {
  const response = await fetch(`${API_BASE_URL}/tasks/${id}/conflict`)
}
```

**Create .env.example:**
```bash
# .env.example (check into version control)
VITE_API_URL=http://localhost:8000/api

# .env.local (gitignored)
VITE_API_URL=https://api.production.com/api
```

**Why This Matters:**
- Development vs production environments
- Local testing vs deployed testing
- API versioning and migration
- Docker container configuration

#### API Error Response Patterns

**HTTP Status Code Strategy:**

```python
# FastAPI endpoint with proper status codes
from fastapi import HTTPException, status

@router.post("/tasks/{task_id}/resolve-conflict")
async def resolve_conflict(task_id: int, request: ResolveConflictRequest):
    # 404: Resource not found
    task = db.query(Task).filter(Task.id == task_id).first()
    if not task:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="Task not found"
        )

    # 404: Conflict not found (treat as "resource" not available)
    if not task.sync_state or task.sync_state != SyncState.CONFLICT:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="No conflict detected for this task"
        )

    # 400: Client error (missing required field)
    if request.strategy == "merge_manual" and not request.merged_data:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail="merged_data required for merge_manual strategy"
        )

    # 422: Validation error (handled automatically by Pydantic)
    # Invalid enum value, type mismatch, etc.

    # 200: Success with data
    return {"status": "resolved", "task": task}
```

**Status Code Guidelines:**
- **200 OK**: Successful operation with response data
- **201 Created**: Resource created successfully
- **204 No Content**: Successful operation, no response data
- **400 Bad Request**: Client error (missing field, invalid combination)
- **404 Not Found**: Resource doesn't exist (task, conflict, user)
- **422 Unprocessable Entity**: Validation error (Pydantic handles)
- **500 Internal Server Error**: Unexpected server error

**Frontend Error Handling:**
```typescript
try {
  const response = await fetch(`/api/tasks/${id}/resolve-conflict`, {
    method: 'POST',
    body: JSON.stringify(request)
  })

  if (!response.ok) {
    if (response.status === 404) {
      throw new Error('Task or conflict not found')
    } else if (response.status === 400) {
      const error = await response.json()
      throw new Error(error.detail)  // Show user-friendly message
    }
    throw new Error('Failed to resolve conflict')
  }

  return await response.json()
} catch (error) {
  // Display error to user
  setError(error.message)
}
```

#### 3-Way Diff UI Patterns

**Challenge: Display three versions side-by-side clearly**

```typescript
// Component structure for conflict resolution
interface ConflictViewProps {
  localValues: Record<string, any>
  trelloValues: Record<string, any>
  lastSyncedValues: Record<string, any>
  conflictFields: string[]
}

// ✅ CORRECT: Progressive disclosure pattern
const ConflictView: React.FC<ConflictViewProps> = ({
  localValues,
  trelloValues,
  lastSyncedValues,
  conflictFields
}) => {
  // Only show conflicting fields, not all fields
  return (
    <div className="conflict-view">
      {conflictFields.map(field => (
        <div key={field} className="conflict-field">
          <h3>{field}</h3>

          {/* Three columns with color coding */}
          <div className="three-way-diff">
            <div className="local-version" style={{borderColor: 'blue'}}>
              <label>Local (Your Changes)</label>
              <pre>{localValues[field]}</pre>
            </div>

            <div className="trello-version" style={{borderColor: 'green'}}>
              <label>Trello (External Changes)</label>
              <pre>{trelloValues[field]}</pre>
            </div>

            <div className="base-version" style={{borderColor: 'gray'}}>
              <label>Last Synced (Common Ancestor)</label>
              <pre>{lastSyncedValues[field]}</pre>
            </div>
          </div>
        </div>
      ))}
    </div>
  )
}
```

**Design Principles:**
1. **Color coding**: Local=blue, External=green, Base=gray
2. **Progressive disclosure**: Show only conflicting fields
3. **Fixed-width columns**: Prevent layout shifts
4. **Overflow scrolling**: Handle long content
5. **Clear labeling**: Explain what each version represents

**Manual Merge State Management:**
```typescript
// Track per-field selections
const [selections, setSelections] = useState<Record<string, 'local' | 'trello'>>({})

// Handle field selection
const handleSelectLocal = (field: string) => {
  setSelections(prev => ({ ...prev, [field]: 'local' }))
}

const handleSelectTrello = (field: string) => {
  setSelections(prev => ({ ...prev, [field]: 'trello' }))
}

// Build merged object on-demand (not stored in state)
const buildMergedData = () => {
  const merged: Record<string, any> = {}
  for (const field of conflictFields) {
    const source = selections[field] || 'local'  // Default to local
    merged[field] = source === 'local'
      ? localValues[field]
      : trelloValues[field]
  }
  return merged
}

// Submit merged data
const handleSubmit = async () => {
  const mergedData = buildMergedData()
  await resolveConflict(taskId, {
    strategy: 'merge_manual',
    merged_data: mergedData
  })
}
```

**Lesson:** Keep state minimal (just selections), derive merged data on-demand. Prevents state synchronization bugs.

#### Accessibility Best Practices (React)

**Implement from the start, not as afterthought:**

```typescript
// ✅ CORRECT: Accessible modal dialog
const ConflictResolutionDialog: React.FC<Props> = ({ isOpen, onClose, conflict }) => {
  const firstButtonRef = useRef<HTMLButtonElement>(null)

  useEffect(() => {
    if (isOpen) {
      // Focus first button when dialog opens
      firstButtonRef.current?.focus()
    }
  }, [isOpen])

  // Keyboard navigation
  const handleKeyDown = (e: KeyboardEvent) => {
    if (e.key === 'Escape') {
      onClose()
    }
  }

  return (
    <div
      role="dialog"
      aria-modal="true"
      aria-labelledby="dialog-title"
      onKeyDown={handleKeyDown}
    >
      <h2 id="dialog-title">
        {conflict.conflict_fields.length} conflict
        {conflict.conflict_fields.length === 1 ? '' : 's'} detected
      </h2>

      <button
        ref={firstButtonRef}
        aria-label="Accept local changes"
        onClick={handleAcceptLocal}
      >
        Keep Local
      </button>

      <button
        aria-label="Accept Trello changes"
        onClick={handleAcceptTrello}
      >
        Keep Trello
      </button>
    </div>
  )
}
```

**Accessibility Checklist:**
- [ ] ARIA labels for dynamic content (conflict count)
- [ ] Keyboard navigation (Escape key, Tab order)
- [ ] Focus management (auto-focus first element)
- [ ] Semantic HTML (role="dialog", aria-modal="true")
- [ ] Screen reader announcements (aria-live regions)
- [ ] Color contrast ratios (WCAG AA minimum)

**Test with keyboard only:** Tab, Shift+Tab, Enter, Escape, Space

### Testing Strategy: Mock vs Integration

**CRITICAL LEARNING: Mocked tests can hide real bugs**

**What Happened in Phase 4:**
- Unit tests passed because they mocked `push_task_to_trello()`
- Method had indentation bug (not actually a class method)
- Bug was hidden until refactor-scan analysis
- Would have failed in production

**The Mocking Trap:**
```python
# Unit test with mock (passes despite bug)
@patch.object(TrelloService, 'push_task_to_trello')
def test_sync_pushes_to_trello(mock_push):
    service = TrelloService()
    task = Task(title="Test")

    service.push_task_to_trello(task, db)

    mock_push.assert_called_once()  # Passes!

# Why it passes: Mock intercepts the attribute access
# Method doesn't need to exist - mock provides it
```

**Integration Test (would have caught bug):**
```python
# Integration test without mock (fails with bug)
def test_trello_service_has_push_method():
    """Verify interface contract without mocks."""
    service = TrelloService()

    # AttributeError: 'TrelloService' object has no attribute 'push_task_to_trello'
    assert hasattr(service, 'push_task_to_trello')
    assert callable(service.push_task_to_trello)
```

**Testing Strategy: Mix Both Approaches**

```python
# tests/unit/test_trello_service_unit.py
# Unit tests with mocks (fast, isolated)
@patch('requests.post')
def test_push_to_trello_sends_correct_payload(mock_post):
    """Test business logic without external dependencies."""
    service = TrelloService()
    task = Task(title="Test", description="Description")

    service.push_task_to_trello(task, db)

    # Verify payload structure
    call_args = mock_post.call_args
    assert call_args[1]['json']['name'] == "Test"
    assert call_args[1]['json']['desc'] == "Description"

# tests/integration/test_trello_service_integration.py
# Integration tests without mocks (slower, real objects)
def test_trello_service_interface_contract():
    """Verify service implements expected interface."""
    service = TrelloService()

    # Verify methods exist and are callable
    assert hasattr(service, 'push_task_to_trello')
    assert hasattr(service, 'pull_task_from_trello')
    assert hasattr(service, '_get_status_for_list')

    # Verify method signatures
    import inspect
    sig = inspect.signature(service.push_task_to_trello)
    assert 'task' in sig.parameters
    assert 'db' in sig.parameters

def test_trello_service_with_real_objects():
    """Test with real Task and Session objects (mocked Trello API only)."""
    service = TrelloService()
    task = Task(title="Integration Test", user_id=1)
    db_session = create_test_session()

    with patch('requests.post') as mock_post:
        mock_post.return_value.status_code = 200
        mock_post.return_value.json.return_value = {'id': 'trello123'}

        # Uses real service method, real objects, mocked external API
        service.push_task_to_trello(task, db_session)

        assert task.trello_card_id == 'trello123'
```

**Testing Pyramid for Python/FastAPI:**
```
         /\
        /  \  E2E Tests (few, slow, full stack)
       /----\
      /      \  Integration Tests (some, medium, real objects)
     /--------\
    /          \  Unit Tests (many, fast, mocked dependencies)
   /------------\
```

**Guidelines:**
- **Unit tests (70%)**: Business logic, edge cases, mocked external dependencies
- **Integration tests (20%)**: Interface contracts, object interactions, mocked external APIs only
- **E2E tests (10%)**: Critical user flows, full stack including external APIs (staging only)

**Lesson:** Use mocks for external dependencies (APIs, databases), but verify internal interfaces with real objects.

### Code Quality Gates: Refactor-Scan

**CRITICAL: Static analysis catches bugs that tests miss**

**Phase 4 Quality Metrics:**
- Phase 3: 7.5/10 (baseline)
- Phase 4: 8.5/10 (significant improvement)
- **Critical bug found:** Indentation error (method nested in function)

**What Refactor-Scan Catches:**
- Structural issues (indentation, nesting)
- Complexity metrics (cyclomatic complexity)
- Code duplication
- Long methods/classes
- Poor naming conventions

**Integration into Workflow:**

```bash
# After implementing feature
pytest  # All tests pass ✓

# Run refactor-scan
refactor-scan analyze backend/app/

# Output shows quality issues
# - TrelloService.push_task_to_trello: Incorrect indentation (CRITICAL)
# - ConflictHandler: Method too long (WARNING)
# - Duplicate code in validation (INFO)

# Fix critical issues before committing
git add -A
git commit -m "fix: correct method indentation in TrelloService"
```

**Quality Thresholds:**
- **Below 7.0**: Major refactoring needed
- **7.0-8.0**: Minor improvements needed
- **8.0-9.0**: Good quality (ship it)
- **9.0+**: Excellent quality

**Lesson:** Run refactor-scan after each major feature. Don't skip code quality checks even when tests pass.

**Pre-commit Checklist Addition:**
- [ ] Refactor-scan score > 8.0: `refactor-scan analyze app/`

### Component Extraction Guidelines (React)

**When to Extract Components:**

```typescript
// Phase 4 Component: 265 lines (too long)
const TrelloConflictResolutionDialog: React.FC = () => {
  // 265 lines of JSX and logic
}

// Guidelines for extraction:
// - Component > 200 lines → Consider extraction
// - Complex state management → Extract to custom hook
// - Repeated JSX patterns → Extract to subcomponent
```

**Extraction Timing:**
1. **During feature development**: Extract when complexity becomes apparent
2. **After feature complete**: Refactor if > 200 lines
3. **During code review**: Reviewer suggests extraction

**Example Extraction:**
```typescript
// Before: 265-line monolithic component
const TrelloConflictResolutionDialog: React.FC = () => {
  const [strategy, setStrategy] = useState<Strategy>('local')
  const [selections, setSelections] = useState<Record<string, string>>({})

  const renderMergeMode = () => {
    // 100+ lines of merge UI
  }

  return (
    <Dialog>
      {strategy === 'merge_manual' && renderMergeMode()}
    </Dialog>
  )
}

// After: Extracted subcomponents
const TrelloConflictResolutionDialog: React.FC = () => {
  const [strategy, setStrategy] = useState<Strategy>('local')

  return (
    <Dialog>
      {strategy === 'merge_manual' && (
        <ManualMergeMode conflict={conflict} onResolve={handleResolve} />
      )}
    </Dialog>
  )
}

// Separate component (focused, testable)
const ManualMergeMode: React.FC<Props> = ({ conflict, onResolve }) => {
  const [selections, setSelections] = useState<Record<string, string>>({})

  // 100 lines of merge UI (now isolated)
}
```

**Balance:**
- **Avoid premature abstraction**: Don't extract before patterns emerge
- **Avoid late extraction**: Don't let components grow beyond 300 lines
- **Sweet spot**: Extract when complexity becomes apparent (~200 lines)

**Lesson:** Extract components when they have clear single responsibility, not before.

### Pre-commit Checklist for Python/SQLAlchemy

- [ ] All tests passing: `pytest`
- [ ] Test coverage > 80%: `pytest --cov=app --cov-report=term-missing`
- [ ] Type checking passing: `mypy app/`
- [ ] Code formatted: `black app/ tests/`
- [ ] Imports sorted: `isort app/ tests/`
- [ ] Linting passing: `ruff check app/ tests/`
- [ ] Refactor-scan score > 8.0: `refactor-scan analyze app/`
- [ ] No Pydantic deprecation warnings: `pytest -W error::DeprecationWarning`
- [ ] No N+1 queries (use SQLAlchemy query logging)
- [ ] Event listeners use connection.execute() not session operations
- [ ] All datetime objects use timezone.utc
- [ ] Integration tests verify interface contracts (not just mocked unit tests)
- [ ] Environment variables documented in .env.example