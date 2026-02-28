# Project Context: Agent-stuff

## Overview
This repository contains Claude Code skills, plugins, and agents for use across various projects. Primary tech stack includes Python, Java, and React/Angular.

## Project Structure
```
agent-stuff/
├── skills/              # Claude Code skills
│   └── commit/          # Git commit skill (Conventional Commits)
├── CLAUDE.1md          # This file - Claude assistant guidelines
└── README.md           # Project documentation
```

## Tech Stack
- **Primary Languages**: Python, Java, JavaScript/TypeScript (React/Angular)
- **Version Control**: Git with Conventional Commits format
- **Claude Code**: Skills framework for custom commands

## Development Guidelines

### Python Best Practices
When working with Python code in this project:

#### Type Hints
Always use type hints for function signatures and class attributes:
```python
from typing import Optional, List, Dict
from dataclasses import dataclass

@dataclass
class User:
    id: int
    name: str
    email: str

def fetch_users(
    limit: int,
    active_only: bool = False
) -> List[User]:
    """Fetch users from the database."""
    pass

async def get_user_by_id(
    user_id: int
) -> Optional[User]:
    """Retrieve a single user by ID, or None if not found."""
    pass
```

#### Docstrings
Use Google-style or NumPy-style docstrings for non-trivial functions:
```python
def calculate_discount(
    price: float,
    discount_percent: float,
    is_vip: bool = False
) -> float:
    """Calculate the final price after applying discount.

    Args:
        price: Original price of the item.
        discount_percent: Discount percentage (0-100).
        is_vip: Whether the customer has VIP status (adds 5% bonus).

    Returns:
        Final price after discount is applied.

    Raises:
        ValueError: If discount_percent is not between 0 and 100.
    """
    if not 0 <= discount_percent <= 100:
        raise ValueError("Discount must be between 0 and 100")

    if is_vip:
        discount_percent += 5

    return price * (1 - discount_percent / 100)
```

#### Error Handling
Use specific exceptions and proper exception chaining:
```python
import logging

logger = logging.getLogger(__name__)

class UserNotFoundError(Exception):
    """Raised when a user cannot be found."""

def get_user_email(user_id: int) -> str:
    """Get user email with proper error handling."""
    try:
        user = db.query(User).filter_by(id=user_id).one()
        return user.email
    except NoResultFound as e:
        logger.error(f"User {user_id} not found")
        raise UserNotFoundError(f"User {user_id} does not exist") from e
    except DatabaseError as e:
        logger.error(f"Database error fetching user {user_id}")
        raise  # Re-raise for handler to deal with
```

#### Modularity and DRY
Extract reusable logic into separate functions/modules:
```python
# ❌ Bad - repeated logic
def process_order_a(order: Order) -> float:
    total = sum(item.price for item in order.items)
    tax = total * 0.08
    shipping = total * 0.05
    return total + tax + shipping

def process_order_b(order: Order) -> float:
    total = sum(item.price for item in order.items)
    tax = total * 0.08
    shipping = total * 0.03  # Different rate
    return total + tax + shipping

# ✅ Good - DRY principle
def calculate_subtotal(order: Order) -> float:
    return sum(item.price for item in order.items)

def calculate_total(
    subtotal: float,
    tax_rate: float = 0.08,
    shipping_rate: float = 0.05
) -> float:
    return subtotal * (1 + tax_rate + shipping_rate)

def process_order(order: Order, shipping_rate: float = 0.05) -> float:
    subtotal = calculate_subtotal(order)
    return calculate_total(subtotal, shipping_rate=shipping_rate)
```

#### Context Managers
Use context managers for resource management:
```python
from contextlib import contextmanager

@contextmanager
def database_transaction(session):
    """Manage database transaction with automatic rollback on error."""
    try:
        yield session
        session.commit()
    except Exception:
        session.rollback()
        raise
    finally:
        session.close()

# Usage
with database_transaction(session) as db:
    user = db.query(User).first()
    user.name = "Updated"
```

