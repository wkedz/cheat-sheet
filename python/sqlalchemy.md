# SQLAlchemy 2.0

Poniżej spis informacji o sqlalchemy.

## Setup: Engine, Base, modele

```python
from typing import List, Optional
from sqlalchemy import create_engine, ForeignKey, select, func, text
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, relationship, Session

# 1) Engine (tu: in-memory SQLite)
engine = create_engine("sqlite:///:memory:", echo=False)

# 2) Deklaratywna baza
class Base(DeclarativeBase):
    pass

# 3) Modele
class User(Base):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(primary_key=True)
    email: Mapped[str] = mapped_column(unique=True, index=True)
    name: Mapped[str]

    posts: Mapped[List["Post"]] = relationship(back_populates="author", cascade="all, delete-orphan")

class Post(Base):
    __tablename__ = "posts"

    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str]
    body: Mapped[str]
    author_id: Mapped[int] = mapped_column(ForeignKey("users.id"), index=True)

    author: Mapped[User] = relationship(back_populates="posts")

# 4) Utworzenie schematu
Base.metadata.create_all(engine)
```

```SQL
CREATE TABLE users (
    id INTEGER NOT NULL PRIMARY KEY,
    email VARCHAR NOT NULL,
    name VARCHAR NOT NULL
);
CREATE UNIQUE INDEX ix_users_email ON users (email);
CREATE TABLE posts (
    id INTEGER NOT NULL PRIMARY KEY,
    title VARCHAR NOT NULL,
    body VARCHAR NOT NULL,
    author_id INTEGER NOT NULL,
    FOREIGN KEY(author_id) REFERENCES users (id)
);
CREATE INDEX ix_posts_author_id ON posts (author_id);
```

## Insert

```python
# ORM: wstaw dane startowe
with Session(engine) as session:
    u1 = User(email="anna@example.com", name="Anna")
    u2 = User(email="bob@example.com", name="Bob")
    session.add_all([
        u1,
        u2,
        Post(title="Hello", body="First!", author=u1),
        Post(title="Tips", body="SQLAlchemy 2.0 rocks", author=u1),
        Post(title="Notes", body="Minimal example", author=u2),
    ])
    session.commit()
```

```SQL
INSERT INTO users (email, name) VALUES ('anna@example.com', 'Anna');
INSERT INTO users (email, name) VALUES ('bob@example.com', 'Bob');

INSERT INTO posts (title, body, author_id)
VALUES ('Hello','First!', 1),
       ('Tips','SQLAlchemy 2.0 rocks', 1),
       ('Notes','Minimal example', 2);
```

## SELECT

### Whole objects

```python
with Session(engine) as session:
    users = session.execute(select(User).order_by(User.name)).scalars().all()
    print([u.name for u in users])  # ['Anna', 'Bob', 'Carol']
```

```sql
SELECT users.id, users.email, users.name
FROM users
ORDER BY users.name;
```

### Filtr

```python
with Session(engine) as session:
    q = select(User).where(User.email.like("%@example.com")).limit(2).offset(0)
    res = session.execute(q).scalars().all()
```

```sql
SELECT id, email, name FROM users
WHERE email LIKE '%@example.com'
LIMIT 2 OFFSET 0;
```

### Select column and aggregation

```python
with Session(engine) as session:
    q = select(User.name, func.count(Post.id).label("post_count"))\
        .join(Post, Post.author_id == User.id, isouter=True)\
        .group_by(User.id)\
        .order_by(func.count(Post.id).desc())
    rows = session.execute(q).all()
    # [('Anna', 2), ('Bob', 1), ('Carol', 0)]
```

```sql
SELECT u.name, COUNT(p.id) AS post_count
FROM users AS u
LEFT JOIN posts AS p ON p.author_id = u.id
GROUP BY u.id
ORDER BY COUNT(p.id) DESC;
```

### Eager loading

```python
from sqlalchemy.orm import selectinload

with Session(engine) as session:
    q = select(User).options(selectinload(User.posts)).where(User.name == "Anna")
    anna = session.execute(q).scalars().one()
    # anna.posts jest już pobrane efektywnie (1+1 query pattern)
```

