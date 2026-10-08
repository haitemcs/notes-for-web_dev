**Definition:**

Cloning creates a local copy of a remote GitHub project on your computer that is fully connected to the original repository via Git.

**Clone vs. Download ZIP (Key Difference):**

- **Download ZIP:** Only gives you the source files (no Git history).
- **Clone:** Gives you the files **plus** the hidden `.git` folder. This allows Git to track changes, pull updates, and push commits back to GitHub.

---

**Step-by-Step Clone Process:**

1. **Copy the URL:** On GitHub, click the green **Code** button → select **HTTPS** → copy the URL (e.g., `https://github.com/your-name/Booking-App.git`).
2. **Navigate in Terminal:** `cd E:\` (choose your desired parent directory).
3. **Run Clone:** `git clone https://github.com/your-name/Booking-App.git`
4. **Enter the Folder:** `cd Booking-App` (Git creates this folder automatically).
5. **Verify:** Run `git status` – you should see a normal Git status, *not* a "fatal: not a git repository" error.

---

**Standard Workflow (After Cloning):**

1. **Check changes:** `git status`
2. **Stage files:** `git add <file-name>` (or use `.` for all)
3. **Commit locally:** `git commit -m "Your message"`
4. **Push to GitHub:** `git push`