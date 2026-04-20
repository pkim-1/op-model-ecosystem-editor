---
phase: quick
plan: 260420-kqo
type: execute
wave: 1
depends_on: []
files_modified:
  - tools/meeting-tracker.html
autonomous: true
requirements: [quick-fix]
must_haves:
  truths:
    - "Uploading a Zoom closed-caption .txt file ([Speaker] HH:MM:SS format) auto-populates participants"
    - "Uploading a WebVTT .vtt file (Speaker: text on timestamp lines) auto-populates participants"
    - "Pasting transcript text and clicking Parse also populates participants"
    - "Existing manual-entry and item-parsing functionality still works"
  artifacts:
    - path: "tools/meeting-tracker.html"
      provides: "Fixed parseTranscript with multi-format speaker extraction"
  key_links:
    - from: "parseTranscript()"
      to: "meetingParticipants input"
      via: "speaker Set merged into input value"
      pattern: "partInput.value"
---

<objective>
Fix the parseTranscript() function in meeting-tracker.html so that speakers are correctly extracted from real Zoom transcript formats and auto-populated into the participants field.

Purpose: The current speaker-matching regex does not match either of the two actual Zoom transcript formats (closed-caption .txt and WebVTT .vtt), so speakers are never detected and participants never populate.

Output: Fixed meeting-tracker.html with working participant auto-population.
</objective>

<execution_context>
@/Users/paulkim/.claude/get-shit-done/workflows/execute-plan.md
@/Users/paulkim/.claude/get-shit-done/templates/summary.md
</execution_context>

<context>
@tools/meeting-tracker.html

## Bug Analysis

The `parseTranscript()` function (line 951) uses this regex to detect speaker lines:

```javascript
const speakerMatch = trimmed.match(/^(?:\d{1,2}:\d{2}(?::\d{2})?\s+)?(.+?):\s*$/);
```

This regex requires the line to END with a colon (`:\s*$`), matching a format like:
```
10:30:00 Speaker Name:
```

But real Zoom transcripts use TWO different formats, neither of which match:

**Format 1 — Zoom closed-caption .txt:**
```
[John Sheahan] 08:46:35
Sorry, Paul, I'm the world of your own.
```
Speaker is in brackets, timestamp after — no trailing colon at all.

**Format 2 — WebVTT .vtt:**
```
00:00:40.000 --> 00:00:42.000
Ben Foreman: Hey!
```
Speaker name is inline WITH the text content (colon is mid-line, not at end).

Both formats fail the regex, so `speakers` Set stays empty, and participants are never filled.
</context>

<tasks>

<task type="auto">
  <name>Task 1: Fix speaker extraction regex to handle real Zoom transcript formats</name>
  <files>tools/meeting-tracker.html</files>
  <action>
Rewrite the speaker-detection logic in the `parseTranscript()` function (starting at line 951) to handle all three transcript formats:

**Format 1 — Zoom closed-caption .txt** (`[Speaker Name] HH:MM:SS` on its own line, content on next line):
- Detect with regex: `/^\[(.+?)\]\s+\d{1,2}:\d{2}(:\d{2})?$/`
- When matched, set `currentSpeaker` to capture group 1, add to `speakers` Set, continue to next line
- Next non-empty lines are content attributed to that speaker

**Format 2 — WebVTT .vtt** (`Speaker Name: content text` on a line, possibly after a timestamp line):
- Detect with regex: `/^([A-Z][A-Za-z\s.'-]+?):\s+(.+)$/` — speaker name starts with uppercase, followed by colon, then actual text content
- Add speaker to `speakers` Set, and push the text portion (capture group 2) as an entry
- Also skip WEBVTT header line and timestamp lines (`HH:MM:SS.mmm --> HH:MM:SS.mmm`)

**Format 3 — The original format** (keep as fallback): `HH:MM:SS Speaker Name:` with colon at end and content on subsequent lines.

The logic should:
1. First check for Format 1 (`[Speaker] timestamp`)
2. Then check for Format 2 (`Speaker: text` inline) — but NOT match lines like "Action item: do something" (require the name portion to look like a proper name: starts uppercase, 2+ words or known patterns)
3. Then fall back to Format 3 (original regex)
4. Skip VTT metadata lines: `WEBVTT`, blank lines, and `HH:MM:SS.mmm --> HH:MM:SS.mmm` timestamp lines

After the loop, the existing participant-merging code (lines 979-985) should work as-is since it reads from the `speakers` Set.

Also ensure that `entries` are properly built — for Format 1 the content line should be pushed as an entry with `currentSpeaker`. For Format 2, the text after the colon should be pushed. For Format 3, same as current behavior.

Do NOT change any other functionality — the item categorization heuristics, the review UI, or the save logic.
  </action>
  <verify>
Open tools/meeting-tracker.html in a browser. Create a new meeting, switch to Transcript mode.

Test 1: Upload or paste a Zoom closed-caption transcript (.txt format):
```
[Paul Kim] 08:46:38
I'm doing good.

[John Sheahan] 08:46:40
How's your trip going?
```
Verify: meetingParticipants field shows "Paul Kim, John Sheahan"

Test 2: Paste a WebVTT transcript:
```
WEBVTT

00:00:40.000 --> 00:00:42.000
Ben Foreman: Hey!

00:00:42.000 --> 00:00:49.000
Paul Kim: Good to see you.
```
Verify: meetingParticipants field shows "Ben Foreman, Paul Kim"

Test 3: Click Parse — parsed items appear in the review section with correct speaker attributions.
  </verify>
  <done>
- Uploading a .txt Zoom transcript auto-populates participants from [Speaker] lines
- Uploading a .vtt WebVTT transcript auto-populates participants from Speaker: lines
- Parsed items show correct speaker as assignee
- Existing meeting items and manual entry still function correctly
  </done>
</task>

</tasks>

<verification>
- Open meeting-tracker.html in browser
- Test with real transcript files from /Users/paulkim/Documents/Zoom/1-1s/
- Confirm participants field populates after file upload
- Confirm participants field populates after paste + Parse button
- Confirm previously saved meetings still display correctly
</verification>

<success_criteria>
Participants auto-populate from uploaded transcript files in both Zoom .txt and .vtt formats.
</success_criteria>

<output>
After completion, create `.planning/quick/260420-kqo-fix-participants-auto-population-in-meet/260420-kqo-SUMMARY.md`
</output>
