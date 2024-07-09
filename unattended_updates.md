To automate daily updates and upgrades on an Ubuntu system, you can use the built-in `unattended-upgrades` package, which is designed to handle automatic updates. Here’s how to set it up:

### Step 1: Install Unattended Upgrades

If it’s not already installed, you can install the `unattended-upgrades` package:

```bash
sudo apt update
sudo apt install unattended-upgrades
```

### Step 2: Enable Unattended Upgrades

Enable the unattended-upgrades service:

```bash
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

This command will prompt you to confirm enabling automatic updates.

### Step 3: Configure Unattended Upgrades

Edit the configuration file to customize the behavior of unattended-upgrades:

```bash
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
```

In this file, you can specify which packages to upgrade automatically. By default, it includes:

```plaintext
Unattended-Upgrade::Allowed-Origins {
    "${distro_id}:${distro_codename}-security";
    // "${distro_id}:${distro_codename}-updates";
    // "${distro_id}:${distro_codename}-proposed";
    // "${distro_id}:${distro_codename}-backports";
};
```

You can uncomment other lines if you want to include updates for other categories (e.g., `${distro_id}:${distro_codename}-updates` for regular updates).

### Step 4: Schedule Daily Updates

To run updates daily, configure the `apt` periodic configuration file:

```bash
sudo nano /etc/apt/apt.conf.d/10periodic
```

Add or update the following lines:

```plaintext
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Download-Upgradeable-Packages "1";
APT::Periodic::AutocleanInterval "7";
APT::Periodic::Unattended-Upgrade "1";
```

These settings ensure that the package list is updated daily, upgrades are downloaded and installed daily, and old packages are cleaned up weekly.

### Step 5: (Optional) Configure Notification Emails

To receive email notifications of updates, edit the `50unattended-upgrades` configuration file:

```bash
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
```

Uncomment and set the email configuration:

```plaintext
Unattended-Upgrade::Mail "your-email@example.com";
Unattended-Upgrade::MailOnlyOnError "true";
```

Replace `your-email@example.com` with your actual email address.

### Step 6: (Optional) Reboot if Required

If you want your system to reboot automatically after certain updates, you can configure that in the `50unattended-upgrades` file as well:

```plaintext
Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-Time "02:00";
```

This will schedule automatic reboots at 2 AM if required by updates.

### Conclusion

By following these steps, you can ensure that your Ubuntu system performs daily updates and upgrades automatically, helping to keep your system secure and up to date with the latest patches.

### References
- [Ubuntu Community Help Wiki - Automatic Updates](https://help.ubuntu.com/community/AutomaticUpdates)
- [Debian Administration - Automatic Updates](https://www.debian-administration.org/article/162/Automatic_Updates_with_Unattended_Upgrades)
- [Ubuntu Manpage: unattended-upgrade](https://manpages.ubuntu.com/manpages/bionic/man8/unattended-upgrade.8.html)

No, the unattended-upgrades configuration does not directly support using `msmtp` for sending email notifications. However, you can configure the unattended-upgrades to use a custom script to send emails, which can leverage `msmtp`.

Here's how you can configure `unattended-upgrades` to use `msmtp` for sending email notifications:

### Step 1: Install `msmtp`

If you haven't already installed `msmtp`, do so with the following commands:

```bash
sudo apt update
sudo apt install msmtp msmtp-mta
```

### Step 2: Configure `msmtp`

Create or edit the `~/.msmtprc` file with your email settings:

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

Ensure the `~/.msmtprc` file has the correct permissions:

```bash
chmod 600 ~/.msmtprc
```

### Step 3: Create a Custom Email Script

Create a script that `unattended-upgrades` can use to send email notifications. For example, create a script at `/usr/local/bin/unattended-upgrade-email`:

```bash
sudo nano /usr/local/bin/unattended-upgrade-email
```

Add the following content to the script:

```bash
#!/bin/bash

recipient="your-email@example.com"
subject="Unattended Upgrade Report"
email_body=$(cat)

/usr/bin/msmtp -a default -t <<EOF
To: $recipient
Subject: $subject

$email_body
EOF
```

Make the script executable:

```bash
sudo chmod +x /usr/local/bin/unattended-upgrade-email
```

### Step 4: Configure Unattended Upgrades to Use the Custom Script

Edit the `50unattended-upgrades` configuration file:

```bash
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
```

Set the `Unattended-Upgrade::Mail` option to your email address and configure it to use the custom script:

```plaintext
Unattended-Upgrade::Mail "your-email@example.com";
Unattended-Upgrade::MailReport "always";
Unattended-Upgrade::MailCmd "/usr/local/bin/unattended-upgrade-email";
```

### Step 5: Test the Configuration

To test the email notification, you can manually run unattended-upgrades and check if an email is sent:

```bash
sudo unattended-upgrade -d
```

### Conclusion

By following these steps, you will configure `unattended-upgrades` to send email notifications using `msmtp`. This setup leverages a custom script to handle the email sending process, allowing you to integrate `msmtp` or any other mail client of your choice.

