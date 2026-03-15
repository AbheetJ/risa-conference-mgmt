# RISA Labs — Conference Management App

## What this is
Single-file HTML app for managing RISA Labs' GTM strategy across 9 US healthcare conferences.

- **Live URL**: https://abheetj.github.io/risa-conference-mgmt/
- **The entire app is one file**: `index.html`
- **To deploy any change**: edit `index.html`, then run the deploy command below

## Deploy command
```bash
git add index.html && git commit -m "describe your change" && git push
```
GitHub Pages auto-updates within ~30 seconds.

## Stack
- Single HTML file — no build step, no framework, no npm
- Firebase Realtime Database for shared real-time state
- Firebase Authentication — Google Sign-In, @risalabs.ai accounts only
- GitHub Pages for hosting

## Firebase config
```javascript
apiKey: "AIzaSyDpPVCsuId1Z5ikHykFzwn4SCOvteuZ4VA"
authDomain: "risa-conference-mgmt.firebaseapp.com"
databaseURL: "https://risa-conference-mgmt-default-rtdb.firebaseio.com"
projectId: "risa-conference-mgmt"
```

## Conferences (9, all 2026)
HOPA, NCCN, ACCA, Beckers Oncology, Beckers CPO, Asembia, COA, THMA, Ci4cc

## Team members
| Name | Email |
|---|---|
| Abheet | abheet@risalabs.ai |
| Naru | naru@risalabs.ai |
| GR | gr@risalabs.ai |
| GG | gg@risalabs.ai |
| Subhash | subhash@risalabs.ai |
| Sparsh | sparsh@risalabs.ai |
| Amarpreet | amar@risalabs.ai |
| Nilesh | nilesh@risalabs.ai |
| Dane | dane@risalabs.ai |
| Manching | manching@risalabs.ai |

## Design system (always follow this)
- **Light mode only** — background #f5f5f7, cards white with 1px #e5e5ea border
- Font: Inter / -apple-system
- Primary blue: #4f86f7
- Use styled modals instead of browser prompt() or confirm()
- Card border-radius: 14px, padding: 20–24px

## Key architecture
- All shared state lives in Firebase under `v1`: `confs`, `tasks`, `members`
- Navigation state (active conf, current tab) is local only
- `S` is the global state object
- `save()` debounces Firebase writes by 400ms
- `DB.on('value')` listens for remote teammate changes

## State structure
```
S.confs[]                          — conference objects
S.tasks[cid][key][tid]             — task data: {checked, owner, status, value, dueDate, sheetUrl}
S.tasks[cid][key]._custom          — user-added tasks [{id, title}]
S.tasks[cid][key]._hidden          — removed built-in task IDs []
S.tasks[cid][key]._renames         — renamed titles {tid: newTitle}
S.tasks[cid][key]._order           — drag-reorder order [tid, ...]
S.members[]                        — [{name, email}]
```

## Key functions
| Function | What it does |
|---|---|
| `refreshSection(cid, key)` | Re-renders one task section in-place, keeps open/collapsed state |
| `renderTab()` | Re-renders full tab, preserves open sections |
| `openMasterSheetModal(cid)` | Link one Google Sheet URL to multiple list tasks |
| `showMyTasks()` | Page showing all tasks assigned to logged-in user |
| `myName()` | Returns member name matching current user's email |
| `normMembers(arr)` | Migrates old string[] members to {name,email}[] |
| `duePill(cid,key,tid,t)` | Renders color-coded due date pill |
| `sheetLinkHtml(cid,key,tid,t)` | Renders Google Sheet link UI for list tasks |

## Task sections per conference
- **Databasing**: conference research & data collection
- **Pre-Conference** tabs: Sponsorship, Messaging & Story, Design, Lead Gen, Campaigns, People, Parties, Learning
- **During**: live conference execution
- **Post**: follow-up actions

## Important rules when making changes
1. Always use `refreshSection(cid, key)` instead of `renderTab()` for single-section updates
2. After every change, deploy with the deploy command above
3. Never use browser `prompt()` or `confirm()` — use the `modal()` helper instead
4. All interactions should keep the user in the same place (section stays open)
5. Test that Firebase sync still works after changes
