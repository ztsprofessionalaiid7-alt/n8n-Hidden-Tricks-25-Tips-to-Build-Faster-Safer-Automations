# n8n Hidden Tricks & Productivity Guide

A practical collection of lesser-known n8n tips: 10 tricks from a popular tutorial video, 2 bonus tips, and 15 extra tricks for building faster, safer, and more reliable automations.

> **Note:** Keyboard shortcuts and some features vary by n8n version and plan. If a shortcut doesn't work, check the official n8n Keyboard Shortcuts docs.

---

## Table of Contents

- [Part 1: Video Tricks (10 + 2 Bonus)](#part-1-video-tricks)
- [Part 2: Extra Tricks](#part-2-extra-tricks)
- [Quick Shortcut Cheat Sheet](#quick-shortcut-cheat-sheet)
- [Best Practices Checklist](#best-practices-checklist)

---

## Part 1: Video Tricks

### 1. Pin Data
**Save API calls while testing.** Run a node once, then pin its output so n8n reuses that data instead of calling the API again on every test run.

- Select the node and click the **Pin** icon in the output panel, or press `P`.
- Saves cost, time, and rate-limit headroom.
- Unpin when you want fresh data.

### 2. Switch Expressions (Fixed vs. Expression)
**Toggle any field between a fixed value and a dynamic expression.**

- Hover over a field and switch **Fixed / Expression**.
- Typing `{{` in a field also opens expression mode.
- Use fixed for static text, expression for data from earlier nodes.

### 3. Mock Data
**Build and test without a live API.** Use sample JSON as stand-in data.

- Use a **Edit Fields (Set)** node, **Code** node, or the pinned-data editor to create demo JSON.
- Great for designing workflows before credentials or endpoints are ready.

```json
[
  { "name": "Alice", "email": "alice@example.com", "plan": "pro" },
  { "name": "Bob", "email": "bob@example.com", "plan": "free" }
]
```

### 4. Node Navigation
**Jump between nodes with the keyboard** instead of clicking around a large canvas.

- Use arrow keys (or the sidebar/node list) to move from one node to the next.
- Helpful in large workflows where nodes are far apart.

### 5. Activate / Deactivate Nodes
**Temporarily disable a node without deleting it.**

- Select a node and press `D`, or use the toggle on the node.
- Disabled nodes are skipped during execution.
- Perfect for A/B testing steps or isolating bugs.

### 6. Sticky Notes
**Document and organize your workflow visually.**

- Add via the canvas menu or press `Shift + S`.
- Change colors to group sections (for example, green = input, red = error handling).
- Supports Markdown formatting.

### 7. Workflow History
**View and restore previous versions** of a workflow.

- Open the workflow menu and choose **History** (availability depends on your plan/version).
- Compare versions and restore an earlier one if something breaks.

### 8. Tab Key
**Open the node picker instantly.**

- Press `Tab` on the canvas to open or close the "Add node" menu.
- Faster than clicking the **+** button.

### 9. Date & Time
**Format dates easily with built-in functions.**

```text
{{ $now }}
{{ $now.toFormat('yyyy-MM-dd') }}
{{ $now.minus({ days: 7 }).toISO() }}
{{ $today }}
```

- `$now` and `$today` are Luxon-based date objects.
- Chain methods like `.plus()`, `.minus()`, and `.toFormat()`.

### 10. Logs
**Inspect inputs and outputs of every step.**

- Open the **Logs** panel at the bottom of the editor after a run (especially useful with AI Agents).
- See each node's input, output, timing, and status.
- Use the **Executions** tab to review past runs.

### Bonus A: Human in the Loop
**Add manual approval before critical actions.**

- Use a Wait / approval step so a person confirms before the workflow sends emails, makes payments, or deletes data.
- Essential for AI agents performing high-stakes tasks.

### Bonus B: Specific Output Format
**Force structured output from AI agents.**

- Enable the **Structured Output Parser** or request JSON in the prompt.
- Define a JSON schema so downstream nodes can rely on predictable fields.

```json
{
  "summary": "string",
  "sentiment": "positive | neutral | negative",
  "action_required": true
}
```

---

## Part 2: Extra Tricks

### 11. Execute a Single Node
Test one node without running the whole workflow: open the node and click **Execute step**. Combine with pinned data for fast iteration.

### 12. Reference Data from Any Earlier Node
Pull data from any previous node, not just the one directly before it.

```text
{{ $json.fieldName }}                      // current item
{{ $('Node Name').item.json.fieldName }}   // specific earlier node
{{ $('Node Name').first().json.field }}    // first item from a node
```

You can also **drag and drop** fields from the input panel into any parameter.

### 13. Quick Pin with `P`
Select a node and press `P` to pin or unpin its data without opening the node.

### 14. Quick Sticky Notes and New Workflows
- `Shift + S`: add a sticky note.
- `Ctrl/Cmd + Alt + N`: create a new workflow.

### 15. Code Node Helpers
Handle multiple items cleanly in the Code node:

```javascript
// All items from the previous node
const items = $input.all();

// Only the first item
const first = $input.first();

// Return in n8n's expected format
return items.map(item => ({
  json: { ...item.json, processed: true }
}));
```

### 16. Error Workflow + Error Trigger
Get alerted whenever a workflow fails.

1. Create a workflow starting with an **Error Trigger** node.
2. Add Telegram / Slack / Email to send the alert.
3. In your main workflow: **Settings → Error Workflow** and select it.

### 17. Retry On Fail and Continue On Error
Found in each node's **Settings** tab.

- **Retry On Fail:** automatically retry flaky API calls (set tries and wait time).
- **On Error → Continue:** keep the workflow running even if one node fails.

### 18. Sub-Workflows (Execute Workflow Node)
Break large workflows into small, reusable pieces.

- Easier debugging and maintenance.
- Reuse the same logic across many workflows.
- Makes it easier to package workflows as templates or products.

### 19. Loop Over Items + Wait
Avoid rate limits by processing items in batches.

- Use **Loop Over Items (Split in Batches)** with a small batch size.
- Add a **Wait** node inside the loop to pause between batches.

### 20. JavaScript Methods in Expressions
Use plain JS and n8n helpers directly in fields.

```text
{{ $json.name.toUpperCase() }}
{{ $json.tags.join(", ") }}
{{ $json.items.length }}
{{ $json.email.isEmpty() }}
{{ $json.createdAt.toDateTime().toFormat('dd LLL yyyy') }}
```

### 21. Tidy Up
Auto-arrange messy nodes: `Shift + Alt + T` (available in recent versions).

### 22. Copy and Paste Workflows as JSON
- Select nodes and press `Ctrl/Cmd + C` to copy them as JSON.
- Paste into any n8n instance with `Ctrl/Cmd + V`.
- Paste JSON shared in forums or chats directly onto the canvas.

### 23. Test URL vs. Production URL (Webhooks)
- **Test URL:** works only while listening in the editor; shows data for debugging.
- **Production URL:** works only when the workflow is **Active**.

Remember to switch URLs in your external app when going live.

### 24. Credentials and Environment Variables
Never hardcode API keys in nodes or expressions.

- Store secrets in **Credentials**.
- Use environment variables for configuration values (self-hosted).
- Share workflows safely without leaking keys.

### 25. Evaluations Tab
Test the quality of AI workflow outputs against a dataset (available in recent versions). Useful for comparing prompts or models before deploying.

---

## Quick Shortcut Cheat Sheet

| Action | Shortcut |
|---|---|
| Open node picker | `Tab` |
| Pin / unpin data | `P` |
| Disable / enable node | `D` |
| Add sticky note | `Shift + S` |
| Tidy up nodes | `Shift + Alt + T` |
| Copy nodes | `Ctrl/Cmd + C` |
| Paste nodes | `Ctrl/Cmd + V` |
| Select all nodes | `Ctrl/Cmd + A` |
| Save workflow | `Ctrl/Cmd + S` |
| New workflow | `Ctrl/Cmd + Alt + N` |
| Execute workflow | `Ctrl/Cmd + Enter` |

---

## Best Practices Checklist

- [ ] Pin data while building to avoid repeated API calls
- [ ] Use sticky notes to document each section
- [ ] Add an Error Workflow to every production workflow
- [ ] Enable Retry On Fail for unreliable APIs
- [ ] Split large workflows into sub-workflows
- [ ] Batch requests and add Wait nodes to respect rate limits
- [ ] Keep all secrets in Credentials
- [ ] Add Human in the Loop for risky actions
- [ ] Use structured output for AI agent responses
- [ ] Check Logs and Executions when debugging

---

## Contributing

Found a new trick or a shortcut that changed? Open an issue or submit a pull request.

## License

MIT
