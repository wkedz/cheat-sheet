# 🔗 `secondary` w SQLAlchemy — relacje wiele-do-wielu (many-to-many)

## 🧠 Co robi `secondary`

`secondary` w funkcji `relationship()` mówi SQLAlchemy:

> „Użyj tej *tabeli pośredniej*, aby połączyć dwa modele w relacji wiele-do-wielu.”

Innymi słowy:
- **Baza danych** ma trzecią tabelę, która przechowuje powiązania między dwoma innymi tabelami.
- **SQLAlchemy ORM** używa jej automatycznie, żebyś mógł pisać w Pythonie naturalnie:
  ```python
  player.teams.append(team)
  ```
  zamiast ręcznie wstawiać rekord do tabeli pośredniej.

---

## 🧩 Klasyczny przykład

### Tabele:
- `student`
- `course`
- `student_course` (tabela pośrednia — tylko klucze obce)

### Kod

```python
from sqlalchemy import Table, Column, Integer, String, ForeignKey
from sqlalchemy.orm import relationship, DeclarativeBase

class Base(DeclarativeBase):
    pass

# Tabela pośrednia
student_course = Table(
    "student_course",
    Base.metadata,
    Column("student_id", ForeignKey("student.id"), primary_key=True),
    Column("course_id", ForeignKey("course.id"), primary_key=True)
)

class Student(Base):
    __tablename__ = "student"
    id = Column(Integer, primary_key=True)
    name = Column(String)

    courses = relationship(
        "Course",
        secondary=student_course,       # ⬅️ tabela pośrednia
        back_populates="students"
    )

class Course(Base):
    __tablename__ = "course"
    id = Column(Integer, primary_key=True)
    title = Column(String)

    students = relationship(
        "Student",
        secondary=student_course,
        back_populates="courses"
    )
```

---

## 📊 Struktura SQL

```sql
CREATE TABLE student (
    id INTEGER PRIMARY KEY,
    name TEXT
);

CREATE TABLE course (
    id INTEGER PRIMARY KEY,
    title TEXT
);

CREATE TABLE student_course (
    student_id INTEGER,
    course_id INTEGER,
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (student_id) REFERENCES student(id),
    FOREIGN KEY (course_id) REFERENCES course(id)
);
```

---

## ⚙️ Jak to działa

```python
anna = Student(name="Anna")
math = Course(title="Math")
bio = Course(title="Biology")

# ORM używa tabeli pośredniej automatycznie
anna.courses.append(math)
anna.courses.append(bio)

session.add(anna)
session.commit()
```

SQLAlchemy wykona:

```sql
INSERT INTO student_course (student_id, course_id) VALUES (1, 1);
INSERT INTO student_course (student_id, course_id) VALUES (1, 2);
```

Nie trzeba wstawiać tych rekordów ręcznie.

---

## ⚠️ Gdy tabela pośrednia ma dodatkowe kolumny

Jeśli tabela pośrednia ma **dodatkowe dane** (np. `date_joined`, `role`, `last_changed_date`),
to samo `secondary` nie wystarczy.  
Wtedy stosujemy **association object pattern**.

### Przykład z klasą pośrednią

```python
class TeamPlayer(Base):
    __tablename__ = "team_player"
    team_id = Column(Integer, ForeignKey("team.id"), primary_key=True)
    player_id = Column(Integer, ForeignKey("player.id"), primary_key=True)
    last_changed_date = Column(Date, nullable=False)

    team = relationship("Team", back_populates="team_players")
    player = relationship("Player", back_populates="team_players")

class Player(Base):
    __tablename__ = "player"
    id = Column(Integer, primary_key=True)
    name = Column(String)
    team_players = relationship("TeamPlayer", back_populates="player")

class Team(Base):
    __tablename__ = "team"
    id = Column(Integer, primary_key=True)
    name = Column(String)
    team_players = relationship("TeamPlayer", back_populates="team")
```

Wtedy można np. ustawić `last_changed_date`:

```python
TeamPlayer(team=team, player=player, last_changed_date=date.today())
```

---

## 🧭 TL;DR — Podsumowanie

| Co | Znaczenie |
|----|------------|
| `secondary` | Określa **tabelę pośrednią** używaną przez relację wiele-do-wielu |
| Wartość | Obiekt `Table` lub nazwa tabeli (`str`) |
| Kiedy używać | Gdy tabela pośrednia zawiera **tylko klucze obce** |
| Gdy ma dodatkowe kolumny | Użyj **association object pattern** (pełna klasa ORM) |
| `back_populates` | Łączy obie strony relacji i synchronizuje je w pamięci |

---

## 🧩 Przykład z życia

W modelu sportowym:
```python
Player.teams = relationship("Team", secondary="team_player", back_populates="players")
Team.players = relationship("Player", secondary="team_player", back_populates="teams")
```
➡️ SQLAlchemy automatycznie wypełnia tabelę `team_player`, kiedy:
```python
team.players.append(player)
```

---

## 📚 Wniosek

- `secondary` to **narzędzie ORM**, nie bazy danych.  
- Ułatwia pracę z relacjami wiele-do-wielu, gdy tabela łącząca nie ma własnych danych.  
- Jeśli ma dodatkowe kolumny → przekształć ją w pełnoprawny model.
