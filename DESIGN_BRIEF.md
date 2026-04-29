# Mobile App Design Brief — Headless CMS

---

## Overview

Design a **mobile application** (iOS + Android) for a **headless CMS platform**. The app is the control panel for a multi-tenant content management system. Users log in, join or create organisations, and within each organisation they manage content entries, categories, tags, media assets, members, and API keys. A separate system-admin role can manage all users and system roles across the entire platform.

The design should feel like a modern productivity tool — clean, structured, with clear role-based UI gating (certain buttons/sections are hidden or disabled based on what the user is allowed to do).

---

## User Roles & Permissions

There are **two independent permission layers** — the UI must respect both.

**System-level (platform-wide):**

| Role | What they can do |
|---|---|
| Regular User | Everything below except the Admin Panel |
| System Admin | Everything + the Admin Panel (user management, roles, activity logs) |

**Org-level (per organisation the user belongs to):**

| Role | What they can do |
|---|---|
| Viewer | Browse content, categories, tags — read only |
| Editor | Everything a Viewer can do + create/edit/delete content, categories, tags, upload assets |
| Admin | Everything an Editor can do + manage members, manage API keys, rename/delete the org |

---

## Screen List & Content

---

### GROUP 1 — Authentication (unauthenticated users)

---

#### Screen 1 — Splash / Onboarding

- App logo and name
- Short tagline (e.g. "Your content, your control")
- Two CTAs: **Log In** and **Sign Up**

---

#### Screen 2 — Register

- Fields: Email, Username, Password, Confirm Password
- Primary CTA: **Create Account**
- Link to Log In screen
- Inline validation errors per field

---

#### Screen 3 — Login

- Fields: Email, Password
- Show/hide password toggle
- Primary CTA: **Log In**
- Link to Register screen
- If the server returns a `twoFactorId` in the response, the app navigates automatically to the 2FA verification screen

---

#### Screen 4 — 2FA Verification *(login step 2, only shown when 2FA is enabled)*

- Heading: "Two-Factor Authentication"
- Sub-text: "Enter the 6-digit code from your authenticator app"
- 6-digit OTP input (large, finger-friendly — each digit in its own box)
- Primary CTA: **Verify**
- Small secondary link: "Back to Login"

---

### GROUP 2 — Home & Organisations

---

#### Screen 5 — My Organisations *(home screen after login)*

- Top bar: App name/logo, notification bell icon (with unread count badge), profile avatar
- Section heading: "My Organisations"
- List of organisation cards, each showing:
  - Org name
  - User's role in that org (badge: Admin / Editor / Viewer)
  - Member count
  - Tap → goes to Org Dashboard
- FAB (floating action button): **+ New Organisation**
- Empty state: illustration + "You haven't joined any organisations yet. Create one to get started."

---

#### Screen 6 — Create Organisation *(modal or full sheet)*

- Field: Organisation Name
- CTA: **Create**
- Note: creator is automatically assigned the Admin role

---

### GROUP 3 — Organisation Dashboard & Navigation

---

#### Screen 7 — Organisation Dashboard

- Top bar: Org name (with back arrow to My Orgs), settings/gear icon (Admin only)
- Bottom tab bar with tabs:
  - **Content** (default)
  - **Categories**
  - **Tags**
  - **Media**
  - **Members** *(visible to Admin only)*
  - **API Keys** *(visible to Admin only)*
- Quick stats banner: total content entries, published count, draft count, member count

---

### GROUP 4 — Content Management

---

#### Screen 8 — Content List *(Content tab)*

- Search bar at top
- Filter chips (horizontal scroll): All / New / Draft / Published / Unpublished
- Additional filter controls (collapsible row): Category, Tag, Date range (From / To)
- List of content cards, each showing:
  - Title (or "Untitled" if empty)
  - Status badge (colour-coded: New = grey, Draft = yellow, Published = green, Unpublished = orange)
  - Category name (if set)
  - Tags (up to 3, then "+N more")
  - Created date
  - Thumbnail if asset is attached
- FAB: **+ New Entry** *(Editor / Admin only — hidden for Viewer)*
- Empty state per filter state

---

#### Screen 9 — Content Editor *(create or edit an entry)*

- Back arrow + title: "New Entry" or "Edit Entry"
- Status indicator at top (pill badge, not editable — auto-calculated)
- Fields:
  - **Title** — text input
  - **Rich Content** — rich text editor with basic formatting toolbar (bold, italic, lists, headings, links)
  - **Category** — dropdown/picker populated from org's category list
  - **Tags** — multi-select chip picker populated from org's tag list (can search within it)
  - **Asset** — image/file section:
    - Shows thumbnail if an asset is already attached (with URL)
    - Button: **Upload Asset** → opens file picker
    - Upload progress bar when uploading
    - After processing completes, the asset URL updates in real time (via push notification)
