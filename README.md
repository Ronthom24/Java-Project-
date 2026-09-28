# Comedy Movie System

A desktop movie catalog application built with **Java Swing** and a **MySQL** backend. It lets you log in, browse a collection of movies with their cast, director, genres, language and reviews, and (for admin accounts) add, edit, and delete entries.

## Features

- **Login screen** with username/password authentication against a MySQL `accounts` table
- **Role-based access** — `admin` accounts can add/edit/delete movies; `user` accounts have read-only access
- **Movie dashboard** with summary stat cards (total movies, average rating, most recent release) and a quick-add action for admins
- **Movie table** listing title, running time, rating, release date, director, and language
- **Search** across movie title, director, and actor names
- **Movie details dialog** showing full plot, cast, genres, language, and reviews
- **Add / edit movie form** capturing title, running time, rating, release date, plot, reviews, director, language, actors, and genres (comma-separated where applicable)
- **Auto-provisioning database schema** — tables and default accounts are created automatically on first run

## Tech Stack

- Java 19
- Swing (AWT/Swing UI, no external UI framework)
- MySQL 8 (via `mysql-connector-java` 8.0.33)
- Maven for build/dependency management

## Project Structure

```
src/main/java/org/moviesystem/
├── Main.java              # Application entry point + main dashboard/table/forms UI
├── LoginFrame.java        # Login screen UI
├── dao/
│   ├── DatabaseUtil.java  # JDBC connection + schema initialization
│   ├── UserDAO.java       # Account authentication and setup
│   ├── MovieDAO.java      # Movie CRUD + actor/director/genre/language linking
│   ├── GenreDAO.java      # Genre lookups
│   └── LanguageDAO.java   # Language lookups
└── model/
    ├── Movie.java, Actor.java, Director.java, Genre.java, Language.java, Review.java, User.java
```

## Database Schema

On startup, `DatabaseUtil.initializeDatabase()` creates a `movies` database (if it doesn't already exist) with the following tables:

- `directors`, `languages`, `genres`, `actors` — lookup tables
- `movies` — core movie data, with foreign keys to `directors` and `languages`
- `movie_actors`, `movie_genres` — many-to-many junction tables

`UserDAO.initializeUsersTable()` creates an `accounts` table and seeds two default logins:

| Username | Password | Role  |
|----------|----------|-------|
| admin    | admin123 | admin |
| user     | user123  | user  |

## Prerequisites

- JDK 19+
- Maven 3.6+
- A running MySQL 8 server

## Setup

1. **Configure the database connection.** Connection details currently live in `src/main/java/org/moviesystem/dao/DatabaseUtil.java` — update `URL`, `USER`, and `PASSWORD` to match your local MySQL instance:

   ```java
   private static final String URL = "jdbc:mysql://localhost:3306/movies";
   private static final String USER = "root";
   private static final String PASSWORD = "your-password";
   ```

   > ⚠️ This file currently has a real MySQL password hardcoded and committed to the repo. Rotate that password and move credentials to an environment variable or an untracked config file before sharing or deploying this project further.

2. **Build the project:**

   ```bash
   mvn clean package
   ```

3. **Run the application:**

   ```bash
   mvn exec:java -Dexec.mainClass="org.moviesystem.LoginFrame"
   ```

   (or run `LoginFrame` directly from your IDE)

   The app will create the `movies` database, required tables, and the default `admin`/`user` accounts automatically on first launch.

4. **Log in** with `admin` / `admin123` (full access) or `user` / `user123` (read-only), then change the default passwords for anything beyond local testing.

## Notes

- This is a learning/coursework-style project — there's no password hashing, input sanitization is minimal, and the schema is created imperatively in Java rather than via migrations.
