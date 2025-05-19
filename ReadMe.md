# System Utility Client (Go Version)

This utility monitors various system parameters, runs periodically, and reports changes to a configured API endpoint. It is designed to be cross-platform (macOS, Windows, Linux) and consume minimal resources.

## Features

* Cross-platform execution.
* Checks:
    * Disk encryption status
    * OS update status
    * Antivirus presence
    * Inactivity sleep settings (target: ≤ 10 minutes)
* Background operation (simulated via a loop).
* Reports data only if there's a change from the last known state.
* Sends updates to a remote API endpoint.
* Consumes minimal resources.

## Prerequisites

* **For Running Pre-compiled Binaries (if provided):**
    * No special prerequisites other than the correct binary for your OS.
* **For Compiling from Source:**
    * Go (version 1.18 or later recommended). You can download it from [https://golang.org/dl/](https://golang.org/dl/).

## Installation / Setup

### Option 1: Using Pre-compiled Binaries (Recommended for End-Users)

In a typical distribution scenario, pre-compiled binaries for your operating system would be available for download (e.g., from a GitHub Releases page or a dedicated download link).

1.  **Download** the appropriate binary for your system:
    * `system_monitor_macos_amd64` (or `system_monitor_macos_arm64` for Apple Silicon) for macOS
    * `system_monitor_windows_amd64.exe` for Windows
    * `system_monitor_linux_amd64` for Linux
2.  **Place** the downloaded executable in a directory of your choice (e.g., `~/bin`, `/usr/local/bin`, or `C:\Program Files\SystemMonitor`).
3.  **(macOS/Linux Only)** Make the binary executable:
    ```bash
    chmod +x /path/to/your/downloaded/system_monitor_binary
    ```

### Option 2: Compiling from Source

If pre-compiled binaries are not available, or if you prefer to compile it yourself:

1.  **Download or Clone the Source Code:**
    * If you have the `system_monitor.go` file, save it to a directory.
2.  **Open your terminal or command prompt.**
3.  **Navigate** to the directory containing `system_monitor.go`.
4.  **Build the executable:**
    * **For your current OS/Architecture:**
        ```bash
        go build system_monitor.go
        ```
        This will create `system_monitor` (macOS/Linux) or `system_monitor.exe` (Windows).
    * **To Cross-Compile for a specific OS/Architecture (example):**
        * For Windows (64-bit):
            ```bash
            GOOS=windows GOARCH=amd64 go build -o system_monitor_windows_amd64.exe system_monitor.go
            ```
        * For Linux (64-bit):
            ```bash
            GOOS=linux GOARCH=amd64 go build -o system_monitor_linux_amd64 system_monitor.go
            ```
        * For macOS (Intel 64-bit):
            ```bash
            GOOS=darwin GOARCH=amd64 go build -o system_monitor_macos_amd64 system_monitor.go
            ```
        * For macOS (Apple Silicon ARM64):
            ```bash
            GOOS=darwin GOARCH=arm64 go build -o system_monitor_macos_arm64 system_monitor.go
            ```
5.  The compiled executable will be in the current directory. You can move it to a preferred location.

## Configuration

Before running the utility for the first time, you **must** configure the API endpoint. This is done by editing the `system_monitor.go` source file and recompiling if you are building from source.

1.  Open `system_monitor.go` in a text editor.
2.  Locate the `apiEndpoint` constant:
    ```go
    const (
        // !!! REPLACE WITH YOUR ACTUAL API ENDPOINT !!!
        apiEndpoint             = "[https://your-api-endpoint.com/report](https://your-api-endpoint.com/report)"
        // ... other constants
    )
    ```
3.  **Replace `"https://your-api-endpoint.com/report"` with the actual URL of your remote API.**
4.  If you modified the source, recompile the utility as described in "Compiling from Source."

*Future versions might support configuration via a separate file or environment variables.*

## Running the Utility

**Important:** Some system checks performed by this utility may require administrative or root privileges to access the necessary information.

### macOS

1.  Open **Terminal**.
2.  Navigate to the directory where `system_monitor_macos_amd64` (or your compiled binary) is located.
3.  Run the utility:
    * Without elevated privileges (some checks might fail or report "Unknown"):
        ```bash
        ./system_monitor_macos_amd64
        ```
    * With elevated privileges (recommended for full functionality):
        ```bash
        sudo ./system_monitor_macos_amd64
        ```
    You will be prompted for your administrator password.

### Windows

1.  Open **Command Prompt** or **PowerShell**.
2.  Navigate to the directory where `system_monitor_windows_amd64.exe` (or your compiled binary) is located.
3.  Run the utility:
    * Without elevated privileges (some checks might fail or report "Unknown"):
        ```cmd
        .\system_monitor_windows_amd64.exe
        ```
    * With elevated privileges (recommended for full functionality):
        Right-click on `system_monitor_windows_amd64.exe` and select "Run as administrator",
        OR
        Open Command Prompt/PowerShell as Administrator, then navigate and run:
        ```cmd
        .\system_monitor_windows_amd64.exe
        ```

### Linux

1.  Open your **Terminal**.
2.  Navigate to the directory where `system_monitor_linux_amd64` (or your compiled binary) is located.
3.  Run the utility:
    * Without elevated privileges (some checks might fail or report "Unknown"):
        ```bash
        ./system_monitor_linux_amd64
        ```
    * With elevated privileges (recommended for full functionality):
        ```bash
        sudo ./system_monitor_linux_amd64
        ```
    You will be prompted for your root or sudo password.

Once running, the utility will perform an initial check and then continue to check periodically (default: every 30 minutes). It will print status messages to the console and log detailed information to `system_monitor.log` in the same directory. A `system_monitor_last_state.json` file will also be created to store the last known state.

Press `Ctrl+C` in the terminal to stop the utility.

## True Background Operation (Daemon/Service)

The current version runs as a foreground process in your terminal. To run it as a true background daemon (Linux/macOS) or service (Windows), you would need to use OS-specific tools:

* **macOS:** `launchd` (create a `.plist` file in `~/Library/LaunchAgents` or `/Library/LaunchDaemons`)
* **Linux:** `systemd` (create a `.service` unit file in `/etc/systemd/system`) or `init.d` scripts.
* **Windows:** Windows Task Scheduler (for periodic runs) or tools like `NSSM (Non-Sucking Service Manager)` to register it as a Windows Service.

Setting this up is beyond the scope of these basic running instructions.

## Troubleshooting

* **"Permission denied" (macOS/Linux):** Ensure the binary is executable (`chmod +x ./binary_name`). If running, try with `sudo`.
* **Checks return "Unknown" or "Could not determine":** This often means the utility lacks the necessary permissions to run the underlying system commands. Try running with `sudo` (macOS/Linux) or as Administrator (Windows). It could also indicate that a command is not found or its output is unexpected on your specific OS version/distribution.
* **API Send Fails:**
    * Verify the `apiEndpoint` in the configuration is correct and reachable.
    * Check your network connection.
    * Inspect `system_monitor.log` for error messages from the API.
* **Log File:** Check `system_monitor.log` in the directory where the utility is run for detailed operational logs and error messages.