- Status auto-logic (shown as read-only info text):
  - If Title + Rich Content + Category + Asset all filled → **Published**
  - Otherwise → **Draft**
- Action buttons at bottom:
  - **Save** (always)
  - **Unpublish** (only if status is Published)
  - **Delete** (soft delete — confirmation dialog)
- Editors and Admins can edit; Viewers see a read-only version

---

#### Screen 10 — Content Detail *(read-only for Viewers)*

- Full view of a content entry: title, rich text, category, tags, asset image, status, dates
- Admin/Editor: edit icon in top-right corner

---

### GROUP 5 — Categories

---

#### Screen 11 — Category List *(Categories tab)*

- Search bar
- Alphabetical list of category cards, each showing:
  - Category name
  - Description (truncated to 1 line)
- FAB: **+ New Category** *(Editor / Admin only)*
- Swipe-left on a card: **Edit** and **Delete** actions *(Editor / Admin only)*

---

#### Screen 12 — Create / Edit Category *(modal or full sheet)*

- Field: Name (required)
- Field: Description (optional)
- CTA: **Save**
- If editing, show a **Delete** button at the bottom with a confirmation dialog

---

### GROUP 6 — Tags

---

#### Screen 13 — Tag List *(Tags tab)*

- Search bar
- Alphabetical list of tag chips/cards, each showing:
  - Tag name
- FAB: **+ New Tag** *(Editor / Admin only)*
- Swipe-left: **Edit** and **Delete** *(Editor / Admin only)*

---

#### Screen 14 — Create / Edit Tag *(modal or full sheet)*

- Field: Name (required)
- CTA: **Save**
- If editing, show a **Delete** button

---

### GROUP 7 — Media Library

---

#### Screen 15 — Media Library *(Media tab)*

- Grid of uploaded asset thumbnails (image or generic file icon)
- Each tile shows: file name, upload date
- Tap a tile → Screen 16 (Asset Detail)
- Upload button in top-right *(Editor / Admin only)*: opens file picker
  - File picker also asks: "Link to which content entry?" (search/select existing entry)
- Empty state: "No media uploaded yet"

---

#### Screen 16 — Asset Detail

- Full-size preview (image) or file icon
- Metadata: file name, type, upload date, linked entry (tappable link)
- URL (copyable)

---

### GROUP 8 — Members *(Admin only tab)*

---

#### Screen 17 — Member List

- List of member rows, each showing:
  - User avatar / initials
  - Username
  - Role badge (Admin / Editor / Viewer)
- FAB: **+ Add Member**
- Tap a member row → Screen 18 (Edit Member Role)

---

#### Screen 18 — Add Member *(modal)*

- Search field: find user by email or username
- Results list (tap to select)
- Role picker: Viewer / Editor / Admin (Viewer pre-selected)
- CTA: **Add to Organisation**

---

#### Screen 19 — Edit Member Role *(modal)*

- Shows username and current role
- Role picker: Viewer / Editor / Admin
- CTA: **Save**
- Danger zone at bottom: **Remove from Organisation** (confirmation dialog)

---

### GROUP 9 — API Keys *(Admin only tab)*

---

#### Screen 20 — API Key List

- List of API key cards, each showing:
  - Truncated key value (e.g. `aBcD...XyZ=`)
  - Created date
  - Expiry date (or "No expiry")
  - Status badge: Active (green) / Inactive (red)
  - Toggle switch to enable/disable
- FAB: **+ Generate Key**
- Swipe-left: **Delete** with confirmation

---

#### Screen 21 — Create API Key *(modal)*

- Optional field: Expiry date (date picker — leave blank for no expiry)
- CTA: **Generate**
- After creation: show full key value in a copyable text box with a warning ("This key will not be shown again — copy it now")

---

### GROUP 10 — Notifications

---

#### Screen 22 — Notifications

- Accessed from the bell icon on the home screen
- List of notification rows, each showing:
  - Icon (type of event)
  - Message text
  - Timestamp
  - Unread indicator dot
- Header actions: **Mark all as read**, **Clear all**
- Swipe-left on a row: **Delete**
- Tap a row: mark as read + navigate to relevant resource
- Empty state: "You're all caught up"

---

### GROUP 11 — Profile & Account Settings

---

#### Screen 23 — Profile

- Accessed from avatar icon on home screen
- Avatar image (tappable to upload a new one)
- Display name, bio (editable inline or via edit button)
- Link rows:
  - Account Settings → Screen 24
  - Security & 2FA → Screen 25
  - Logout (with confirmation dialog)

