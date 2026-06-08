# Page Challenge: Password Change

**Difficulty:** Easy  
**Points available:** 100  
**Scoring rubric:** See `SCORING-SHEET.md` in the hub repo

---

## What You Are Building

A password change form. The authenticated user enters their current password, a new password, and confirms the new password.

---

## Reference Layout

```
┌──────────────────────────────────────────┐
│  ← Back     Change Password             │
├──────────────────────────────────────────┤
│                                          │
│  Current Password                        │
│  [________________________________]      │
│                                          │
│  New Password                            │
│  [________________________________]      │
│  Must be at least 8 characters           │
│                                          │
│  Confirm New Password                    │
│  [________________________________]      │
│                                          │
│  [ Cancel ]   [ Save Password ]          │
│                                          │
└──────────────────────────────────────────┘
```

---

## Required Fields

| Field | Type | Validation |
|-------|------|-----------|
| Current Password | Password input | Required |
| New Password | Password input | Required, min 8 chars |
| Confirm New Password | Password input | Required, must match New Password |

---

## Required Behaviour

- Form uses **Reactive Forms** with a custom cross-field validator to check that New Password and Confirm New Password match
- Show inline error: "Passwords do not match" when they differ and the confirm field is touched
- "Save Password" button disabled while form is invalid
- On successful submit: show a success message inline ("Password updated successfully") — no navigation required
- "Cancel" button navigates back (use `router.navigate(['/'])` or `location.back()`)

---

## Component Structure (suggested)

```
app/
├── app-routing.module.ts
├── app.module.ts
├── features/
│   └── auth/
│       ├── auth.module.ts
│       ├── auth-routing.module.ts
│       ├── password-change/
│       │   ├── password-change.component.ts
│       │   ├── password-change.component.html
│       │   └── password-change.component.scss
│       └── auth.service.ts
└── shared/
    └── validators/
        └── password-match.validator.ts   ← custom cross-field validator
```

---

## Scoring Focus for This Page

| Category | Key things reviewed |
|----------|-------------------|
| Code quality (40 pts) | Custom validator in own file; no inline logic; service layer even if minimal |
| Visual accuracy (35 pts) | Clean stacked form; helper text under new password field; button alignment |
| Functionality (25 pts) | Cross-field validation fires correctly; success state shown; cancel works |

---

## Constraints Reminder

- Angular `^21.2.14`
- NgModules — not standalone
- Reactive Forms — not template-driven
- UI library: your choice — document it in this README
- No real backend

---

## What to Document in This README (add below)

After building, fill in:

**UI Library used:** ___________________  
**Packages added:** ___________________  
**How to run:** `nvm use && npm install && ng serve`  
**Known limitations:** ___________________
