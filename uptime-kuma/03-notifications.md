# 3 – Notifications

Uptime Kuma sends me an email through Gmail when a monitor goes **DOWN** and again
when it is back **UP**.

## Step 1 – Create a Gmail app password

Gmail does not accept your normal password for SMTP. You need an **app password**:

1. Google account → **Security** → turn on **2-Step Verification** (required for app passwords)
2. Google account → search for **App passwords** → create one, for example named `uptime-kuma`
3. Google shows a 16-character password **once**. Copy it straight into Uptime Kuma

> **Never** put this password in a README, a screenshot or a Git repo.
> If it leaks, delete it in the Google account. Your real password stays safe.

## Step 2 – Add the notification in Uptime Kuma

**Settings** → **Notifications** → **Setup Notification**:

| Field                  | Value                                     | Why                                      |
|------------------------|-------------------------------------------|------------------------------------------|
| Notification Type      | Email (SMTP)                              |                                          |
| Hostname               | `smtp.gmail.com`                          | Gmail's mail server                      |
| Port                   | `587`                                     | Standard port for sending with STARTTLS  |
| Security               | None / STARTTLS                           | Port 587 starts unencrypted and then upgrades to TLS. `TLS` would be port 465 |
| Username               | `<your-address>@gmail.com`                |                                          |
| Password               | The app password from step 1              |                                          |
| From / To              | Sender and receiver of the alerts         | The receiver should be an inbox you actually read |
| Default enabled        | On                                        | New monitors get this notification automatically |

## Step 3 – Test it

Click **Test** in the notification dialog. A test email should arrive within a minute.

**My honest status:** so far I have only used the Test button. That proves the SMTP settings
work. It does **not** prove that a real outage triggers an email.
A real test is on my list, see [Limitations & next steps](04-limitations-and-next-steps.md).