---

#### Screen 24 — Account Settings

- Fields (pre-filled, editable): Email, Username, Password (masked, with change option)
- CTA: **Save Changes**

---

#### Screen 25 — Security / 2FA Settings

- Current 2FA status: Enabled / Disabled (status pill)
- If **Disabled**:
  - Button: **Enable Two-Factor Authentication** → opens Screen 26
- If **Enabled**:
  - Button: **Disable Two-Factor Authentication** → asks for current TOTP code to confirm, then disables

---

#### Screen 26 — 2FA Setup *(sheet/modal)*

- Step 1: Instructions ("Download Google Authenticator or any TOTP app")
- QR code displayed (generated from API)
- Manual entry key shown below QR as fallback
- Step 2: Field — "Enter the 6-digit code from your app"
- CTA: **Confirm & Enable**
- Success state: confirmation message, auto-dismiss

---

### GROUP 12 — System Admin Panel *(System Admins only — hidden entirely for regular users)*

---

#### Screen 27 — Admin Panel Home

- Accessed via a dedicated tab or menu item that only appears for system admins
- Quick links:
  - User Management
  - Role Management
  - Activity Logs

---

#### Screen 28 — User Management

- Search bar (search by email or username)
- List of user rows:
  - Username, email
  - System role badge (Admin / User)
  - 2FA status indicator
- Tap a user → Screen 29 (User Detail)
- Multi-select mode: checkbox per row, bulk delete action bar at bottom

---

#### Screen 29 — User Detail

- User info: username, email, system roles, 2FA enabled status, account created date
- Edit button → Screen 30
- Danger zone: **Delete User** (confirmation dialog)

---

#### Screen 30 — Edit User

- Fields: Username, Email (editable)
- System roles section (assign or remove system-level roles)
- CTA: **Save**

---

#### Screen 31 — Role Management *(system roles)*

- List of system roles (name, description)
- FAB: **+ New Role**
- Tap a role → edit name/description; delete button at bottom

---

#### Screen 32 — Activity Logs

- Filter: by user (searchable user picker, optional)
- Paginated log list, each row showing:
  - Action description
  - User (username)
  - Timestamp
- Read-only, no actions

---

### GROUP 13 — Public Content *(no auth required)*

---

#### Screen 33 — Public Content Feed

- No auth required
- Search bar, tag and category filters
- Card list of published content entries:
  - Thumbnail (if available)
  - Title
  - Category
  - Tags
  - Published date
- Pagination / infinite scroll

---

#### Screen 34 — Public Content Detail

- Hero image (asset)
- Title
- Category + Tags
- Rich text body rendered
- Created / updated date
- No edit controls

---

## Key Flows *(for prototyping)*

**Login with 2FA:**
Screen 3 → (if 2FA required) Screen 4 → Screen 5

**Create & publish a content entry:**
Screen 8 → tap FAB → Screen 9 → fill Title + Rich Content + pick Category → tap "Upload Asset" → upload completes → status auto-updates to Published → Save

**Add a member to an org:**
Screen 17 → tap FAB → Screen 18 → search user by email → pick role → Add → back to Screen 17

**Enable 2FA:**
Screen 23 → Security → Screen 25 → Enable → Screen 26 → scan QR → enter code → Confirm → success

**Generate an API key:**
Screen 20 → tap FAB → Screen 21 → set expiry (optional) → Generate → copy key (one-time display) → back to Screen 20

---

## UI Notes for the Designer

- **Role gating:** FABs and action buttons (Create, Edit, Delete) should be absent — not just greyed out — for Viewers. Admin-only tabs (Members, API Keys) should not appear in the nav at all for non-Admins. The entire Admin Panel group should not appear for non-system-admins.
- **Status colours for content:** New = grey, Draft = amber/yellow, Published = green, Unpublished = orange. These appear consistently as badges throughout the content list and editor.
- **Async upload UX:** After a file is uploaded (Screen 9 / Screen 15), there is a processing delay before the asset is linked to the entry. Show a "processing…" spinner in the asset section of the editor until the system confirms the link. When the confirmation arrives, update the asset preview in place without a full screen reload.
- **Empty states:** Every list screen needs a designed empty state (illustration + message + CTA if applicable).
- **Destructive actions:** Deleting content, removing a member, deleting an API key, deleting a user — all require a confirmation bottom sheet or dialog before proceeding.
- **API key reveal (Screen 21 post-generation):** The full key value is shown only once. Design this as a prominent, copyable card with a clear warning and a "I've copied this key" dismiss button.
- **Navigation pattern:** Bottom tab bar within an org context. Top navigation (back arrows + org name in header) for drilling into detail screens.