#### List Comprehensions and Generators
Prefer comprehensions and generators for readability and efficiency:
```python
# List comprehension - when you need the full list
active_user_ids = [
    user.id for user in users
    if user.is_active
]

# Generator expression - for large datasets or one-time iteration
total_revenue = sum(
    order.total for order in orders
    if order.status == "completed"
)

# Dictionary comprehension
user_by_email = {
    user.email: user
    for user in users
    if user.email
}

# ❌ Avoid nested comprehensions - use a loop instead
# Bad:
matrix = [[x * y for y in range(5)] for x in range(5)]

# Good - more readable
def create_matrix(size: int) -> List[List[int]]:
    matrix = []
    for x in range(size):
        row = []
        for y in range(size):
            row.append(x * y)
        matrix.append(row)
    return matrix
```

#### Dataclasses Over Plain Classes
Use dataclasses for data containers:
```python
from dataclasses import dataclass, field
from typing import ClassVar

@dataclass
class Product:
    """Product entity with automatic __init__, __repr__, __eq__."""
    id: int
    name: str
    price: float
    tags: List[str] = field(default_factory=list)
    created_at: datetime = field(default_factory=datetime.now)

    TAX_RATE: ClassVar[float] = 0.08

    @property
    def price_with_tax(self) -> float:
        return self.price * (1 + self.TAX_RATE)
```

- Follow PEP 8 style guidelines
- Use virtual environments (prefer `uv` for modern projects)
- Apply linters: Ruff (formatting/linting), Mypy (type checking)
- Write tests following TDD principles when appropriate

### Git Commit Convention
Use the `/commit` skill for all commits. Format:
```
<jira><type>(<scope>): <summary>
```
- `jira`: REQUIRED (e.g., PROJ-123)
- `type`: feat, fix, docs, refactor, chore, test, perf
- `scope`: OPTIONAL - affected area (e.g., api, parser, ui)
- `summary`: REQUIRED - imperative mood, <= 72 chars, no period

### Available Skills
- `/commit` - Create git commits with Conventional Commits format
- More skills can be added to the `skills/` directory

## Assistant Guidelines

### 1. Plan Mode Default
- Enter plan mode for any non-trivial task (3+ steps or architectural decisions)
- Stop and re-plan immediately if something goes wrong
- Use plan mode for verification, not just implementation
- Write detailed specifications upfront to reduce ambiguity

### 2. Subagent Strategy
- Use subagents liberally to keep main context window clean
- Offload research, exploration, and parallel analysis
- Assign one task per subagent for focused execution

### 3. Self-Improvement Loop
- After user corrections, update tasks/lessons.md with relevant patterns
- Create rules to prevent repeating mistakes
- Review lessons at session start when relevant

### 4. Verification Before Done
- Never mark task complete without proving it works
- Ask: “Would a staff engineer approve this?”
- Run tests, check logs, demonstrate correctness

### 5. Demand Elegance (Balanced)
- For non-trivial changes, consider if there's a more elegant solution
- Implement the solution you'd choose knowing everything you know now
- Don't over-engineer simple fixes
- Critically evaluate your own work before presenting

### 6. Autonomous Bug Fixing
- Fix bugs without unnecessary guidance
- Review logs, errors, failing tests, then resolve
- Avoid requiring context switching from the user
- Fix failing CI tests proactively

## Task Management
1. **Plan First**: Write plan to tasks/todo.md with checkable items
2. **Verify Plan**: Review before starting implementation
3. **Track Progress**: Mark items complete as you go
4. **Explain Changes**: Provide high-level summary at each step
5. **Document Results**: Add review section to tasks/todo.md
6. **Capture Lessons**: Update tasks/lessons.md after corrections

## Core Principles
- **Simplicity First**: Make changes as simple as possible, minimize impact
- **No Laziness**: Identify root causes, avoid temporary fixes
- **Minimal Impact**: Touch only what's necessary, avoid new bugs