```sql
SELECT id, email, name FROM users WHERE name = 'Anna';
SELECT id, title, body, author_id FROM posts WHERE author_id IN (1);
```

## UPDATE/DELETE

```python
with Session(engine) as session:
    bob = session.execute(select(User).where(User.name == "Bob")).scalar_one()
    bob.name = "Bobby"
    session.commit()
```

```sql
UPDATE users SET name = 'Bobby' WHERE id = 2;
```

```python
from sqlalchemy import update, delete

with engine.begin() as conn:
    conn.execute(update(User).where(User.email.like("c%")).values(name="Caroline"))
    conn.execute(delete(Post).where(Post.title == "Notes"))
```

```sql
UPDATE users SET name='Caroline' WHERE email LIKE 'c%';
DELETE FROM posts WHERE title='Notes';
```

## TRANSACTIONS

```python
try:
    with Session(engine) as session:
        # kilka operacji
        session.add(User(email="dave@example.com", name="Dave"))
        # ...
        session.commit()
except Exception:
    # w razie błędu sesja wykona rollback przy wyjściu z bloku
    raise
```

## OTHER

### Eager loading vs lazy loading

Domyślnie SQLAlchemy nie pobiera relacji od razu — dopiero, gdy ich użyjesz.
To tzw. lazy loading.

```python
with Session(engine) as s:
    user = s.scalar(select(User).where(User.name == "Anna"))
    print(user.posts)  # ← w tym momencie SQLAlchemy robi NOWE zapytanie SQL
```

```sql
--1
SELECT id, email, name FROM users WHERE name = 'Anna';
--2
SELECT id, title, body, author_id FROM posts WHERE author_id = 1;
```

Jeśli masz 100 użytkowników i dla każdego chcesz odczytać user.posts → robisz 101 zapytań (tzw. N+1 problem).
To może być dramatycznie wolne.

### Eager loading

„Eager loading” = pobierz dane powiązane od razu, jednym lub kilkoma kontrolowanymi zapytaniami.

SQLAlchemy daje Ci strategie ładowania relacji:

| Strategia                   | Co robi                                               | Liczba zapytań | Kiedy używać                                       |
| --------------------------- | ----------------------------------------------------- | -------------- | -------------------------------------------------- |
| `lazy='select'` (domyślnie) | ładuje przy pierwszym użyciu                          | N+1            | małe dane                                          |
| `selectinload()`            | robi 2 zapytania: 1 główne + 1 dla wszystkich relacji | 2              | **najczęstszy wybór**                              |
| `joinedload()`              | łączy w jeden duży JOIN                               | 1              | gdy relacji jest mało lub chcesz filtrować po nich |
| `subqueryload()`            | podobne do selectinload, ale przez subquery           | rzadziej       |                                                    |

```python
from sqlalchemy.orm import selectinload

with Session(engine) as s:
    users = s.scalars(select(User).options(selectinload(User.posts))).all()
```

```sql
SELECT id, email, name FROM users;
SELECT id, title, body, author_id FROM posts WHERE author_id IN (1, 2, 3, ...);
```

Efekt: 2 zapytania, niezależnie od liczby użytkowników.
Znika problem N+1.

#### Przykład joinedload

```python
from sqlalchemy.orm import joinedload

with Session(engine) as s:
    users = s.scalars(select(User).options(joinedload(User.posts))).all()
```

```sql
SELECT users.id, users.email, users.name,
       posts.id, posts.title, posts.body, posts.author_id
FROM users
LEFT OUTER JOIN posts ON users.id = posts.author_id;
```

### Unit of Work

To architektoniczny wzorzec, który ORM-y (jak SQLAlchemy) mają wbudowany.

Idea:
Traktuj wszystkie zmiany w ramach jednej transakcji jako jedną jednostkę pracy („unit of work”).
ORM sam „śledzi”, co zmieniłeś, i wykonuje odpowiednie SQL-e przy commit().