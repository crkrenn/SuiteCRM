To use a Google App-Key to send emails from an Ubuntu server, you need to configure your email-sending application (like `ssmtp`, `msmtp`, or a Python script using `smtplib`) to authenticate using the Google App-Key. Here is a step-by-step guide using `msmtp` as an example:

### Step 1: Generate Google App-Key
1. **Go to Google Account Security Settings**:
   Navigate to your [Google Account Security](https://myaccount.google.com/security) page.

2. **Enable 2-Step Verification**:
   Ensure that 2-Step Verification is enabled for your Google account.

3. **Generate an App Password**:
   - Under the "Signing in to Google" section, select "App passwords".
   - You might need to sign in again.
   - Select "Mail" as the app and "Other (Custom name)" for the device, and then name it appropriately (e.g., "Ubuntu Server").
   - Click "Generate" and note the 16-character password provided. This is your App-Key.

### Step 2: Install `msmtp`
```bash
sudo apt update
sudo apt install msmtp msmtp-mta
```

### Step 3: Configure `msmtp`
Create or edit the `~/.msmtprc` file with the following configuration:

```plaintext
# Example ~/.msmtprc

# Gmail configuration
account default
auth on
tls on
tls_trust_file /etc/ssl/certs/ca-certificates.crt
logfile ~/.msmtp.log

# SMTP server configuration
host smtp.gmail.com
port 587
from your-email@gmail.com
user your-email@gmail.com
password 16-character-app-password
```

Replace `your-email@gmail.com` with your actual Gmail address and `16-character-app-password` with the App-Key you generated.

### Step 4: Set Permissions
Ensure the `~/.msmtprc` file has the correct permissions to keep your credentials secure:
```bash
chmod 600 ~/.msmtprc
```

### Step 5: Send a Test Email
You can send a test email using `msmtp` with the following command:
```bash
echo -e "Subject: Test Email\n\nThis is a test email from msmtp." | msmtp recipient@example.com
```

Replace `recipient@example.com` with the actual recipient's email address.

### Using Python `smtplib`
If you prefer to use a Python script to send emails, here’s how to do it with `smtplib`:

1. **Install Python SMTP library (if necessary)**:
   ```bash
   sudo apt install python3
   ```

2. **Create a Python script**:
   ```python
   import smtplib
   from email.mime.text import MIMEText

   # Email details
   sender_email = "your-email@gmail.com"
   receiver_email = "recipient@example.com"
   app_password = "16-character-app-password"
   subject = "Test Email"
   body = "This is a test email from Python smtplib."

   # Create the email
   msg = MIMEText(body)
   msg["Subject"] = subject
   msg["From"] = sender_email
   msg["To"] = receiver_email

   # Send the email
   try:
       with smtplib.SMTP("smtp.gmail.com", 587) as server:
           server.starttls()
           server.login(sender_email, app_password)
           server.sendmail(sender_email, receiver_email, msg.as_string())
           print("Email sent successfully!")
   except Exception as e:
       print(f"Error: {e}")
   ```

3. **Run the Python script**:
   ```bash
   python3 send_email.py
   ```

Replace the placeholders with the appropriate information, such as your Gmail address, the recipient's address, and the App-Key.

### Conclusion
By following these steps, you should be able to send emails from your Ubuntu server using a Google App-Key for authentication. Whether you use `msmtp` or a Python script with `smtplib`, this method ensures a secure and reliable way to send emails via Gmail.

### References
- [Google Account Security](https://myaccount.google.com/security)
- [msmtp documentation](https://marlam.de/msmtp/)
- [Python `smtplib` documentation](https://docs.python.org/3/library/smtplib.html)
