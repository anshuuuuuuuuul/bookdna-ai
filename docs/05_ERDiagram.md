# BookDNA AI — ER Diagram

```mermaid
erDiagram
    USERS {
        uuid id PK
        varchar name
        varchar email UK
        varchar password_hash
        varchar role
        uuid selected_theme_id FK
        uuid selected_mascot_id FK
        timestamp created_at
        timestamp updated_at
    }

    AUTHORS {
        uuid id PK
        varchar name
        text bio
        text image_url
        timestamp created_at
    }

    BOOKS {
        uuid id PK
        varchar title
        uuid author_id FK
        text description
        text cover_url
        integer published_year
        integer page_count
        decimal average_rating
        varchar language
        timestamp created_at
    }

    GENRES {
        uuid id PK
        varchar name
        text description
    }

    BOOK_GENRES {
        uuid book_id PK, FK
        uuid genre_id PK, FK
    }

    USER_PREFERENCES {
        uuid id PK
        uuid user_id FK
        varchar preferred_pacing
        varchar preferred_ending
        integer romance_interest
        integer mystery_interest
        integer fantasy_interest
        integer horror_interest
        integer sci_fi_interest
        timestamp updated_at
    }

    READER_DNA_PROFILES {
        uuid id PK
        uuid user_id FK
        varchar primary_personality
        jsonb genre_scores
        jsonb mood_scores
        timestamp updated_at
    }

    USER_LIBRARY {
        uuid id PK
        uuid user_id FK
        uuid book_id FK
        varchar status
        integer progress_percent
        timestamp started_at
        timestamp completed_at
        timestamp created_at
    }

    RATINGS {
        uuid id PK
        uuid user_id FK
        uuid book_id FK
        integer rating
        text review_text
        timestamp created_at
    }

    RECOMMENDATIONS {
        uuid id PK
        uuid user_id FK
        uuid book_id FK
        decimal score
        text reason
        varchar algorithm_version
        timestamp created_at
    }

    RECOMMENDATION_EVENTS {
        uuid id PK
        uuid recommendation_id FK
        varchar event_type
        timestamp created_at
    }

    SEARCH_HISTORY {
        uuid id PK
        uuid user_id FK
        text query
        timestamp created_at
    }

    THEMES {
        uuid id PK
        varchar name
        varchar mode
        varchar mood
        jsonb configuration
    }

    MASCOTS {
        uuid id PK
        varchar name
        varchar category
        text description
        text image_url
        integer unlock_requirement
    }

    ACHIEVEMENTS {
        uuid id PK
        varchar name
        text description
        text icon_url
        varchar requirement_type
        integer requirement_value
    }

    USER_ACHIEVEMENTS {
        uuid user_id PK, FK
        uuid achievement_id PK, FK
        timestamp unlocked_at
    }

    AUTHORS ||--o{ BOOKS : writes
    BOOKS ||--o{ BOOK_GENRES : has
    GENRES ||--o{ BOOK_GENRES : classifies

    USERS ||--|| USER_PREFERENCES : has
    USERS ||--|| READER_DNA_PROFILES : has
    USERS ||--o{ USER_LIBRARY : owns
    BOOKS ||--o{ USER_LIBRARY : appears_in

    USERS ||--o{ RATINGS : creates
    BOOKS ||--o{ RATINGS : receives

    USERS ||--o{ RECOMMENDATIONS : receives
    BOOKS ||--o{ RECOMMENDATIONS : is_recommended

    RECOMMENDATIONS ||--o{ RECOMMENDATION_EVENTS : records

    USERS ||--o{ SEARCH_HISTORY : makes

    THEMES ||--o{ USERS : selected_by
    MASCOTS ||--o{ USERS : selected_by

    USERS ||--o{ USER_ACHIEVEMENTS : earns
    ACHIEVEMENTS ||--o{ USER_ACHIEVEMENTS : unlocked_by
```

## Relationship Summary

* One author can write many books.
* A book can belong to multiple genres, and a genre can contain multiple books.
* Each user has one preference profile and one Reader DNA profile.
* A user can add many books to their library; each book can appear in many user libraries.
* A user can rate many books, and each book can receive many ratings.
* A user can receive many recommendations; each recommendation can generate multiple interaction events.
* A user can choose one theme and one mascot.
* Users can unlock multiple achievements.

```
```
