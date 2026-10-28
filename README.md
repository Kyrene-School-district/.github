# KSD PBI Repo SOP and Rules

This organization contains repos that correspond to our workspaces in Power BI service. Within the repos, there are folders for each Power BI report we have published to the service. Each report folder contains the .pbip file, and an "artifacts" folder that may contain any related documents, samples, notes, wire frames, or other artifacts related to that report. 

(Note: for the sake of this readme, it is written as if it were for the pbi-talent repo. replace the repo name with whichever you are working in)

## Your Git client is Claude, not VS Code

Everything in this README that says "check out a branch," "commit," "push," or "open your Git client" is something you ask Claude Desktop to do in plain English — you do not need VS Code, GitHub Desktop, or any other Git GUI installed. Claude runs the actual `git` commands on your machine and tells you what happened.

In practice this looks like:

- "What branch am I on?" / "Check status" — Claude runs `git status` and tells you the branch and whether anything is uncommitted.
- "Get me synced up" / "pull the latest" — Claude runs `git pull` for you, stashing and restoring any uncommitted work first if it needs to.
- "Pull main and make a new branch called mandatorypd-sep" — Claude does the pull, creates the branch, and checks it out.
- "Check out the staffing-rb-901 branch" — switches you to an existing branch.
- "Commit this with the message ..." — Claude shows you what changed before committing, if you want to see it first just ask "what changed?"
- "Push it" — pushes the branch, first time or the hundredth time, no different button to remember.
- "Open a pull request" — Claude can open it for you and give you the link, or you can do it yourself on GitHub.

## Rules

These are how our team operates. Each rule exists because breaking it has a cost that lands on someone else.

- **R1. The `main` branch is what is published in the Power BI Service.** If `main` and the Service disagree, that is a bug, and it gets fixed the same day. Anytime you publish to the service, merge to main and vice versa (see R6).

