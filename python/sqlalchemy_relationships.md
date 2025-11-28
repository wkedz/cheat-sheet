# 🧩 `relationship()` i `back_populates` w SQLAlchemy 2.0

Zrozumienie `relationship()` i `back_populates` to klucz do pracy z powiązanymi tabelami w sposób naturalny dla Pythona, a nie czysto SQL-owy.

---

## 1. Co to jest `relationship()`

`relationship()` tworzy **powiązanie między dwiema klasami ORM** (czyli tabelami).  
Nie tworzy kolumny w bazie danych — służy do **nawigowania po relacji** w kodzie.

### Przykład

```python
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, relationship
from sqlalchemy import ForeignKey
from typing import List

class Base(DeclarativeBase):
    pass

class User(Base):
    __tablename__ = "users"
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str]

    # Relacja "jeden do wielu" (User → Post)
    posts: Mapped[List["Post"]] = relationship(back_populates="author")

class Post(Base):
    __tablename__ = "posts"
    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str]
    author_id: Mapped[int] = mapped_column(ForeignKey("users.id"))

    # Strona odwrotna relacji
    author: Mapped["User"] = relationship(back_populates="posts")
```

### Co się dzieje?

- `ForeignKey("users.id")` mówi bazie: *każdy post ma autora (User.id)*.
- `relationship()` mówi ORM-owi: *pozwól mi przejść od jednego obiektu do drugiego w Pythonie*.

```python
user = User(name="Anna")
post = Post(title="Hello", author=user)  # przypisanie obiektu, nie id!

print(post.author.name)  # "Anna"
print(user.posts)        # [<Post(title='Hello')>]
```

ORM sam wypełni `author_id` (FK) w czasie zapisu do bazy.  
Nie musisz tego robić ręcznie.

---

## 2. Co robi `back_populates`

`back_populates` = **powiązanie dwóch stron relacji** (dwukierunkowa synchronizacja).  

- Jedna strona ma `relationship(..., back_populates="druga_strona")`.
- Druga strona ma `relationship(..., back_populates="pierwsza_strona")`.

Dzięki temu, gdy dodasz obiekt z jednej strony, ORM automatycznie aktualizuje drugą.

### Przykład

```python
user = User(name="Anna")
post = Post(title="Hello")

# powiąż obiekty z jednej strony
user.posts.append(post)

print(post.author is user)   # True — automatycznie powiązane w drugą stronę!
```

Bez `back_populates`, to powiązanie byłoby **jednostronne** — ORM nie wiedziałby, że `user.posts` i `post.author` odnoszą się do tej samej relacji.

---

## 3. Typy relacji (ściągawka)

| Typ relacji | W SQL | W ORM |
|--------------|--------|-----------|
| **One-to-Many** | `posts.user_id → users.id` | `User.posts = relationship(Post, back_populates="author")` |
| **Many-to-One** | jw. | `Post.author = relationship(User, back_populates="posts")` |
| **One-to-One** | jak One-to-Many, ale `uselist=False` | `relationship(..., uselist=False)` |
| **Many-to-Many** | tabela pośrednia (association table) | `relationship(secondary=association_table, back_populates=...)` |

---

## 4. Co *nie* robi `relationship()`

- Nie tworzy kolumny w tabeli (to robi `mapped_column(ForeignKey(...))`).
- Nie przechowuje wartości w bazie – to tylko powiązanie logiczne między obiektami ORM.
- Nie pobiera danych od razu – domyślnie działa **lazy loading** (czyli dociąga dane przy pierwszym użyciu).

---

## 5. Praktyczny przykład (pełny mini-demo)

```python
from sqlalchemy import create_engine, ForeignKey
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, relationship, Session
from typing import List

engine = create_engine("sqlite:///:memory:", echo=True)

class Base(DeclarativeBase): pass

class User(Base):
    __tablename__ = "users"
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str]
    posts: Mapped[List["Post"]] = relationship(back_populates="author")

class Post(Base):
    __tablename__ = "posts"
    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str]
    author_id: Mapped[int] = mapped_column(ForeignKey("users.id"))
    author: Mapped["User"] = relationship(back_populates="posts")

Base.metadata.create_all(engine)

with Session(engine) as s:
    anna = User(name="Anna")
    post = Post(title="Hello", author=anna)  # przypisanie relacji
    s.add(anna)
    s.commit()

    print(anna.posts)  # [Post(id=1, title='Hello')]
```

W logach zobaczysz, że ORM sam wstawi poprawne `author_id`.

---

## 6. TL;DR

| Pojęcie | Co robi | Kluczowy efekt |
|----------|----------|----------------|
| `ForeignKey()` | definiuje relację w **bazie danych** | mówi, że kolumna odnosi się do innej tabeli |
| `relationship()` | definiuje relację w **ORM / Pythonie** | pozwala przechodzić między obiektami |
| `back_populates` | wiąże dwie strony relacji | zapewnia synchronizację w obie strony |
| `uselist=False` | zmienia listę w pojedynczy obiekt | dla relacji 1:1 |
