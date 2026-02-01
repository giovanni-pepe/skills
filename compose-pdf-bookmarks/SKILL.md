---
name: compose-pdf-bookmarks
description: Implement offline PDF library, SAF import, persisted URI permissions, and page bookmarks inside the Ultima Torcia Compose app. Use when adding PDF storage, viewer UI, or bookmark/share features.
---

# Compose PDF Bookmarks

## Overview
- Ensures scanned PDFs can be imported via the Android Storage Access Framework, remembered offline, and opened directly at bookmarked pages.
- Covers Room schema, URI permission hygiene, Compose UI patterns, and validation steps.

## Workflow

### 1. Prepare dependencies
1. Add `androidx.documentfile:documentfile`, `androidx.activity:activity-compose` for `rememberLauncherForActivityResult`, and a PDF renderer (`androidx.pdf:pdf-viewer` or `android.graphics.pdf.PdfRenderer` + custom pager).
2. Request `READ_EXTERNAL_STORAGE` only if targeting SDK < 33; rely on scoped storage + SAF for current SDKs.

### 2. Model data
1. `PdfDocumentEntity(id, displayName, uriString, pageCount, importedAt)`; store persisted flag and optional thumbnail path.
2. `PdfBookmarkEntity(id, documentId, title, pageIndex, createdAt)`.
3. Provide DAO queries for listing docs, listing bookmarks by doc, and joining bookmark→document.

### 3. Import PDFs
1. Launch `ACTION_OPEN_DOCUMENT` with MIME `application/pdf`, `extraAllowMultiple = true`.
2. On result, call `contentResolver.takePersistableUriPermission(uri, flags)`; catch `SecurityException` to request again.
3. Read metadata: `DocumentFile.fromSingleUri`, `ParcelFileDescriptor` + `PdfRenderer` to compute page count.
4. Insert/replace document entry; if URI exists, update metadata but keep bookmarks.

### 4. Display library
1. Compose `PdfLibraryScreen` shows a lazy column of PDFs with thumbnail/icon, size, last opened.
2. Provide overflow menu: rename label, remove (revoking permission), share original URI (if permitted).
3. Keep bookmarks visible either inline (chips) or via nested navigation item.

### 5. Bookmark management
1. In viewer, surface `Add Bookmark` FAB that captures current page index and prompts for title.
2. Save to Room and update `ViewModel` state; UI should reflect new bookmark immediately.
3. Duplicates: allow same page with different labels, but show conflict warning when identical label+page exists.

### 6. PDF viewing + navigation
1. Use `PdfRenderer` inside `remember` block; ensure file descriptor is closed in `DisposableEffect`.
2. For large files, implement pager with `LazyRow`/`Pager` and caching bitmaps using `rememberSaveable`.
3. When user taps a bookmark, open viewer route with nav args `{documentId, pageIndex}` and scroll/jump to that page.
4. Provide `ZoomablePdfPage` (double-tap to zoom, pinch gestures) for readability.

### 7. Permission + lifecycle hygiene
1. Handle `onTrimMemory` / low storage by clearing cached bitmaps stored under app cache.
2. When deleting a PDF, revoke persisted URI permission (`releasePersistableUriPermission`).
3. On app start, verify stored URIs by resolving them; mark unavailable docs and prompt user to relink.

### 8. Testing + validation
1. JVM tests: DAO roundtrips, bookmark filtering, URI persistence helpers.
2. Instrumented tests: fake SAF via `Intent` instrumentation or `DocumentsProvider` test double to ensure import/export flows.
3. Manual checklist: import > open offline > add bookmark > relaunch app > jump to page; remove PDF and confirm bookmarks handled gracefully.

Use this skill any time PDF import/bookmark flows change to keep offline guarantees intact.