- **R2. Clone this repo to `C:\Users\yourusername\Git\pbi-talent\`.** Same path on everyone's machine. Never OneDrive, never a SharePoint-synced folder, never a network drive. 

- **R3. Turn on both PBIP preview features before your first commit.** TMDL for the model, PBIR for the report. Both, not one. See Part 1. The reports in this repo are already saved in these formats, so opening one without them enabled will not go well.

- **R4. PBIP is the source of truth, not `.pbix`, and no data source files.** A `.pbix` may only exist in a report's `artifacts` subfolder as a historical/reference copy (see R7) — never as a working copy next to the `.pbip`. Student and staff data does not go in GitHub, ever. The `.gitignore` blocks data files; do not work around it.

- **R5. All report edits happen in Power BI Desktop.** Never use Edit in the Power BI Service. A change made in the web exists only in the Service, is invisible to Git, and gets silently erased the next time anyone republishes from Desktop.

- **R6. Merge to `main` and republish to the Service in the same sitting.** A merged branch that has not been published makes R1 false.

- **R7. Open the `.pbip`, never the `.pbix`.** Once a report is in this repo, the old `.pbix` is a historical artifact. Archive it in the `C:\Users\yourusername\Git\pbi-talent\<reportname>\artifacts` and stop opening it. Two people editing two different copies of the same report is the exact problem this repo exists to end.

- **R8. Other file types may be saved into the subfolder "artifacts" for each report.** The main folder should only contain the pbip file and its folders. Any other file types should be saved into the artifacts subfolder. 

---

# Part 1: One-time setup on your machine

Do this once per machine, before anything else.

### 1. Ensure you have two preview features turned on in your Power BI desktop

Power BI Desktop → **File → Options and settings → Options → Preview features**

Check both:

- **Store semantic model using TMDL format**
- **Store reports using enhanced metadata format (PBIR)**

Click OK, then **fully close and reopen Power BI Desktop.** The settings do not take effect until you restart.

Why both: TMDL makes the model readable as text, so measure and relationship changes show up as reviewable lines. PBIR does the same for the report layout, writing one file per visual instead of one enormous JSON blob. With only TMDL on, half your changes are unreviewable.

### 2. Create your local location for clones

On your C drive, create a folder where you will clone to. C:\Users\yourusername\Git\pbi-talent\ 


### 3. Clone the repo

Clone `Kyrene-School-district/pbi-talent` to C:\Users\yourusername\Git\pbi-talent
You can use Claude and ask it to do this. 


### 4. Confirm it worked

You should have `C:\Users\yourusername\Git\pbi-talent\` containing `.gitignore`, `README.md`, and a folder per report. If you see those, you are set up.

---

# Part 2: Migrating an existing report into this repo

Once per report. Skip this if the report is already here.

### 1. Find out what the report is called in the Service

This matters because Power BI Desktop matches on that name when you publish. If your PBIP has a different name, publishing creates a **second** report in the workspace instead of updating the existing one, and R1 breaks immediately. This is the name you will save under.

### 2. Make a folder for the file within your local Git folder

Create `C:\Users\yourusername\Git\pbi-talent\<reportname>\`, in `snake_case` (lowercase, words separated by underscores — e.g. `new_staff_survey`). The folder name is a repo convention only and has no effect on anything in Power BI. Team convention is that the folder name match the report name.

Also create a subfolder `C:\Users\yourusername\Git\pbi-talent\<reportname>\artifacts` to house any notes, reference documents, wireframes, samples, etc. This is where anything other than the PBIP will be saved.

### 3. Save the report as PBIP

1. Open the `.pbix` in Power BI Desktop
2. **File → Save as**
3. Change the file type dropdown to **Power BI project files (\*.pbip)**
4. Navigate into `C:\Users\yourusername\Git\pbi-talent\<reportname>\`
5. File name: **the name from step 1, exactly**
6. Save
7. **Close Power BI Desktop completely.** It holds file locks and Git gets confused while it is open

### 4. Check before you commit

Ask Claude to show you what's new (`git status`). You will see a long list of new files, typically 40 to 150 depending on the report (those come from the PBIP structure — don't worry, you only created one PBIP). Before committing, confirm:

- ✅ `.tmdl` files exist under `<name>.SemanticModel\definition\`
- ✅ A `<name>.Report\definition\pages\` folder exists, with a folder per visual
- ❌ No `.pbix` anywhere in the list
- ❌ No `localSettings.json`
- ❌ No `.csv`, `.xlsx`, or other data files within the main folder. Any notes, related documents, etc will be saved in the artifacts subfolder

If you see `model.bim` instead of `.tmdl` files, TMDL was off when you saved. If you see a single `report.json` instead of the `pages` folder, PBIR was off. Either way: fix the setting, restart Desktop, delete the folder contents, and redo step 3.

If a `.pbix` or a data file shows up, **stop and ask.** That means the `.gitignore` is not doing its job and it needs fixing before anything gets committed.

### 5. Commit the baseline

Commit message: `baseline: <Report Name> as published`

**This first commit goes straight to `main`, and it is the only time you skip branching.** A baseline import has nothing to diff against, so a branch and a pull request would be pure ceremony. Everything after this goes through Part 3.

Push it.

### 6. Add the report to the inventory table

Fill in a row in the table further down this file, and archive the old `.pbix` in the artifacts subfolder. 

---

# Part 2b: Building a brand-new report from scratch

Different from Part 2. Part 2 is for a report that **already exists and is already published** — you are just catching Git up to something real. This part is for a report that **does not exist anywhere yet** — no `.pbix`, nothing published, nothing to catch up to.

The dividing line is R1: `main` is supposed to be what's published in the Service. A report that has never been published has nothing for `main` to correctly reflect yet, so unlike Part 2, **this does not skip branching.** Everything here goes through Part 3's branch-first flow, from the very first file.

### 1. Pull `main` and create a branch first, before creating anything

Same as Part 3, Step 1: ask Claude to pull `main` (official command: git pull) and create a new branch, named for the new report plus your initials and/or the month. Do this **before** you create the folder or open Power BI Desktop — not after.

### 2. Create the folder and build the report on that branch

Create `C:\Users\yourusername\Git\<reponame>\<reportname>\` in `snake_case`, same as Part 2 Step 2. Build the report fresh in Power BI Desktop (or start from a blank PBIP), saving into that folder as you go. Commit along the way like any other round of work (Part 3, Steps 2–4).

### 3. Merge and publish together, the first time

When the report is ready for its first-ever publish to the Service: open a pull request, merge it, then publish from Power BI Desktop in the same sitting (R6). This is the moment `main` and the Service become true for this report at the same time — do not publish before merging, and do not merge without publishing right after.

### 4. Add the report to the inventory table

Same as Part 2 Step 6.

---

# Part 3: A round of work

This is the loop you will run every time from now on.

### 1. Sync up and check out the right branch, before you touch anything

There is only one copy of `C:\Users\yourusername\Git\pbi-talent\` on your machine. "Checking out" a branch means telling Git which branch's version of the files should sit in that one folder right now — it rewrites the files on disk to match. Power BI Desktop has no idea any of this happens; it just opens whatever is sitting there. So syncing and checking out has to happen before you open the `.pbip`, not after.

**Run this decision every time you sit down to work, in order:**

1. **Ask Claude to check status first** (official command: git status; you can also say "check status" / "what's going on"). This tells you whether anything is uncommitted and what branch you're on.
2. **If `git status` shows uncommitted changes** — commit them, or ask Claude to stash them, before doing anything else below. Pulling or switching branches with dirty changes sitting around is how work gets lost or tangled into the wrong branch.
3. **If you are starting a brand-new round of work on a report that nobody else is touching** — ask Claude to pull `main` (official command: git pull) and create a new branch, named report plus your initials and/or the month: `mandatorypd-sep`, `staffing-rb-901`. No spaces.
4. **If you are picking up a round someone else already started, or coming back to your own branch from a prior session** — ask Claude to check out that existing branch by name instead. Do not make a second branch for the same round of work — it just splits the history and makes the eventual pull request harder to read.
5. **If you are not sure whether a branch already exists for the report you are about to touch** — ask Claude to list the repo's branches, or ask a teammate, before creating one.
6. **If you are just picking back up mid-round on your own branch and only want the latest of your own work** (e.g. switching machines) — ask Claude to check out the branch and pull it, same as step 3/4 but no new branch.

**Quick commands:**

**git status** — Get me up to date. Shows current branch, staged/unstaged changes, untracked files. My "where am I" command, run before almost anything else.

**git pull** — Fetch and merge the latest from cloud/main into your local branch. Use when you know the tree is clean.

**git fetch** — downloads latest info from cloud (new commits, branches, tags) but doesn't touch your working files or current branch.

**git push** — uploads your local commits to cloud.



One branch still covers one round of work on one report. New pages, new measures, a new data source, all in the same branch is fine.

Ask Claude to confirm which branch you're on ("what branch am I on?") before you open Power BI Desktop, not after something looks wrong. It should say your branch name, not `main`. That is your "you are here."

### 2. Do the work

Open the `.pbip` from `C:\Users\yourusername\Git\pbi-talent\<reportname>\` (R7), make your changes, Ctrl+S.

Power BI Desktop manages the folder contents itself, including deleting files for things you removed. You never move or delete files by hand.

### 3. Close Power BI Desktop before committing

File locks again. Close it fully.

### 4. Commit

Tell Claude to commit. It will show you what changed and ask for (or suggest) a short plain-language message.

Good message: `added FY comparison page, new dimFiscalYear table, updated four measures for multi-year`

Commit at every stopping point and always at the end of a working session. Several commits on one branch is normal and good.

Also commit before you check out a different branch for any reason — a quick look at another report, a switch to help with something else, anything. Uncommitted changes can follow you across a branch switch and land somewhere they don't belong, or block the switch outright. If you are stepping away mid-change and are not ready for a real commit yet, at minimum do not check out another branch until you are.

Tell Claude "commit and push it" and it will do both. There is no separate first-time step to remember — the first push on a new branch and every push after it are the same ask.

### 5. When you are ready to publish to the service, open a pull request

Ask Claude to open the pull request, or do it yourself:

`github.com/Kyrene-School-district/pbi-talent/compare/main...YOUR-BRANCH-NAME`

Or use the **Pull requests** tab, **New pull request**, and pick the branches from the dropdowns.

Click **Create pull request**.

### 6. Actually read the diff

Click the **Files changed** tab. This is the part that justifies the whole system, so do not skip it.

See "Reading a diff" below for how to cut through the noise.

### 7. Merge, then publish, then clean up

1. **Merge pull request**, then **Confirm merge**
2. **Delete branch.** Merging already copied every commit into `main`, so the work is saved. The branch name is just a label and deleting it removes nothing. The pull request page keeps a permanent record. The one case where deleting loses work is deleting a branch that was never merged, so always merge first
3. **Republish to the Service, same sitting.** See R6. This is the step that keeps R1 true, and it is the easiest one to forget

---

# Reading a diff

A report of any size produces a lot of files. Most of them are not interesting. Here is how to find the parts that are.

**Use the filter box** at the top left of the Files changed list. Typing `tmdl` drops a 138-file diff to about 30 and leaves you looking at the model.

**What the model files are:**

| File | What it holds |
|---|---|
| `definition/tables/<TableName>.tmdl` | One table: its columns, its Power Query, its measures |
| `definition/relationships.tmdl` | Every relationship in the model |
| `definition/model.tmdl` | Model-level settings |
| `definition/expressions.tmdl` | Shared Power Query expressions and parameters |
| `definition/cultures/en-US.tmdl` | Display folders, formatting, descriptions |

**What is noise:**

- `LocalDateTable_<guid>.tmdl` and `DateTableTemplate_<guid>.tmdl` are hidden tables that Power BI generates automatically behind every date column. Their names are GUIDs, they churn constantly, and they are almost never what changed. Ignore them.
- `diagramLayout.json` is where you dragged the boxes in model view. Ignore it.
- `.platform` files are metadata. Ignore them.

**Green is added, red is removed.** A new table shows up as an entirely new `.tmdl` file, which is usually the clearest signal of what a round of work actually did.

---

# Naming

- **The PBIP name must match the report's name in the Power BI Service.** Publishing matches on that name. A mismatch creates a duplicate item in the workspace instead of updating the real one.
- **Renaming a report is its own deliberate round of work**, with workspace cleanup, not something to fold into other changes.
- **Team convention is that the folder name match the report name in `snake_case`** — lowercase words separated by underscores, e.g. `employee_absences`. This is a repo/folder convention only; it has no effect on the PBIP name itself or on Power BI.
- Spaces in the PBIP name are allowed and Git handles them. Matching the Service matters more than tidiness.
- **Renaming a folder?** Ask Claude to do it with `git mv`, not by renaming it in File Explorer or on github.com — `git mv` keeps the file history attached to the rename. github.com's web editor cannot rename a folder in one step (it renames files one at a time), so avoid doing folder renames there.

---



## NOTE: What this repo does not cover

Some things live only in the Power BI Service and will never appear here:

- Scheduled refresh settings and data source credentials
- Row-level security role membership (roles are in the model, the people assigned to them are not)
- Workspace and app permissions and audiences
- Subscriptions and alerts
