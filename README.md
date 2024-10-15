# ClamAV Weekly Scan Automation Script

This script automates a weekly scan of ClamAV on your system. It checks if a scan has already been performed during the current week. If not, it initiates a full system scan, records the results and moves infected files to a quarantine folder. Notifications are displayed via the system notification area and graphical dialog boxes using Zenity, and optionally, audible alerts can be played.

## Table of Contents

1. [Requirements](#requirements)
2. [Installation](#installation)
3. [Configuration](#configuration)
   - Optional: Audio Alerts](#optional-audio-alerts)
4. [Automating the Script](#automating-the-script)
5. [Usage](#usage)
6. [Advanced Options](#advanced-options)
   - Customizing Scanning Directories](#customizing-scan-directories)
   - Customizing Notifications](#customize-notifications)
7. [Troubleshooting](#troubleshooting-problems)

## Requirements

To run the script, make sure you have the following software installed:

- **ClamAV**: The antivirus software to perform the scan.
- **Zenity**: For graphical notification dialog boxes.
- **libnotify-bin**: For sending desktop notifications.
- **mpg123 (optional)**: For audio alerts when threats are detected.

## Installation

### 1. Script Directory Configuration

Create a directory to store the script and the necessary assets:

```bash
mkdir -p ~/scripts/Weekly-clamscan
```

### 2. Copy the Script

Save the provided script in the directory you created. You can name it `clamav-weekly-scan.sh`. To make it executable, use the following command:

````bash
chmod +x ~/scripts/Weekly-clamscan/clamav-weekly-scan.sh
```

### (Optional) Audio Notifications Configuration

If you wish to receive audio notifications when a scan is completed or when threats are detected, add audio files to the `~/scripts/Weekly-clamscan/sounds/` directory. The recommended files are listed below:

- **`notification-sa.mp3`**: Plays a sound when a threat is detected (Severity Alert).
- **`notification-na.mp3`**: Plays a sound when no threats are detected (Normal Alert).

If you do not have audio files, you can download free sound effects or record your own and place them in the `~/scripts/Weekly-clamscan/sounds/` directory.

## Configuration

The script comes preconfigured to:

- Log scan results to `~/Clamscan/Scans/`.
- Move infected files to `~/Clamscan/Quarantine/`.
- Exclude certain directories (`/sys`, `/proc`, `/dev`, `/run`, `/tmp`, `/var/run`) to avoid scanning sensitive system directories.

You can modify these paths directly in the script by changing the `LOG_DIR`, `QUARANTINE_DIR` variables and scan exclusions.

### Optional: Audio Alerts

If you want audio alerts when the scan is complete, make sure the files `notification-sa.mp3` (for threats) and `notification-na.mp3` (for non-threats) are placed in the sounds folder.

The script is already configured to play these files if they exist. If the files are not present, the script will continue without audio alerts.

## Automating the Script

To automate the scan to run every week, you can use `anacrontab`, which is useful for scheduling tasks that should run periodically but can be skipped if the system is off.

### 1. Open Anacrontab

Run the following command to edit your anacrontab:

````bash
sudo nano /etc/anacrontab
```

### 2. Add the Anacron Job

Add the following line to schedule the script to run every Monday at 3:00 AM:

````bash
1 3 * * 1 ~/scripts/Weekly-clamscan/clamav-weekly-scan.sh
```

This configuration means:

- **1**: The frequency in days (this job runs every week).
- **3**: The number of hours after midnight to run the job (3 AM).
- This job can be run on any day of the month.
- This job can be run in any month.
- **1**: This job will only run on Mondays.

You can adjust the schedule according to your needs. For example, to run the scan every day at 2:00 AM, you could use:

````bash
1 2 * * * ~/scripts/Weekly-clamscan/clamav-weekly-scan.sh
```

## Usage

Once the script is configured and scheduled, it will run automatically according to the schedule you defined in ``anacrontab`. You can also run it manually by running the script directly:

````bash
~/scripts/Weekly-clamscan/clamav-weekly-scan.sh
```

### What Happens When the Script Runs:

- ClamAV scans your system, excluding sensitive directories.
- If infected files are found, they are moved to the Quarantine folder (`~/Clamscan/Quarantine/`).
- The scan results are recorded in the Scans folder (`~/Clamscan/Scans/`).
- If threats are detected, the script notifies you with:
  - A pop-up message using Zenity.
  - A desktop notification using `notify-send`.
  - An audio alert (if configured).
- If no threats are found, a success notification is displayed.

## Advanced Options

### Customizing Scan Directories

By default, the script scans your entire system starting from `/`. To change this, you can modify the `clamscan` command in the script to target specific directories.

For example, to scan only your home directory:

````bash
sudo clamscan -r -i -o --move=“$QUARANTINE_DIR” “$HOME” > “$LOGFILE” 2>&1
```

### Customizing Notifications

- Zenity dialogs are used for important pop-up messages, especially when threats are encountered. You can change the behavior or replace Zenity with another tool if you wish.
- `notify-send` sends system notifications. You can customize notification icons, message titles or even add more detailed logging.

## Troubleshooting

### ClamAV Outdated Definitions

If ClamAV does not have the latest virus definitions, it may not work as expected. Be sure to update the definitions regularly:

````bash
sudo freshclam
```

If the ``freshclam`` command fails, you may need to check your internet connection or ClamAV configuration.

### Permission Problems

The script runs `clamscan` with `sudo`, which is necessary to scan system directories. Make sure you have the necessary permissions by adding yourself to the sudoers file or running the script as root.

### Sound Doesn't Play

If the script does not play sound, check the following:

- `mpg123` is installed (`sudo apt install mpg123`).
- The sound files exist in the correct directory (`~/scripts/Weekly-clamscan/sounds/`).
- You can manually test the sound files by running:

````bash
mpg123 ~~/scripts/Weekly-clamscan/sounds/notification-sa.mp3
```

If the sound plays, the script should work correctly when it detects threats.

Feel free to customize the script to your specific requirements. For any problems, consult the ClamAV, Zenity or Anacron documentation, or open an issue in the repository where this script is hosted.

By following the steps in this tutorial, your system should be configured to automatically scan for viruses on a weekly basis and notify you of any problems found.


