---
phase: quick
plan: 260420-kea
type: execute
wave: 1
depends_on: []
files_modified:
  - tools/meeting-tracker.html
autonomous: true
requirements: [quick-task]
must_haves:
  truths:
    - "User can upload a .txt transcript file instead of copy/pasting"
    - "Meeting title auto-populates from the uploaded filename"
    - "Participants auto-populate from speakers detected in the transcript file"
    - "Existing paste-transcript workflow still works unchanged"
  artifacts:
    - path: "tools/meeting-tracker.html"
      provides: "File upload input and processing logic in transcript mode"
      contains: "Upload Transcript File"
  key_links:
    - from: "file input element"
      to: "parseTranscript function"
      via: "FileReader reads file content, populates textarea, triggers parse"
      pattern: "FileReader.*readAsText"
---

<objective>
Add file upload capability to the Meeting Tracker's transcript input mode. Currently users must copy/paste transcript text manually. This adds a file input button that reads a .txt file, populates the transcript textarea, auto-generates the meeting title from the filename (e.g., "Erica_Paul 1_1's transcript" becomes "Erica Paul 1:1"), and auto-extracts participants from transcript speakers.

Purpose: Eliminate the friction of opening a transcript file, selecting all, copying, and pasting -- instead just pick the file.
Output: Updated meeting-tracker.html with file upload in transcript mode.
</objective>

<execution_context>
@/Users/paulkim/.claude/get-shit-done/workflows/execute-plan.md
@/Users/paulkim/.claude/get-shit-done/templates/summary.md
</execution_context>

<context>
@tools/meeting-tracker.html
</context>

<tasks>

<task type="auto">
  <name>Task 1: Add file upload button and processing to transcript mode</name>
  <files>tools/meeting-tracker.html</files>
  <action>
In the meeting-tracker.html file, modify the transcript mode section (the `div#transcriptMode` block around lines 838-843) to add a file upload option:

1. **Add file input UI** inside `#transcriptMode`, before the textarea:
   - Add a styled file input area with a visible "Upload Transcript File" button and a hidden `<input type="file" accept=".txt,.vtt">` element
   - Style: use existing `.btn .btn-secondary` styling, add a small label like "or paste below"
   - When a file is selected, read it with FileReader.readAsText()

2. **Auto-generate meeting title from filename:**
   - Extract the filename without extension (e.g., `"Erica_Paul 1_1's transcript.txt"` -> `"Erica_Paul 1_1's transcript"`)
   - Clean it up: replace underscores with spaces, remove "'s transcript" suffix, replace "1_1" with "1:1"
   - If the meeting title field (`#meetingTitle`) is empty, populate it with the cleaned name
   - Also auto-set the date field if the filename or file metadata contains a date pattern (YYYY-MM-DD or similar)

3. **Populate textarea and auto-parse:**
   - Set the transcript textarea (`#transcriptText`) value to the file contents
   - Automatically call `parseTranscript()` so the parsed items appear immediately
   - The existing parseTranscript() already extracts speakers into participants -- no changes needed there

4. **Add the new function** `handleTranscriptFile(event)`:
   ```javascript
   function handleTranscriptFile(event) {
     const file = event.target.files[0];
     if (!file) return;
     const reader = new FileReader();
     reader.onload = function(e) {
       // Auto-fill title from filename
       const titleInput = document.getElementById('meetingTitle');
       if (!titleInput.value.trim()) {
         let name = file.name.replace(/\.[^.]+$/, ''); // strip extension
         name = name.replace(/'s transcript$/i, '');     // strip "'s transcript"
         name = name.replace(/_/g, ' ');                 // underscores to spaces
         name = name.replace(/\b1 1\b/g, '1:1');        // "1 1" to "1:1"
         titleInput.value = name.trim();
       }
       // Populate textarea and parse
       document.getElementById('transcriptText').value = e.target.result;
       parseTranscript();
       showToast('Transcript loaded from file');
     };
     reader.readAsText(file);
     event.target.value = ''; // reset for re-upload
   }
   ```

5. **CSS**: Add minimal styling for the upload area -- a dashed border drop zone area with the button centered, matching existing card/form styling. Keep it simple.

Do NOT break the existing paste workflow -- the textarea remains fully functional for manual paste. The file upload is an additional convenience.
  </action>
  <verify>
    <automated>cd /Users/paulkim && python3 -c "
import re
html = open('tools/meeting-tracker.html').read()
assert 'handleTranscriptFile' in html, 'Missing handleTranscriptFile function'
assert 'Upload Transcript' in html or 'upload' in html.lower(), 'Missing upload UI'
assert 'FileReader' in html, 'Missing FileReader usage'
assert 'readAsText' in html, 'Missing readAsText call'
assert \"'s transcript\" in html or 's transcript' in html, 'Missing filename cleanup logic'
print('All checks passed')
"</automated>
  </verify>
  <done>
    - File upload button visible in transcript mode of the new meeting form
    - Selecting a .txt file populates the transcript textarea
    - Meeting title auto-generates from filename (underscores to spaces, removes "transcript" suffix, fixes "1:1" format)
    - parseTranscript() runs automatically after file load, extracting speakers into participants
    - Existing paste workflow unchanged
  </done>
</task>

</tasks>

<verification>
- Open tools/meeting-tracker.html in browser
- Click "New Meeting" then switch to "Paste Transcript" mode
- Verify file upload button is visible above or alongside the textarea
- Upload a Zoom transcript .txt file -- textarea populates, title auto-fills, participants extracted, parsed items appear
- Verify manual paste still works as before
- Verify editing an existing meeting also shows the upload option in transcript mode
</verification>

<success_criteria>
- File upload input accepts .txt files in transcript mode
- Meeting title auto-populates from cleaned filename
- Participants auto-extract from transcript speakers (existing behavior triggered automatically)
- No regression in paste-transcript or manual-entry workflows
</success_criteria>

<output>
After completion, create `.planning/quick/260420-kea-add-file-upload-capability-to-meeting-tr/260420-kea-SUMMARY.md`
</output>
