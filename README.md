# How to Connect MailerLite to Google Sheets (Free – No Zapier Required) - Beginner Friendly Guide
`A complete beginner guide to sync your MailerLite subscribers into Google Sheets automatically — without Zapier, Make, or paid tools.`
## Outcome : Every hour your subscriber list will sync into Google Sheets - 100% FREE !



## Step 1 — Create Your Google Sheet

1. Go to **Google Sheets**
2. Click **Blank**
3. Rename the spreadsheet (optional): `MailerLite Subscribers`
4. Rename the first tab to exactly: `Subscribers`


---

## Step 2 — Create Your MailerLite API Token (New API)

1. Log in to **MailerLite**
2. Go to **Integrations → Developer API**
3. Click **Create token**
4. Token name: `Google Sheets Subscribers`
5. Choose **All IPs allowed**
6. Click **Create token**
7. Copy the token and save it windows notepad

---

## Step 3 — Open Google Apps Script

1. In your Google Sheet click **Extensions → Apps Script**
2. Delete all existing code

---

## Step 4 — Paste the Sync Script

```javascript
function pullMailerLiteSubscribers() {
  const API_TOKEN = "PASTE_MAILERLITE_TOKEN_HERE";
  const SHEET_NAME = "Subscribers";

  const sheet = SpreadsheetApp.getActive().getSheetByName(SHEET_NAME);
  if (!sheet) throw new Error("Missing sheet tab named: " + SHEET_NAME);

  sheet.clearContents();
  sheet.appendRow([
    "email","name","last_name","company","country","state","city","zip",
    "status","subscribed_at","groups","id"
  ]);

  let cursor = null;

  while (true) {
    const url =
      "https://connect.mailerlite.com/api/subscribers" +
      (cursor ? `?cursor=${encodeURIComponent(cursor)}` : "");

    const res = UrlFetchApp.fetch(url, {
      method: "get",
      headers: {
        "Authorization": "Bearer " + API_TOKEN,
        "Accept": "application/json"
      }
    });

    const payload = JSON.parse(res.getContentText());
    const data = payload.data || [];

    if (data.length) {
      const rows = data.map(s => ([
        s.email || "",
        s.name || "",
        s.last_name || "",
        (s.fields && s.fields.company) || "",
        s.country || "",
        s.state || "",
        s.city || "",
        s.zip || "",
        s.status || "",
        s.subscribed_at || "",
        (s.groups || []).map(g => g.name).join(", "),
        s.id || ""
      ]));

      sheet.getRange(sheet.getLastRow()+1,1,rows.length,rows[0].length).setValues(rows);
    }

    cursor = payload.meta && payload.meta.next_cursor ? payload.meta.next_cursor : null;
    if (!cursor) break;
  }
}
```

Replace:

```
PASTE_MAILERLITE_TOKEN_HERE
```

with your real MailerLite API token.

Click **Save**.

---

## Step 5 — Run the Script Once

1. Click the function dropdown and choose **pullMailerLiteSubscribers**
2. Click **Run ▶️**
3. Click **Advanced → Go to project (unsafe) → Allow**

Your subscribers will appear in the sheet.

---

## Step 6 — Automate the Sync (Hourly)

1. Click the **Triggers ⏰** icon
2. Click **+ Add Trigger**
3. Set:

| Setting | Value |
|--------|------|
Function | pullMailerLiteSubscribers |
Deployment | Head |
Event source | Time-driven |
Type | Hour timer |
Interval | Every hour |

Click **Save**.

---

## You’re Done

MailerLite will now sync to Google Sheets every hour — automatically and free.

---

## Common Problems

**Nothing appears**
- Check your token is correct
- Check the sheet tab is named `Subscribers`
- Run the script manually once

**Google security warning**
- Click **Advanced → Allow**

**Token error**
- Ensure token is set to **All IPs allowed**

---

## Why This Is Better Than Zapier

| Zapier | This Method |
|------|-----------|
Paid | Free |
Limited | Unlimited |
Affiliate driven | Real automation |

---

## Final Result

You now have a live MailerLite CRM in Google Sheets with zero monthly cost.

---

**End of guide**

