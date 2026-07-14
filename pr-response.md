# PR Response Doc � CineLog Watchlist Feature

## AI Usage

I used AI as a support tool for codebase orientation, debugging workflow, and commit-history review. I verified all code changes against the actual CineLog files and test results instead of relying on AI output alone.

## Comment 1 — Rename

**What I did:**

I renamed `save_to_watchlist()` to `add_to_watchlist()` in `services/watchlist_service.py` so the watchlist service follows the same `verb_to_noun` naming convention used elsewhere in the project, such as `add_to_collection()` in `services/collection_service.py`.

I also updated the watchlist route in `routes/watchlist/watchlist.py` so it imports and calls `add_to_watchlist()` instead of the old function name.

**How I verified:**

I searched the watchlist service and route files to confirm there were no remaining references to `save_to_watchlist`. I also ran `pytest tests/ -v` and confirmed the existing test suite still passed.

## Comment 2 — Deduplication

**What I did:**

I added deduplication logic to `add_to_watchlist()` in `services/watchlist_service.py`. The function now checks whether a `WatchlistEntry` already exists for the same `user_id` and `film_id` before creating a new row. If a duplicate exists, it raises a new `AlreadyInWatchlistError`.

I followed the same pattern used by `add_to_collection()` in `services/collection_service.py`, which checks for an existing `CollectionEntry` and raises `AlreadyInCollectionError` before inserting a duplicate.

**How I verified:**

I ran `python -m py_compile services\watchlist_service.py` to confirm the file had no syntax errors. I also ran `pytest tests/ -v` to confirm the existing test suite still passed. In addition, I manually tested the service by adding the same film to the same user's watchlist twice and confirmed the second call raised `AlreadyInWatchlistError` and only one database row existed.

## Comment 3 — Missing test

**What I did:**

I created a new file, `tests/test_watchlist.py`, for watchlist service tests. I added `test_add_to_watchlist_nonexistent_film_raises`, which verifies that calling `add_to_watchlist()` with a film ID that does not exist raises `FilmNotFoundError`.

I followed the same fixture and assertion pattern used in `tests/test_collection.py`, especially `test_add_to_collection_nonexistent_film_raises`.

I also added `test_add_to_watchlist_duplicate_raises` to cover the deduplication behavior from Comment 2. This test verifies that adding the same film twice raises `AlreadyInWatchlistError` and that only one `WatchlistEntry` exists in the database.

**How I verified:**

I ran `python -m py_compile tests\test_watchlist.py` to confirm the test file had no syntax or encoding errors. I then ran `pytest tests/test_watchlist.py -v` and confirmed both watchlist tests passed. Finally, I ran `pytest tests/ -v` and confirmed the full test suite passed with 6 tests.

## Comment 4 � Default visibility

**My position:**

**Reasoning:**

**Tradeoff acknowledged:**

## Comment 5 � Sort order

**My position:**

**Reasoning:**

**Engagement with reviewer's point:**

## Comment 6 � Rebase

**What conflicted:**

**How I resolved it:**

**How I verified no conflict remains:**

## PR Description

## Git Log Screenshot
