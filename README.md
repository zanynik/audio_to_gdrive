# Audio to Drive Recorder

A simple, aesthetically pleasing web application that allows you to record voice notes and type text notes, which are then automatically uploaded to your Google Drive with timestamps.

## Overview
This project serves as a quick-capture interface for your thoughts. Whether you are on your phone or laptop, you can instantly record audio or jot down text. All files are saved to a specific folder in your Google Drive (Nikhil's Drive by default) with a timestamped filename (e.g., `Audio_20240101_120000.mp3` or `Note_20240101_120000.txt`).

## Features
- **Voice Mode**: Record audio with a real-time visualizer.
- **Text Mode**: A distraction-free notepad for typing thoughts.
- **Fail-Safe Uploads**: If an upload fails (e.g., poor network), you can retry or save the file locally to your device.
- **Mobile First**: Designed to look and feel like a native app on mobile devices.

## How to Use on iPhone
For the best experience, add this website to your Home Screen:
1. Open the website in **Safari**.
2. Tap the **Share** icon (square with an arrow).
3. Scroll down and tap **"Add to Home Screen"**.
4. Name it (e.g., "Note Taker") and tap **Add**.

Now you can launch it instantly like a native app to capture thoughts immediately.

## Usefulness: The Second Brain
By automatically syncing every stray thought, idea, or voice memo to a central Google Drive folder, you create a raw repository of your mind. These files can later be processed, transcribed, or organized into a "Second Brain" system (like Obsidian, Notion, or Evernote), allowing you to build upon your ideas rather than losing them.

## Setup Guide (For Other Users)
To use this project with your own Google Drive, you need to deploy a Google Apps Script.

1.  **Create the Google Script**:
    *   Go to [script.google.com](https://script.google.com/).
    *   Click **New Project**.
    *   Paste the following code into the script editor:

    ```javascript
    function doPost(e) {
      try {
        var data = JSON.parse(e.postData.contents);
        var filename = data.filename;
        var fileData = data.fileData;
        var mimeType = data.mimeType;
        
        // Decode base64
        var decoded = Utilities.base64Decode(fileData);
        var blob = Utilities.newBlob(decoded, mimeType, filename);
        
        // Save to specific folder (Optional: Replace with your Folder ID)
        // var folder = DriveApp.getFolderById("YOUR_FOLDER_ID");
        // var file = folder.createFile(blob);
        
        // Or save to Root Drive
        var file = DriveApp.createFile(blob);
        
        return ContentService.createTextOutput(JSON.stringify({ "status": "success", "fileId": file.getId() }))
          .setMimeType(ContentService.MimeType.JSON);
          
      } catch (error) {
        return ContentService.createTextOutput(JSON.stringify({ "status": "error", "message": error.toString() }))
          .setMimeType(ContentService.MimeType.JSON);
      }
    }
    ```

2.  **Deploy the Web App**:
    *   Click **Deploy** > **New deployment**.
    *   Select type: **Web app**.
    *   Description: "Audio Upload".
    *   **Execute as**: "Me" (your email).
    *   **Who has access**: "Anyone" (allows the frontend to upload without login prompts).
    *   Click **Deploy**.

3.  **Connect to Frontend**:
    *   Copy the **Web App URL** (ends in `/exec`).
    *   Open `index.html` in this project.
    *   Find the `const SCRIPT_URL = "..."` line in the JavaScript section.
    *   Replace the URL with your new Web App URL.

You are now ready to upload to your own Drive!
