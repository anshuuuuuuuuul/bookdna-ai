# BookDNA AI — Database Design

## 1. Database Choice

BookDNA AI will use **PostgreSQL** as the primary relational database.

PostgreSQL is suitable because the platform needs structured data for users, books, reading activity, recommendations, analytics, and relationships between books, authors, genres, and users.

A vector index using **FAISS** will initially store and search book embeddings for semantic recommendations.

---

## 2. Core Entities

The MVP database includes these core entities:

* Users
* Books
* Authors
* Genres
* Book Genres
* User Preferences
* Reader DNA Profiles
* User Libraries
* Ratings
* Recommendations
* Recommendation Events
* Search History
* Themes
* Mascots
* Achievements
* User Achievements

---

## 3. Entity Relationships

```text
User
 ├── has one User Preference profile
 ├── has one Reader DNA profile
 ├── has many Library entries
 ├── has many Ratings
 ├── has many Search History records
 ├── receives many Recommendations
 ├── can unlock many Achievements
 └── selects one Theme and one Mascot

Book
 ├── belongs to one Author
 ├── can have many Genres
 ├── can appear in many User Library entries
 ├── can receive many Ratings
 └── can appear in many Recommendations
```

---

## 4. Tables

### Users

| Column             | Type         | Description           |
| ------------------ | ------------ | --------------------- |
| id                 | UUID         | Primary key           |
| name               | VARCHAR(100) | User display name     |
| email              | VARCHAR(255) | Unique email address  |
| password_hash      | VARCHAR(255) | Encrypted password    |
| role               | VARCHAR(20)  | `user` or `admin`     |
| selected_theme_id  | UUID         | Selected UI theme     |
| selected_mascot_id | UUID         | Selected mascot       |
| created_at         | TIMESTAMP    | Account creation time |
| updated_at         | TIMESTAMP    | Last profile update   |

---

### Authors

| Column     | Type         | Description          |
| ---------- | ------------ | -------------------- |
| id         | UUID         | Primary key          |
| name       | VARCHAR(255) | Author name          |
| bio        | TEXT         | Short biography      |
| image_url  | TEXT         | Author image URL     |
| created_at | TIMESTAMP    | Record creation time |

---

### Books

| Column         | Type         | Description            |
| -------------- | ------------ | ---------------------- |
| id             | UUID         | Primary key            |
| title          | VARCHAR(500) | Book title             |
| author_id      | UUID         | Foreign key to Authors |
| description    | TEXT         | Book summary           |
| cover_url      | TEXT         | Cover image URL        |
| published_year | INTEGER      | Publication year       |
| page_count     | INTEGER      | Number of pages        |
| average_rating | DECIMAL(3,2) | Average rating         |
| language       | VARCHAR(50)  | Book language          |
| created_at     | TIMESTAMP    | Record creation time   |

---

### Genres

| Column      | Type         | Description       |
| ----------- | ------------ | ----------------- |
| id          | UUID         | Primary key       |
| name        | VARCHAR(100) | Genre name        |
| description | TEXT         | Genre description |

---

### Book_Genres

| Column   | Type | Description           |
| -------- | ---- | --------------------- |
| book_id  | UUID | Foreign key to Books  |
| genre_id | UUID | Foreign key to Genres |

Primary key: `(book_id, genre_id)`

---

### User_Preferences

| Column           | Type        | Description                     |
| ---------------- | ----------- | ------------------------------- |
| id               | UUID        | Primary key                     |
| user_id          | UUID        | Foreign key to Users            |
| preferred_pacing | VARCHAR(30) | Slow, medium, or fast           |
| preferred_ending | VARCHAR(30) | Happy, sad, open, or plot twist |
| romance_interest | INTEGER     | Score from 1 to 5               |
| mystery_interest | INTEGER     | Score from 1 to 5               |
| fantasy_interest | INTEGER     | Score from 1 to 5               |
| horror_interest  | INTEGER     | Score from 1 to 5               |
| sci_fi_interest  | INTEGER     | Score from 1 to 5               |
| updated_at       | TIMESTAMP   | Last update time                |

---

### Reader_DNA_Profiles

| Column              | Type         | Description                  |
| ------------------- | ------------ | ---------------------------- |
| id                  | UUID         | Primary key                  |
| user_id             | UUID         | Foreign key to Users         |
| primary_personality | VARCHAR(100) | Example: Fantasy Explorer    |
| genre_scores        | JSONB        | Genre preference percentages |
| mood_scores         | JSONB        | Mood preference percentages  |
| updated_at          | TIMESTAMP    | Last update time             |

