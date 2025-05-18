---
layout: page
title: Text Editor
---

<div id="text-editor" class="container-md px-3 my-5" contenteditable="true"></div>

<style>
#text-editor {
  outline: none;
  border: none;
  background: transparent;
  font-family: inherit;
  font-size: inherit;
  line-height: inherit;
  white-space: pre-wrap;
  min-height: 100vh;
}
#text-editor:empty:before {
  content: '';
}
</style>

<script>
const editor = document.getElementById('text-editor');
const STORAGE_KEY = 'savedText';
const AUTO_SAVE_DELAY = 5000; // 5 seconds of inactivity
let autoSaveTimeout = null;

// Default Lorem Ipsum text
const defaultText = `Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.
Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.`;

// Load saved text or default text
function loadText() {
  const savedText = localStorage.getItem(STORAGE_KEY);
  editor.textContent = savedText || defaultText;
  placeCursorAtEnd();
}

// Place cursor at the end of the text
function placeCursorAtEnd() {
  const range = document.createRange();
  const selection = window.getSelection();
  range.selectNodeContents(editor);
  range.collapse(false);
  selection.removeAllRanges();
  selection.addRange(range);
  editor.focus();
}

// Save text to local storage
function saveText() {
  localStorage.setItem(STORAGE_KEY, editor.textContent);
}

// Auto-save after inactivity
function scheduleAutoSave() {
  clearTimeout(autoSaveTimeout);
  autoSaveTimeout = setTimeout(saveText, AUTO_SAVE_DELAY);
}

// Handle keydown events
editor.addEventListener('keydown', (e) => {
  if (e.key === 'Enter' && e.shiftKey) {
    e.preventDefault();
    saveText();
  } else if (e.key === 'Enter') {
    // Allow default Enter behavior (line break)
    scheduleAutoSave();
  }
});

// Schedule auto-save on input
editor.addEventListener('input', scheduleAutoSave);

// Prevent unwanted formatting on paste
editor.addEventListener('paste', (e) => {
  e.preventDefault();
  const text = e.clipboardData.getData('text/plain');
  document.execCommand('insertText', false, text);
});

// Initialize
document.addEventListener('DOMContentLoaded', loadText);
</script>
