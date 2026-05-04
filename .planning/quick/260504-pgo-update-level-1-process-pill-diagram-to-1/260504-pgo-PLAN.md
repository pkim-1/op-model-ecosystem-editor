---
phase: quick
plan: 260504-pgo
type: execute
wave: 1
depends_on: []
files_modified:
  - Desktop/op-model-ecosystem-editor-LATEST.html
autonomous: true
---

<objective>
Update level 1 (parent) process pill highlighting to: (1) color-code connections based on their originating sub-pill color, and (2) only display connections that exist within individual sub-pills (no cross-sub-pill connections).

Purpose: When clicking a parent pill, the user should see a rollup that accurately represents what exists at the sub-pill level -- each sub-pill's internal connections shown in that sub-pill's color -- rather than generating new connections between teams in different sub-pills.

Output: Modified `highlightTeamsForPill()` function in Desktop/op-model-ecosystem-editor-LATEST.html
</objective>

<context>
@Desktop/op-model-ecosystem-editor-LATEST.html
</context>

<tasks>

<task type="auto">
  <name>Task 1: Modify parent pill highlighting to color-code connections by sub-pill and suppress cross-sub-pill connections</name>
  <files>Desktop/op-model-ecosystem-editor-LATEST.html</files>
  <action>
Modify the `highlightTeamsForPill()` function (around line 2198) to handle the parent pill case differently:

When `isSubPill` is false (parent pill selected):

1. Build a map of connection-to-color: For each child sub-pill, compute which connections belong to it (pairs of teams both in that sub-pill's team list). Store the sub-pill color index for each connection.

2. For connection highlighting (the `svg.querySelectorAll('path')` loop around line 2251):
   - Instead of highlighting ALL connections where both endpoints are in the rolled-up teamIds set, only highlight connections where both endpoints exist within the SAME sub-pill's team array.
   - Color each highlighted connection path with the corresponding sub-pill's color from SUB_PILL_COLORS (using the sub-pill's index % 8).
   - If a connection belongs to multiple sub-pills, use the first sub-pill's color.
   - Also include connections from the parent pill's own direct teams (proc.teams) -- color these with a neutral white/light color.

3. For card highlighting: Keep existing behavior (highlight all teams in the rollup set, dim others). Cards should NOT be colored by sub-pill when viewing the parent -- only connections get colored.

4. For the dimming logic: Connections that don't belong to any sub-pill (or parent direct teams) should be dimmed, not highlighted.

Implementation approach -- replace the parent pill branch in `highlightTeamsForPill`:

```javascript
// When parent pill is selected (isSubPill is false):
// Build per-sub-pill connection membership
const subPillConnections = new Map(); // key: "fromId-toId" (sorted), value: colorIndex

// Parent's own teams connections (neutral color)
const parentTeamIds = proc.teams.map(t => t.id || t);
for (let i = 0; i < parentTeamIds.length; i++) {
    for (let j = i + 1; j < parentTeamIds.length; j++) {
        const a = Math.min(parentTeamIds[i], parentTeamIds[j]);
        const b = Math.max(parentTeamIds[i], parentTeamIds[j]);
        subPillConnections.set(`${a}-${b}`, -1); // -1 = parent/neutral
    }
}

// Each child's teams connections (colored)
if (proc.children && proc.children.length > 0) {
    proc.children.forEach((child, ci) => {
        const childTeams = (child.teams || []).map(t => t.id || t);
        for (let i = 0; i < childTeams.length; i++) {
            for (let j = i + 1; j < childTeams.length; j++) {
                const a = Math.min(childTeams[i], childTeams[j]);
                const b = Math.max(childTeams[i], childTeams[j]);
                const key = `${a}-${b}`;
                if (!subPillConnections.has(key)) {
                    subPillConnections.set(key, ci); // first sub-pill wins
                }
            }
        }
    });
}
```

Then in the path loop, for parent pill case:
```javascript
svg.querySelectorAll('path').forEach(path => {
    const connId = parseInt(path.dataset.connId);
    const conn = state.connections.find(c => c.id === connId);
    path.classList.remove('conn-highlighted', 'conn-dimmed');
    path.style.stroke = '';
    path.style.strokeWidth = '';
    if (conn && teamIds.size > 0) {
        const a = Math.min(conn.fromId, conn.toId);
        const b = Math.max(conn.fromId, conn.toId);
        const key = `${a}-${b}`;
        if (subPillConnections.has(key)) {
            path.classList.add('conn-highlighted');
            const colorIdx = subPillConnections.get(key);
            if (colorIdx >= 0) {
                const [r, g, b2] = SUB_PILL_COLORS[colorIdx % 8];
                path.style.setProperty('stroke', `rgba(${r}, ${g2}, ${b2}, 1)`, 'important');
                path.style.setProperty('stroke-width', '3', 'important');
            }
            // colorIdx === -1: parent-level connection, use default highlight style
        } else {
            path.classList.add('conn-dimmed');
        }
    }
});
```

NOTE: Fix the variable shadowing -- use `cr, cg, cb` or similar instead of `r, g, b2` to avoid conflict with the outer scope variable `b`. The actual implementation should use non-conflicting variable names like:
```javascript
const [cr, cg, cb] = SUB_PILL_COLORS[colorIdx % 8];
path.style.setProperty('stroke', `rgba(${cr}, ${cg}, ${cb}, 1)`, 'important');
```

Keep the existing sub-pill (isSubPill === true) behavior completely unchanged.
  </action>
  <verify>
    <automated>cd /Users/paulkim && python3 -c "
import re
content = open('Desktop/op-model-ecosystem-editor-LATEST.html').read()
# Verify subPillConnections map is used
assert 'subPillConnections' in content, 'subPillConnections map not found'
# Verify SUB_PILL_COLORS is referenced in the parent pill path
assert content.count('SUB_PILL_COLORS') >= 4, 'Expected more SUB_PILL_COLORS references'
# Verify no syntax errors by checking balanced braces in function
print('Verification passed')
"</automated>
  </verify>
  <done>
    - Clicking a parent pill shows connections color-coded by their originating sub-pill
    - Only connections within individual sub-pills are shown (no cross-sub-pill connections)
    - Sub-pill click behavior remains unchanged
    - Parent pill's own direct team connections shown with default highlight style
  </done>
</task>

</tasks>

<verification>
1. Open the HTML file in browser
2. Enter Present Mode
3. Click a parent pill that has multiple sub-pills with teams assigned
4. Verify: connections are colored matching sub-pill colors (visible in the color dots on the parent pill)
5. Verify: no connections appear between teams that only share membership at the parent level but are in different sub-pills
6. Click individual sub-pills and verify their behavior is unchanged
</verification>

<success_criteria>
- Parent pill click shows only intra-sub-pill connections (rollup without cross-sub-pill synthesis)
- Each connection is color-coded to match its originating sub-pill's color
- Sub-pill click behavior is identical to before
- No JavaScript errors in console
</success_criteria>

<output>
After completion, create `.planning/quick/260504-pgo-update-level-1-process-pill-diagram-to-1/260504-pgo-SUMMARY.md`
</output>
