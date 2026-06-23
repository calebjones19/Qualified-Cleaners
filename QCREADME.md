# Qualified Cleaners

A simple web app for account managers to select their accounts and qualified cleaners. Submissions go to a Google Sheet.

## Setup (two steps)

### Step 1 — Create the Google Sheet + Script

1. Go to [Google Sheets](https://sheets.google.com) and create a new spreadsheet
2. Name it **Qualified Cleaners**
3. In row 1, add these headers in columns A–D:
   - A1: `Timestamp`
   - B1: `Account Manager`
   - C1: `Account`
   - D1: `Qualified Cleaner`
4. Click **Extensions → Apps Script**
5. Delete any existing code and paste this:

```javascript
function doPost(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var data = JSON.parse(e.postData.contents);
  var manager = data.manager;

  // Remove existing rows for this manager
  var lastRow = sheet.getLastRow();
  for (var i = lastRow; i >= 2; i--) {
    if (sheet.getRange(i, 2).getValue() === manager) {
      sheet.deleteRow(i);
    }
  }

  // Add new rows
  var timestamp = new Date().toISOString();
  data.rows.forEach(function(row) {
    sheet.appendRow([timestamp, row.manager, row.account, row.cleaner]);
  });

  return ContentService
    .createTextOutput(JSON.stringify({ status: 'ok' }))
    .setMimeType(ContentService.MimeType.JSON);
}
```

6. Click **Save**, then click **Deploy → New deployment**
7. Set type to **Web app**
8. Set "Who has access" to **Anyone**
9. Click **Deploy** and copy the Web app URL

### Step 2 — Add the URL to the app

1. Open `index.html`
2. Find this line near the bottom:
   ```
   const SHEET_URL = "YOUR_GOOGLE_APPS_SCRIPT_URL";
   ```
3. Replace `YOUR_GOOGLE_APPS_SCRIPT_URL` with the URL you copied

### Step 3 — Deploy to GitHub Pages

1. Push this folder to your GitHub repo (`calebjones19`)
2. Go to repo **Settings → Pages**
3. Set source to **main branch / root**
4. GitHub will give you a public URL to share with your team

## That's it

Share the GitHub Pages URL with your account managers. Every submission overwrites their previous entry in the Google Sheet so you always have the latest data.
