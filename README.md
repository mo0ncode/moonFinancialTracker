# moonFinancialTracker
Financial Tracker with iPhone Shortcut &amp; Google Sheets

An iPhone Shortcut that opens automatically when you close your banking app, asks for the transaction details, and logs them to a Google Sheet.

**Shortcut:** [Add Expense](https://www.icloud.com/shortcuts/6c15a9c8997f48e3b8c2eba79b3cf771)

---

## How It Works

```
Close banking app
      │
      ▼
iOS Personal Automation ("App → Is Closed")
      │
      ▼
"Add Expense" shortcut runs
      │
      ▼
Prompts: amount, merchant, category, notes
      │
      ▼
POST request (JSON) → Google Apps Script Web App
      │
      ▼
New row appended in Google Sheets
```

Shortcuts can't write to Google Sheets directly, so a small Google Apps Script acts as the receiving endpoint.

---

## Features

- Triggers automatically when you leave your banking app
- Quick prompts to capture each transaction
- Timestamp added automatically
- Data stored in your own Google Sheet
- No third-party apps or paid services

---

## Requirements

- iPhone with iOS 15 or later and the **Shortcuts** app
- A Google account with access to Google Sheets and Apps Script
- A banking app installed on your phone

---

## Setup

### 1. Create the Google Sheet

Create a new sheet with these headers in row 1:

| A | B | C | D | E |
|---|---|---|---|---|
| Date | Amount | Merchant | Category | Notes |

### 2. Add the Apps Script

1. In the sheet, go to **Extensions → Apps Script**.
2. Replace the contents with:

```javascript
function doPost(e) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const data = JSON.parse(e.postData.contents);

  sheet.appendRow([
    new Date(),
    data.amount,
    data.merchant,
    data.category,
    data.notes || ""
  ]);

  return ContentService
    .createTextOutput(JSON.stringify({ status: "success" }))
    .setMimeType(ContentService.MimeType.JSON);
}
```

3. Click **Deploy → New deployment → Web app**.
4. Set **Execute as:** Me and **Who has access:** Anyone.
5. Click **Deploy** and copy the **Web app URL**.

> ⚠️ Anyone with this URL can add rows to your sheet. Keep it private and don't share it publicly.

### 3. Install the Shortcut

1. Open the [shortcut link](https://www.icloud.com/shortcuts/6c15a9c8997f48e3b8c2eba79b3cf771) on your iPhone.
2. Tap **Add Shortcut**.
3. Edit the **Get Contents of URL** action and paste your Apps Script Web App URL.

### 4. Create the Automation

1. Open **Shortcuts → Automation → + → Create Personal Automation**.
2. Choose **App**, then select your banking app.
3. Select **Is Closed** (deselect "Is Opened").
4. Tap **Next → Add Action → Run Shortcut** and choose **Add Expense**.
5. Set **Run Immediately** and turn off **Notify When Run**. This lets it run without a confirmation tap.

---

## Usage

1. Make a payment or check a transaction in your banking app.
2. Close the app.
3. The shortcut opens and asks for the details.
4. Enter them and the row appears in your Google Sheet.

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Automation doesn't trigger | Check that **Is Closed** is selected and the correct app is chosen |
| Asks for confirmation each time | Set the automation to **Run Immediately** |
| Nothing appears in the sheet | Make sure the Web App is deployed with access set to **Anyone**, and that the URL in the shortcut is correct |
| Edits to the script have no effect | Create a **new deployment version** after changing the code |
| Triggers when you didn't make a transaction | Add an "If" check in the shortcut, or a "Cancel" option in the first prompt |

---

## Customization Ideas

- Add or change categories (Food, Transport, Bills, etc.)
- Add a payment method or account field
- Use a currency selector
- Add a monthly summary tab with `SUMIF` or a pivot table
- Build a chart or dashboard in Google Sheets

---

## Privacy

- Your data goes only to your own Google Sheet.
- The shortcut does **not** read your banking app, so it never sees your balance or login details. It only records what you type in.

---

## Limitations

- iOS can't read transaction data from another app, so entry is manual.
- Automation triggers when the app closes, not when a payment is made.
- Requires an internet connection to log.

---

## License

MIT, free to use and modify.


