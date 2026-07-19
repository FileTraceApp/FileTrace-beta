# FileTrace Beta

FileTrace is a native Windows media management utility built for people with real libraries: TV shows, anime, movies, music, photos, installers, documents, and the messy download folders that collect all of them.

The beta goal is simple: help power users clean, rename, enrich, and route large file collections without turning their folders into a guessing game. FileTrace focuses on preview-first workflows, metadata-aware naming, safe execution, undo support, and high-performance Windows integration.

> Beta note: FileTrace is currently being tested with a small group of power users. The first beta licenses are free for testers who are willing to report issues, edge cases, and workflow feedback.

## Table Of Contents

- [Installation Notes](#installation-notes)
- [Organize And Rename Modes](#organize-and-rename-modes)
- [Smart Sort](#smart-sort)
- [Watchdog](#watchdog)
- [Settings, API Keys, And Naming Formats](#settings-api-keys-and-naming-formats)
- [Cleanup Tools](#cleanup-tools)
- [Privacy And Power Tools](#privacy-and-power-tools)
- [Beta Feedback](#beta-feedback)
- [Legal](#legal)

## Installation Notes

Because I'm a solo indie developer and this is a brand-new Beta, Windows SmartScreen might flag the installer. This happens simply because the app hasn't been downloaded by thousands of people yet, and I haven't purchased an expensive Enterprise EV Certificate!

If you get a blue "Windows protected your PC" popup, don't worry. Here is how to proceed:

Click More info.

Click the Run anyway button that appears at the bottom.

(Note: As the app gets downloaded more by the community, Windows will build trust with the file and this warning will naturally go away!)

## Organize And Rename Modes

Organize and Rename are the two everyday tools in FileTrace. They share the same preview-first workspace, but they do different jobs:

- **Organize** moves files into clean folder structures.
- **Rename** changes file names in place or while organizing, using the same metadata-aware rules.

Both modes are designed around the same safety loop:

1. Pick a folder.
2. Choose what kind of files you are working with.
3. Preview every planned change.
4. Execute only after the preview looks right.
5. Undo if the result is not what you wanted.

### How To Use Organize

Open FileTrace and choose **Organize** from the sidebar.

Click **Browse** and select the folder you want to clean up. You can also paste a path directly into the folder field if you already know it.

Choose the mode that matches the folder:

- **Mixed Folder** for downloads, desktop dumps, and folders containing many file types.
- **TV Series** for episodic TV.
- **Anime** for anime episodes or anime movie folders.
- **Movies** for movie folders or loose movie files.
- **Photos** for camera rolls, screenshots, and image collections.
- **Audio** for music libraries.
- **Installers** for setup files and software packages.
- **General** for custom file renaming and basic cleanup.

For TV Series, Anime, and Movies, Organize can use your saved folder patterns from Settings. For example, a TV folder pattern such as:

```text
TV Series\{Genre}\{ShowTitle} ({ReleaseYear})\Season {Season:00}\
```

can route a file into a clean structure like:

```text
TV Series\Drama\Breaking Bad (2008)\Season 01\
```

If you enable **Download Poster**, FileTrace downloads the main media poster from TMDB when metadata is available. If you enable **Download Folder Icon**, FileTrace converts that poster into a Windows folder icon so the sorted folders look good in File Explorer. FileTrace also writes media-server friendly `.nfo` files for supported movie and series metadata.

For TV and Anime, **Use Official Episode Names** tells FileTrace to look up episode titles when possible, so a numbered episode can become something readable like:

```text
Breaking Bad - S01E01 - Pilot.mkv
```

After choosing the mode, click **Preview Changes**. FileTrace will show the old path and the proposed new path before touching the files.

You can also click **Preview on Drive**. This mounts a temporary preview drive that lets you browse the planned result directly in File Explorer. Nothing is physically moved yet. If you rename folders or move items inside the preview drive, FileTrace mirrors those edits when you click **Apply**. If you cancel or preview again, the temporary drive is removed and its generated sidecars are cleaned up.

When the preview looks right, click **Apply**. If you need to roll back, use **Undo**.

### How To Use Rename

Open FileTrace and choose **Rename** from the sidebar.

Rename uses the same mode selector as Organize, but the focus is file names rather than only folder placement.

For media folders, Rename can combine folder structure and file naming patterns. A movie file pattern such as:

```text
{MovieTitle} ({ReleaseYear}) [{Resolution}]
```

can turn a rough file name into:

```text
Dune Part Two (2024) [2160p].mkv
```

For TV and Anime, Rename can use season, episode, absolute episode, official episode title, codecs, resolution, release year, and other tokens defined in Settings. This lets you keep consistent names across an entire library instead of manually fixing every folder.

For general file cleanup, Rename also supports practical batch tools such as:

- Prefix and suffix text.
- Numbered sequences.
- Find and replace.
- Optional regular expressions.
- Extension filters.
- Duplicate handling.

The most important rule is the same: always click **Preview Changes** first. You can also use **Preview on Drive** when you want to inspect the output as a real folder tree before applying it. FileTrace is built so you can inspect the result before it changes your files.

### Recommended Workflows

#### Clean Up A Movie Folder

1. Open **Organize**.
2. Select the movie folder or a parent folder containing movie folders.
3. Choose **Movies**.
4. Enable **Download Poster** and **Download Folder Icon** if you want media artwork.
5. Click **Preview Changes**.
6. Confirm the movie titles, years, and folders are correct.
7. Click **Apply**.

#### Rename A TV Season

1. Open **Rename**.
2. Select the show or season folder.
3. Choose **TV Series**.
4. Enable **Use Official Episode Names**.
5. Click **Preview Changes**.
6. Check that season and episode numbers are correct.
7. Click **Apply**.

#### Organize A Mixed Download Folder

1. Open **Organize**.
2. Select the messy folder.
3. Choose **Mixed Folder**.
4. Choose how to group files.
5. Click **Preview Changes**.
6. Apply when the category layout looks right.

### What FileTrace Preserves

When organizing media, FileTrace tries to keep useful sidecar files with the media instead of dropping them:

- Existing subtitles such as `.srt`, `.ass`, and `.vtt`.
- Existing poster images such as `poster.jpg`, `folder.jpg`, or cover art.
- Existing `.nfo` files.
- Subtitle folders such as `subs`, `sub`, or `subtitles`.

Automated subtitle downloading has been removed from the beta and version 1 release so FileTrace can focus on the core sorting, routing, and metadata engine. It is planned to return in a future update. If subtitles already exist beside your media, FileTrace will still move and rename them with the matching file.

### Technical Overview

Organize and Rename are powered by a shared preview planner and executor.

When you click **Preview Changes**, FileTrace builds a structured plan instead of immediately moving files. The planner reads the selected UI mode, saved naming patterns, folder hierarchy patterns, metadata options, duplicate rules, and safety settings. It then produces preview items containing the original path, proposed target path, conflict state, generated poster targets, folder icon targets, and `.nfo` metadata when available.

The media naming system is based on a custom C# template resolver. Anything inside braces is treated as a dynamic token, while everything outside braces is treated as literal text. This allows patterns such as:

```text
{ShowTitle} - S{Season:00}E{Episode:00} - {EpisodeTitle}
```

The resolver supports C#-style numeric padding like `{Season:00}` and cleans blank-token artifacts, so optional values do not leave ugly output such as empty brackets or dangling separators.

For TV, Anime, and Movies, FileTrace can enrich names and folder structures using TMDB metadata. The planner uses metadata to resolve titles, years, genres, posters, season artwork, episode names, and TMDB IDs. Resolution is determined from media metadata first, with filename fallback only when media metadata is missing.

For artwork, FileTrace uses the main poster from TMDB, not random uploaded images. The same poster can be saved as `poster.jpg` and converted into a Windows `.ico` folder icon. Season folders can also receive season-specific artwork when available.

For media-server compatibility, FileTrace generates Kodi/Jellyfin-style `.nfo` files for supported movies and series. Existing `.nfo` files are preserved instead of overwritten.

The execution layer is intentionally conservative:

- File operations are previewed before execution.
- Conflicts can be detected before files are touched.
- Duplicate names can be appended, skipped, or overwritten depending on the selected mode.
- Undo logs are written so recent operations can be reverted.
- Empty folders left behind by reorganization are cleaned up when safe.

This architecture makes Organize and Rename useful for small batches, but it is also built for very large collections where mistakes are expensive.

## Smart Sort

Smart Sort is FileTrace's large-library sorting engine. It is designed for the moment when you do not want to manually pick "TV Series," "Movies," "Anime," "Audio," or "Games" one folder at a time.

Point Smart Sort at a folder, a media dump, or an entire drive, and FileTrace analyzes the contents, classifies files, builds a proposed structure, and lets you preview the result before execution.

### How To Use Smart Sort

Open FileTrace and choose **Smart Sort** from the sidebar.

Click **Browse** and select the folder or drive you want FileTrace to analyze. You can use a normal folder such as:

```text
D:\Downloads
```

or a broader root such as:

```text
D:\
```

Smart Sort is built for big messy inputs, so it can scan nested folders and mixed libraries.

After selecting the path, click **Preview Changes**.

FileTrace will classify the files and show a preview tree. The preview is the important part: it shows where files will appear before the sort is executed.

Smart Sort has the same preview-first control style as Organize and Rename. The main options are:

- **Apply Folder Context**
- **Deduplicate Files**
- **Mount Virtual Drive**
- **Sort In Place**
- **Rename Files**
- **Use Official Episode Names**
- **Download Poster**
- **Download Folder Icon**
- **Generate .nfo files**

You can enable more than one action depending on how you want to work.

Click **Apply** only after the preview is ready and looks correct. Apply is disabled until FileTrace has a current preview plan.

### Mount Virtual Drive

**Mount Virtual Drive** is the safest way to test a large organization plan.

When enabled, FileTrace builds a virtual organized library without physically moving your original files. The files appear through a mounted virtual drive, usually using FileTrace's virtual drive layer, while the real files remain where they are.

This is useful when you want to inspect a large reorganization before committing to it.

Example:

```text
Original file:
D:\Downloads\House.S01E01.mkv

Virtual view:
Series\Drama\House (2004)\Season 01\House - S01E01 - Pilot.mkv
```

From the virtual drive, you can browse the organized result in File Explorer. FileTrace also supports virtual-drive metadata sidecars such as posters, icons, and `.nfo` files when metadata is available.

### Preview On Drive

**Preview on Drive** is different from **Mount Virtual Drive**.

Preview on Drive creates a temporary preview drive from the current preview plan. It is meant for inspection and manual adjustment before execution. You can open the drive in File Explorer, rename a folder, or move a few planned items around. When you click **Apply**, FileTrace reads the temporary preview structure and applies those edits to the real operation.

This is useful when automatic metadata gets most of the library right but you want to polish a few folder names before committing.

If you do not apply the run, the temporary preview drive and temporary generated sidecars are removed.

### Sort In Place

**Sort In Place** physically moves files into the organized structure.

Use this when you are happy with the preview and want the folder on disk to be cleaned up.

Smart Sort writes a persistent History transaction for physical moves, so you can use **Undo** or the **History** page after a run if you need to restore files to their previous locations.

### Rename Files

**Rename Files** tells Smart Sort to apply your saved media naming patterns while sorting.

Without Rename Files, Smart Sort focuses on folder placement.

With Rename Files enabled, Smart Sort can also produce clean media names such as:

```text
House - S01E01 - Pilot.mkv
Dune Part Two (2024) [2160p].mkv
```

This uses the same naming and folder format engine as the normal Rename and Organize modes, including your saved patterns from Settings.

### Recommended Workflows

#### Preview A Huge Library Without Moving Anything

1. Open **Smart Sort**.
2. Select the folder or drive.
3. Leave **Mount Virtual Drive** enabled.
4. Click **Preview Changes**.
5. Review the preview tree.
6. Click **Apply**.
7. Browse the mounted virtual drive and inspect the result.

This is the recommended first run for very large folders.

#### Commit A Clean Sort To Disk

1. Open **Smart Sort**.
2. Select the folder or drive.
3. Click **Preview Changes**.
4. Review the preview.
5. Enable **Sort In Place**.
6. Optionally enable **Rename Files**.
7. Click **Apply**.

Use this when you are ready for FileTrace to physically move files.

#### Rename And Mount A Virtual Library

1. Open **Smart Sort**.
2. Select your source folder.
3. Click **Preview Changes**.
4. Enable **Mount Virtual Drive**.
5. Enable **Rename Files**.
6. Click **Apply**.

This gives you a polished virtual library while leaving the original files in place.

### What Smart Sort Detects

Smart Sort is designed to classify common power-user collections, including:

- TV series.
- Anime episodes.
- Anime movies.
- Movies.
- Music.
- Games.
- Junk or unsupported leftovers.

For the beta, Smart Sort is focused on media, music, and games. Cleanup Tools handle junk, partial downloads, duplicate files, and OS artifacts separately.

Smart Sort also understands context. For example, a folder named `Anime` is treated as a useful hint, but not the final answer. A normal 20-25 minute video under an anime branch can be treated as anime, while a long 70+ minute video under the same branch can be treated as an anime movie.

### Sidecars And Artwork

Smart Sort keeps useful companion files with the media:

- Subtitles beside the media file.
- Subtitle folders such as `subs` and `subtitles`.
- Posters and cover images.
- Existing `.nfo` metadata files.

When metadata is available, FileTrace can also generate:

- `poster.jpg`
- season `folder.jpg`
- `movie.nfo`
- `tvshow.nfo`
- `anime.nfo`
- Windows folder icons from poster artwork

This makes the output friendlier for File Explorer, Kodi, Jellyfin, Plex-style libraries, and other media-server workflows.

### Technical Overview

Smart Sort is a multi-stage classification and assembly pipeline.

The first stage is analysis. FileTrace scans the selected root and classifies files using a combination of file extensions, folder context, media metadata, filename parsing, and category dictionaries. For video files, it uses media metadata where possible, including duration and resolution. Resolution detection is width-first, so cropped 1920x800 Blu-ray rips are still treated as 1080p instead of being mislabeled as 720p.

The second stage is planning. Smart Sort creates an organized move plan under an internal `_Organized` structure. This plan does not have to be the final physical result; it is an intermediate representation that lets FileTrace preview, post-process, and mount the organized tree consistently.

The third stage is post-organization. For media categories such as Series, Anime, Anime Movies, Movies, Music, and Programs, FileTrace can pass the initial Smart Sort result back through the same planner used by Organize and Rename. This is what allows Smart Sort to use the user's saved folder hierarchy and file naming formats instead of relying on a hardcoded output layout.

For example, Smart Sort might first classify a file as:

```text
Series\Drama\House\Season 1\episode.mkv
```

Then the post-organize planner can reshape it into the user's saved pattern:

```text
Series\Drama\House (2004)\Season 01\House - S01E01 - Pilot.mkv
```

The fourth stage is execution. Depending on the selected actions, FileTrace can:

- create a mounted virtual namespace,
- physically move files,
- rename files using saved patterns,
- generate sidecars,
- apply folder icons,
- and update undo logs.

The virtual drive path is one of the most important parts of FileTrace. Instead of requiring a full physical move, FileTrace builds a persistent virtual namespace that maps clean virtual paths to the original physical files. The virtual namespace is stored and can be restored later, so a large organized view can survive app restarts.

To keep the mounted view responsive, FileTrace tracks virtual namespace entries and updates them when physical paths change. This includes live updates from the Windows file system layer, so renamed or moved source files can be reflected in the virtual drive without rebuilding the entire library.

### Smart Sort True Sync

True Sync is the virtual-drive update layer behind Smart Sort.

When Smart Sort mounts a library, FileTrace stores a mapping from each virtual path to the real source file. After that, FileTrace watches the physical source paths. If a source file is renamed or moved, the mounted path can be updated to point at the new physical location instead of disappearing or requiring a full remount.

On local NTFS drives, FileTrace prefers USN Journal tracking for this because USN gives low-overhead file identity and rename/move events. Where USN is not available, FileTrace falls back to safer watcher/scan behavior.

True Sync is meant to make the mounted library feel permanent while still letting you keep the real files wherever they are.

For performance, Smart Sort avoids doing heavyweight post-processing repeatedly when it can reuse the preview plan. When mounting into an existing virtual drive, FileTrace can merge new namespace entries instead of replacing everything. It also tags processed files internally so later operations can understand whether an item was sorted, renamed, or mounted.

Smart Sort is built for power-user scale: big drives, nested media folders, mixed content, real sidecar files, and libraries where folder context matters but cannot be blindly trusted.

## Watchdog

Watchdog is FileTrace's automation layer. Instead of opening the app every time a download finishes, you can create routing groups that watch folders and move new files into the right destination automatically.

Use Watchdog when you want FileTrace to:

- monitor a downloads folder,
- route completed media into libraries,
- separate movies, TV, anime, music, archives, installers, and general files,
- push files to local drives, mapped drives, or NAS shares,
- keep the virtual drive updated after moves,
- and run scripts after successful routing.

### How To Use Watchdog

Open FileTrace and choose **Watchdog** from the sidebar.

The first screen shows your groups. A group is a complete routing pipeline. It has:

- folders to listen to,
- destination folders to move into,
- a routing mode,
- duplicate handling,
- optional renaming,
- optional virtual-drive mounting,
- and optional post-move commands.

Click **Add Group** to create a group.

Choose a group name, then choose **One Mode** or **Smart Mode**. If you choose One Mode, also choose the group category.

Each group has its own on/off toggle. This is useful when you want to pause one workflow without turning off Watchdog entirely.

Click a group card to open its detail view. Use the back arrow to return to the group list.

### Listen Paths

**Listen Paths** are the folders Watchdog monitors.

Examples:

```text
D:\Downloads
```

```text
\\NAS\Incoming
```

Use **Browse** whenever possible. The native Windows folder picker helps avoid path typos and handles mapped drives more reliably than manual typing.

You can add multiple listen paths to one group. Path rows can be reordered by dragging, and removed when you no longer need them.

### Move Paths

**Move Paths** are the destinations Watchdog can route files into.

Examples:

```text
D:\Series
```

```text
\\NAS\Movies
```

Move paths are ordered by priority. Watchdog tries the first matching destination first. If that destination does not have enough free space, it spills over to the next matching destination.

You can reorder move paths by dragging them or by using **Up** and **Down**. Priority matters when you use multiple drives or network shares.

### One Mode

Use **One Mode** when everything entering the group should be treated as one category.

For example, if a folder only receives movie downloads, set the group category to **Movies**. Watchdog will treat every supported incoming file as a movie and route it through the movie workflow.

One Mode is best for predictable folders.

### Smart Mode

Use **Smart Mode** when one folder receives mixed content.

In Smart Mode, every move path has its own category. Watchdog classifies the incoming file first, then routes it to the matching destination.

Example:

```text
Listen path:
D:\Downloads

Move paths:
D:\Series       -> TV Series
D:\Movies       -> Movies
D:\Anime        -> Anime
D:\Music        -> Audio
D:\Archives     -> Archives
```

If Watchdog detects a movie, it routes it to a Movies destination. If it detects audio, it routes it to an Audio destination. A destination set to **General** can accept any supported file type.

### Group Settings

Open a group and click **Settings**.

The settings popup keeps the advanced controls out of the main path list view.

#### Rename Files

When **Rename files** is enabled, Watchdog applies FileTrace's media naming logic while moving files.

For movies, TV series, and anime, Watchdog can use the same saved file naming and folder hierarchy patterns from Settings that Organize, Rename, and Smart Sort use.

Example movie output:

```text
Movies\Inception (2010)\Inception (2010) [1080p].mp4
```

Example TV output:

```text
TV Series\Drama\House (2004)\Season 01\House - S01E01 - Pilot.mkv
```

When **Rename files** is disabled, Watchdog still routes files into folders, but keeps the original file name.

#### Mount To V Drive

When **Mount to V Drive** is enabled, Watchdog updates FileTrace's virtual drive after moving files.

This lets routed files appear in the mounted FileTrace library without needing to run Smart Sort again.

#### Duplicate Handling

Watchdog includes duplicate handling for files that resolve to the same media identity.

Available options:

- **Keep highest quality**
- **Delete the new file**
- **Ignore the new file**
- **Move the new file with (1)**

**Keep highest quality** is the default. It compares media quality using video metadata where possible, instead of trusting filename tags. This matters because filenames can say `1080p` even when the actual video stream is lower quality.

If **Ignore the new file** is selected, Watchdog marks the file as handled so it does not retry the same ignored item every scan.

#### Post-Move Commands

Post-Move Commands let you run scripts after a successful move.

Each command can be named, reordered, removed, and opened in the command editor.

FileTrace exposes environment variables to the command:

```text
FT_SOURCE_PATH
FT_DEST_PATH
FT_DEST_FOLDER
FT_CLEAN_NAME
FT_ORIGINAL_NAME
FT_CATEGORY
FT_GROUP_NAME
FT_RESOLUTION
```

Example command:

```bat
echo [%time%] Processed: %FT_ORIGINAL_NAME% --^> %FT_CLEAN_NAME% [%FT_RESOLUTION%] (Group: %FT_GROUP_NAME% | Cat: %FT_CATEGORY%) Moved to: %FT_DEST_FOLDER% >> "C:\FileTrace_TestLog.txt"
```

These values are passed as normal environment variables. Batch scripts can use `%FT_DEST_PATH%`, PowerShell can use `$env:FT_DEST_PATH`, and Python can read them through `os.environ`.

Commands run only after the file move succeeds. If a command fails, FileTrace logs the failure, but it does not roll back the completed file move.

### Start, Stop, And Scan Now

Click **Start** to begin monitoring configured groups.

Watchdog scans automatically and also uses fast Windows file-system events when available. Click **Scan Now** to manually scan all configured listen paths immediately.

Click **Stop** to pause routing.

### Network Shares And Permissions

Watchdog supports:

- local to local routing,
- local to network routing,
- network to local routing,
- and network to network routing.

Destinations can be normal folders, mapped drives, or UNC paths such as:

```text
\\NAS\Movies
```

If a network destination requires credentials, FileTrace can show a credentials popup for the destination share. Enter the share username and password, and FileTrace will retry the move.

### Sidecars And Metadata

When Watchdog routes media, it also tries to keep useful companion files with the media:

- subtitles beside the media file,
- subtitle folders such as `subs`, `sub`, and `subtitles`,
- poster images,
- existing `.nfo` files,
- generated `.nfo` files when metadata is available,
- and folder icons when poster artwork exists.

Existing `.nfo` files are preserved. If metadata is available and no `.nfo` exists, Watchdog can generate one after routing.

### Recommended Workflows

#### Auto-Route A Downloads Folder

1. Open **Watchdog**.
2. Click **Add Group**.
3. Name it something like `Downloads Intake`.
4. Choose **Smart Mode**.
5. Add your downloads folder as a listen path.
6. Add move paths for Movies, TV Series, Anime, Audio, and General.
7. Click **Start**.

This is the best setup when one folder receives many kinds of files.

#### Route Movies To A NAS

1. Create a Watchdog group.
2. Choose **One Mode**.
3. Set the group category to **Movies**.
4. Add the local download folder as a listen path.
5. Add the NAS movie share as the move path.
6. Enable **Rename files** if you want FileTrace naming patterns applied.
7. Start Watchdog.

If the NAS requires credentials, FileTrace will ask for them when needed.

#### Keep A Virtual Library Updated

1. Open a group.
2. Click **Settings**.
3. Enable **Mount to V Drive**.
4. Start Watchdog.

After successful moves, the virtual FileTrace library is refreshed so new items can appear without a full Smart Sort run.

### Technical Overview

Watchdog is built around grouped routing pipelines.

The persisted model contains global Watchdog settings plus a list of groups. Each group has an ID, name, enabled state, routing mode, group category, listen paths, ordered move destinations, and group settings.

Older Watchdog settings can be migrated into the grouped structure, so beta users do not lose their existing monitor paths during upgrades.

Routing starts by matching the incoming file to a group based on the listen path. In One Mode, the file is treated as the group category. In Smart Mode, FileTrace classifies the file first and then filters the destination list by category.

Destination selection is priority based. The engine sorts move paths by order, checks available free space, and spills over to the next matching destination when needed. The free-space layer supports local drives and UNC destinations.

For media files, Watchdog can reuse the same planner used by Organize and Rename. That keeps the behavior consistent across the app: saved naming patterns, saved folder hierarchy patterns, TMDB metadata, poster targets, folder icon targets, `.nfo` generation, duplicate rules, and sidecar handling all flow through the same planning model.

The transfer layer is designed for real Windows storage setups. It supports local moves, cross-volume moves, mapped drives, UNC shares, transactional temporary destinations for large transfers, resumable byte-marking for interrupted cross-volume copies, file-lock backoff, sleeping mapped-drive timeouts, and permission-denied callbacks for credential retry.

For monitoring, FileTrace prefers the NTFS USN Journal on eligible local NTFS paths. USN is extremely efficient because it reads the file-system change journal instead of repeatedly scanning every file. When a path is not USN-eligible, such as a UNC share or a non-NTFS drive, FileTrace falls back to `FileSystemWatcher`.

Watchdog also has a timed scan loop, currently clamped between 10 and 30 seconds, so automatic scanning and **Scan Now** still work even when fast event delivery is unavailable.

### Watchdog USN Details

USN is used only when the watched path is safe for it:

- the path is local rather than UNC,
- the drive is ready,
- the file system is NTFS,
- and the elevated helper can access the journal.

This keeps local download folders extremely light on CPU because FileTrace does not need to repeatedly enumerate the entire folder tree. For network shares, mapped drives, exFAT drives, and any location where USN is not available, Watchdog automatically falls back to normal Windows watcher behavior plus the timed scan loop. That fallback is slower than USN, but it is safer for NAS and removable-drive setups.

Duplicate handling is media-aware. The default **Keep highest quality** option compares actual video metadata where possible, so a lower-quality duplicate is not kept just because the filename claims a higher resolution.

After a successful move, Watchdog can move companion subtitles/posters/NFO files, generate missing `.nfo` metadata, apply folder icons, run post-move commands, refresh the virtual drive namespace, and clean up empty source folders.

This makes Watchdog suitable for always-on routing: download folders, NAS intake shares, media server staging folders, and power-user automation pipelines.

## Settings, API Keys, And Naming Formats

Settings is where you control FileTrace's global behavior, metadata keys, language, support logs, and the naming patterns used by Organize, Rename, Smart Sort, and Watchdog.

Open **Settings** from the sidebar. FileTrace switches into a dedicated Settings workspace with three tabs:

- **General**
- **API Keys**
- **Naming & Folder Format**

Use the back button in the Settings sidebar to return to the main FileTrace tools.

### General Settings

The **General** tab contains everyday app preferences.

#### Change Language

Use **Change Language** to switch the FileTrace interface language. If a translated string is missing during beta, FileTrace falls back to English for that text.

#### Default Preview Sort

**Default Preview Sort** controls how preview results are ordered before execution.

Available options include:

- **Original Position**
- **Alphabetical (A-Z)**
- **Date Modified**
- **File Size**

This only changes how the preview is displayed. It does not change your saved folder patterns.

#### Privacy Helpers

The General tab also includes privacy-related toggles:

- **Auto-strip GPS on import**
- **Auto-separate screenshots**

These are useful for users who often process photo folders and want location metadata or screenshots handled consistently.

#### Save Logs

Click **Save Logs** to export support logs. This is the button beta testers should use when reporting an issue, because it collects the useful local diagnostic logs without requiring users to dig through AppData manually.

### API Keys

The **API Keys** tab is where you add metadata provider keys.

FileTrace is BYOK: bring your own key. The installer does not ship with the developer's private API keys.

#### TMDB API Key

TMDB is used for movie, TV, anime-series metadata, posters, season artwork, TMDB IDs, `.nfo` files, and folder icons generated from posters.

You should add a TMDB key if you want FileTrace to:

- identify movies and TV series more accurately,
- fetch official titles, years, genres, and TMDB IDs,
- fetch official episode names,
- download the main poster,
- create folder icons from posters,
- generate `movie.nfo`, `tvshow.nfo`, and `anime.nfo`,
- and use TMDB tags such as `{TMDbId}` and `{TMDbTag}` in custom formats.

To get a TMDB key:

1. Create or log in to a TMDB account at [themoviedb.org](https://www.themoviedb.org/).
2. Open your account settings.
3. Open the **API** section. TMDB's developer documentation says API registration is available from the API link inside account settings.
4. Accept TMDB's API terms of use.
5. Create an API application/key.
6. Copy the API key or v4 token.
7. In FileTrace, open **Settings** -> **API Keys**.
8. Paste it into **TMDB API Key**.
9. Click **Save Settings**.

TMDB notes that API registration is best done from a desktop browser, not a mobile device. See TMDB's official getting-started docs: [TMDB API Getting Started](https://developer.themoviedb.org/docs/getting-started).

#### AcoustID API Key

AcoustID is used for music identification when a track does not already contain good embedded metadata.

FileTrace's music workflow is intentionally conservative:

- If the audio file already has good artist, album, title, and track metadata, FileTrace uses the embedded metadata.
- If the metadata is missing or weak, FileTrace can fingerprint the audio and query AcoustID.
- Music is then sorted into an artist/album structure such as:

```text
Artist\Album\Track Number - Track Title
```

If the album is unknown, FileTrace uses:

```text
Artist\Unknown Album\Track Number - Track Title
```

If there is no track number, FileTrace omits the number and uses the title.

To get an AcoustID key:

1. Open [acoustid.org](https://acoustid.org/).
2. Sign in. AcoustID supports sign-in providers such as MusicBrainz and Google.
3. Go to the API key/application registration page.
4. Register an application for FileTrace usage.
5. Copy the application API key.
6. In FileTrace, open **Settings** -> **API Keys**.
7. Paste it into **AcoustID API Key**.
8. Click **Save Settings**.

AcoustID's public web service documentation explains that an application key is required in the `client` parameter, and asks integrations to respect rate limits. See: [AcoustID Web Service](https://acoustid.org/webservice).

### Naming & Folder Format

The **Naming & Folder Format** tab controls how FileTrace names media files and builds media folders.

It has two primary editors:

- **File Naming Patterns**
- **Folder Hierarchy Patterns**

Each editor has tabs for:

- **TV Series**
- **Anime**
- **Movies**

The file pattern controls the final file name. The folder pattern controls the directory structure.

For example, the default TV file pattern is:

```text
{ShowTitle} - S{Season:00}E{Episode:00} - {EpisodeTitle}
```

This can resolve to:

```text
Breaking Bad - S01E01 - Pilot.mkv
```

The default TV folder pattern is:

```text
TV Series\{Genre}\{ShowTitle} ({ReleaseYear})\Season {Season:00}\
```

This can resolve to:

```text
TV Series\Drama\Breaking Bad (2008)\Season 01\
```

### How Tokens Work

Anything inside curly brackets is a dynamic token.

Anything outside curly brackets is treated as literal text.

For example:

```text
{MovieTitle} ({ReleaseYear}) [{Resolution}]
```

contains three tokens:

- `{MovieTitle}`
- `{ReleaseYear}`
- `{Resolution}`

and literal text:

- spaces,
- parentheses,
- and square brackets.

You do not need a special text token. If you want text in the result, type the text directly.

### Padding Numbers

Tokens can use number padding.

For example:

```text
{Season:00}
```

turns season `1` into:

```text
01
```

And:

```text
{AbsoluteEpisode:000}
```

turns episode `29` into:

```text
029
```

### Token Buttons

Under each input field, FileTrace shows token buttons.

Clicking a token button inserts that token at your current cursor position, not just at the end of the field. This makes it easy to build patterns without memorizing every token name.

The live preview updates as you type.

### Useful TV Series Tokens

TV Series patterns can use tokens such as:

```text
{ShowTitle}
{ShowCleanTitle}
{Season}
{Season:00}
{Episode}
{Episode:00}
{EpisodeTitle}
{ReleaseYear}
{TMDbId}
{TMDbTag}
{Network}
{Genre}
{Resolution}
{VideoCodec}
{AudioCodec}
```

`{TMDbTag}` is useful for media-server style names. It resolves to a tag like:

```text
{tmdb-1396}
```

So a folder pattern can include:

```text
{ShowTitle} ({ReleaseYear}) {TMDbTag}
```

and resolve to:

```text
Breaking Bad (2008) {tmdb-1396}
```

### Useful Anime Tokens

Anime supports the TV-style tokens plus anime-specific values such as:

```text
{AbsoluteEpisode:000}
{AudioLanguage}
{SubGroup}
```

The default anime file pattern is:

```text
[{SubGroup}] {ShowTitle} - {AbsoluteEpisode:000} [{Resolution}]
```

which can resolve to:

```text
[SubsPlease] Jujutsu Kaisen - 029 [1080p]
```

### Useful Movie Tokens

Movie patterns can use tokens such as:

```text
{MovieTitle}
{MovieCleanTitle}
{ReleaseYear}
{Director}
{Certification}
{TMDbId}
{TMDbTag}
{Resolution}
{VideoCodec}
```

The default movie file pattern is:

```text
{MovieTitle} ({ReleaseYear}) [{Resolution}]
```

which can resolve to:

```text
Dune Part Two (2024) [2160p]
```

The default movie folder pattern is:

```text
Movies\{MovieTitle} ({ReleaseYear})\
```

### Blank Tokens And Cleanup

Some metadata is optional. For example, an anime result may not have a network, or a movie may not have every field populated.

FileTrace blanks unknown or empty tokens and cleans common artifacts.

For example, if this pattern:

```text
{ShowTitle} - [{Network}]
```

has no network value, FileTrace avoids leaving a messy result like:

```text
Jujutsu Kaisen - []
```

Instead, it cleans the empty brackets and dangling separator.

### Reset To Default

Use **Reset to Default** when you want to restore the factory pattern for the current media type and editor.

Reset only applies to the currently selected tab. For example, resetting the TV Series file pattern does not reset the Movie folder pattern.

### Where These Patterns Are Used

The saved patterns are shared across FileTrace.

They can be used by:

- **Organize**
- **Rename**
- **Smart Sort**
- **Watchdog**

This is important: if you change your TV folder pattern in Settings, future TV organization can follow that new structure everywhere the media planner is used.

### Technical Overview

Settings are stored in the user's local FileTrace configuration area. API keys are masked before being returned to the WebView and protected when saved locally.

The format system is backed by FileTrace's C# `MediaFormatResolver`. It parses only `{...}` as dynamic tokens; everything else stays literal. The resolver supports object and dictionary values, numeric format strings, filename/path sanitization, blank-token cleanup, and folder-separator preservation for folder templates.

The Settings page does not fake the preview in JavaScript. The WebView calls the C# preview bridge, which uses the same resolver as the backend planner. That keeps the Settings preview aligned with the actual rename/sort engine.

When a media operation runs, FileTrace applies the saved settings through the shared preview planner. The planner builds token values from parsed file information, embedded metadata, TMDB metadata, video metadata, and audio metadata depending on the mode.

Resolution is determined from media metadata first. Filename tags such as `1080p` are used only as fallback when stream metadata is missing or unavailable.

TMDB lookups are batched and rate-aware, and AcoustID lookup is limited for audio identification. This helps FileTrace stay responsive while still enriching large batches.

## Cleanup Tools

Cleanup Tools replaces the old single-purpose Duplicates tab with a broader maintenance workspace.

Open **Cleanup Tools** from the sidebar, choose a folder, select which tools to run, then scan. FileTrace shows a preview of every candidate before deleting or renaming anything.

### Deduplicate Files

Deduplicate Files finds redundant copies by hashing supported heavy file types such as media files, archives, and executable installers. It avoids wasting time hashing tiny support files, DLLs, images, and ordinary metadata files during broad scans.

The result groups matching files together so you can decide which copies should be removed.

### Sample And Trailer Hunter

Sample And Trailer Hunter looks for video files whose names contain terms such as `sample`, `trailer`, or `extras`.

This is useful for cleaning download folders that include short preview clips you do not want in the final library.

### System/OS Junk Sweeper

System/OS Junk Sweeper removes disposable operating-system artifacts such as:

- `thumbs.db`
- `desktop.ini`
- `.DS_Store`
- `__MACOSX`
- Apple `._*` files
- temporary `~*` files

This keeps media folders cleaner when viewing hidden files or moving libraries between Windows, macOS, and NAS devices.

### Orphaned Sidecar Scanner

Orphaned Sidecar Scanner finds metadata files that no longer belong to a media item, such as leftover `.nfo`, `.jpg`, `.png`, `.srt`, `.ass`, `.ssa`, or `.vtt` files.

FileTrace protects normal media-server layouts. For example, a TV root folder containing `tvshow.nfo` and `poster.jpg` is not treated as orphaned if that folder or its first-level season folders contain video files.

### Partial Download Finder

Partial Download Finder looks for incomplete download files such as:

- `.part`
- `.crdownload`
- `.tmp`
- `.download`

FileTrace excludes its own temp, recovery, and cache files so cleanup does not damage active FileTrace state.

### Deep Clean Empty Directories

Deep Clean Empty Directories removes empty folder trees left behind after sorting or manual cleanup.

The safety rule is strict: FileTrace does not delete a folder if it contains any file, protected folder, config file, `.git` folder, or FileTrace runtime marker.

### Double-Extension Fixer

Double-Extension Fixer repairs names like:

```text
Movie.mkv.txt
Episode.mp4.url
Track.flac.download
```

It only fixes files when the embedded extension is in a strict safe whitelist. Dangerous names such as `Movie.mkv.exe` are ignored rather than renamed.

### Cleanup Safety

Cleanup actions are preview-first. Local delete actions try to use the Recycle Bin where Windows supports it.

For UNC shares, NAS folders, mapped network drives, or any path where the Recycle Bin is unavailable, FileTrace shows an explicit permanent-delete warning before continuing:

```text
Recycle Bin is not available for network drives. These files will be permanently deleted. Continue?
```

Execution results report deleted, renamed, skipped, and failed items. If one item fails because it is locked or missing, FileTrace reports the partial result instead of pretending everything succeeded.

## Privacy And Power Tools

FileTrace also includes smaller utility tools for users who want quick maintenance actions without leaving the app.

### Privacy Tools

Open **Privacy** from the sidebar.

Available tools:

- **Strip GPS Data** removes location data from photos while keeping the rest of the image metadata intact.
- **Strip All Metadata** removes EXIF and other image metadata completely.
- **Separate Screenshots** detects screenshots and separates them from camera photos.

These tools are useful before sharing photos publicly, uploading folders, or cleaning mixed phone/camera imports.

### Power Tools

Open **Power Tools** from the sidebar.

The current beta power tool is:

- **Album Gen** creates clean PDF albums from chapter or image folders. It can generate one PDF, or multiple PDFs from `split.txt` ranges.

Power Tools is where FileTrace will keep focused utilities that do not belong in the main organize/rename/watchdog workflow, but are still useful for library maintenance and power-user batch work.

## Beta Feedback

FileTrace is being prepared for beta testers who work with real libraries, large folders, NAS shares, metadata sidecars, and messy download workflows.

If you are testing the beta, the most useful feedback includes:

- what folder structure you started with,
- what FileTrace previewed,
- what result you expected,
- whether the issue happened in Organize, Rename, Smart Sort, Watchdog, Privacy, or Power Tools,
- and an exported support log from **Settings** -> **Save Logs**.

Please avoid sending private license keys, API keys, or personal media files unless they are test files you are comfortable sharing.

## Closing Notes

FileTrace is built around a simple idea: file organization should be powerful enough for large personal archives, but safe enough that you can preview the result before trusting it with years of media.

The beta focuses on practical Windows workflows:

- preview-first organizing and renaming,
- Smart Sort for large mixed folders,
- Watchdog for automatic routing,
- virtual-drive inspection before committing moves,
- metadata-aware naming,
- media-server friendly sidecars,
- folder artwork,
- NAS and mapped-drive resilience,
- and support logs for fast debugging.

Thanks for testing FileTrace. The more strange folders, edge cases, and real-world setups beta testers throw at it, the better the final app becomes.

## Legal

By installing or using the FileTrace beta, you agree to the beta license terms and privacy policy:

- [End User License Agreement](EULA.md)
- [Privacy Policy](PRIVACY.md)

FileTrace is local-first, but optional metadata features may communicate directly with third-party services such as TMDB and AcoustID when you provide API keys or enable metadata features.