---

### User_Library

| Column           | Type        | Description                    |
| ---------------- | ----------- | ------------------------------ |
| id               | UUID        | Primary key                    |
| user_id          | UUID        | Foreign key to Users           |
| book_id          | UUID        | Foreign key to Books           |
| status           | VARCHAR(30) | Wishlist, reading, completed   |
| progress_percent | INTEGER     | Reading progress from 0 to 100 |
| started_at       | TIMESTAMP   | Reading start time             |
| completed_at     | TIMESTAMP   | Completion time                |
| created_at       | TIMESTAMP   | Library entry creation time    |

---

### Ratings

| Column      | Type      | Description          |
| ----------- | --------- | -------------------- |
| id          | UUID      | Primary key          |
| user_id     | UUID      | Foreign key to Users |
| book_id     | UUID      | Foreign key to Books |
| rating      | INTEGER   | Rating from 1 to 5   |
| review_text | TEXT      | Optional user review |
| created_at  | TIMESTAMP | Rating time          |

---

### Recommendations

| Column            | Type         | Description                  |
| ----------------- | ------------ | ---------------------------- |
| id                | UUID         | Primary key                  |
| user_id           | UUID         | Foreign key to Users         |
| book_id           | UUID         | Foreign key to Books         |
| score             | DECIMAL(5,4) | Recommendation score         |
| reason            | TEXT         | Explainable AI reason        |
| algorithm_version | VARCHAR(50)  | Recommendation model version |
| created_at        | TIMESTAMP    | Recommendation creation time |

---

### Recommendation_Events

| Column            | Type        | Description                                |
| ----------------- | ----------- | ------------------------------------------ |
| id                | UUID        | Primary key                                |
| recommendation_id | UUID        | Foreign key to Recommendations             |
| event_type        | VARCHAR(30) | Viewed, saved, ignored, started, completed |
| created_at        | TIMESTAMP   | Event time                                 |

---

### Search_History

| Column     | Type      | Description          |
| ---------- | --------- | -------------------- |
| id         | UUID      | Primary key          |
| user_id    | UUID      | Foreign key to Users |
| query      | TEXT      | Search query         |
| created_at | TIMESTAMP | Search time          |

---

### Themes

| Column        | Type         | Description                  |
| ------------- | ------------ | ---------------------------- |
| id            | UUID         | Primary key                  |
| name          | VARCHAR(100) | Theme name                   |
| mode          | VARCHAR(20)  | Light or dark                |
| mood          | VARCHAR(50)  | Cozy, magical, calm, etc.    |
| configuration | JSONB        | Theme colors and UI settings |

---

### Mascots

| Column             | Type         | Description                     |
| ------------------ | ------------ | ------------------------------- |
| id                 | UUID         | Primary key                     |
| name               | VARCHAR(100) | Mascot name                     |
| category           | VARCHAR(100) | Fantasy, romance, mystery, etc. |
| description        | TEXT         | Mascot description              |
| image_url          | TEXT         | Mascot image URL                |
| unlock_requirement | INTEGER      | Required reading XP             |

---

### Achievements

| Column            | Type         | Description                          |
| ----------------- | ------------ | ------------------------------------ |
| id                | UUID         | Primary key                          |
| name              | VARCHAR(100) | Achievement name                     |
| description       | TEXT         | Achievement details                  |
| icon_url          | TEXT         | Achievement icon                     |
| requirement_type  | VARCHAR(50)  | Books completed, streak, genre, etc. |
| requirement_value | INTEGER      | Target value                         |

---

### User_Achievements

| Column         | Type      | Description                 |
| -------------- | --------- | --------------------------- |
| user_id        | UUID      | Foreign key to Users        |
| achievement_id | UUID      | Foreign key to Achievements |
| unlocked_at    | TIMESTAMP | Unlock date                 |

Primary key: `(user_id, achievement_id)`

---

## 5. Important MVP Constraints

* A user can have only one library record per book.
* A user can rate a book only once, but can update the rating later.
* A recommendation should not repeatedly show books the user has already completed.
* User interaction events should be stored to improve future recommendations.
* Passwords must never be stored as plain text.
* Analytics must use anonymized or aggregated data where possible.

---

## 6. Future Database Additions

The following tables can be added after the MVP:

* Friendships
* Reading Clubs
* Club Discussions
* Reading Challenges
* Challenge Participation
* Notifications
* AI Chat History
* Book Scanner Results
* Publisher Analytics
