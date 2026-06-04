---
title: "How to Disable Automatic Startup When Opening the Lid on Apple Silicon Macs"
description: "A practical guide for disabling automatic startup behavior on Apple Silicon Macs running macOS Sequoia."
date: 2026-06-01
draft: false
tags:
  - macOS
  - Apple Silicon
  - MacBook
  - IT Support
  - Troubleshooting
categories:
  - macOS
---

# Overview

Apple Silicon MacBooks are designed to provide a seamless user experience.

On modern MacBook models equipped with Apple M-series processors and recent versions of macOS, the system will automatically power on when:

- Opening the laptop lid
- Connecting a power adapter

For most users, this behavior is convenient and makes the device feel more responsive.

However, there are situations where automatic startup may be undesirable, especially for IT professionals, developers, technicians, and users who frequently transport, service, or troubleshoot their devices.

Fortunately, Apple now provides a supported method to control this behavior using Terminal commands.

---

# Why Would Someone Want to Disable Automatic Startup?

While automatic startup improves convenience, there are several scenarios where users may prefer manual control.

## Device Maintenance

When performing hardware inspections, cleaning, or repairs, users may not want the MacBook to power on every time the lid is opened.

## Battery Preservation

Some users prefer to keep devices completely powered off during storage or travel.

Automatically starting the system when connecting power can be inconvenient.

## IT Deployment and Troubleshooting

IT administrators often work with multiple devices simultaneously.

Having systems automatically boot during staging, inventory checks, or maintenance can interrupt workflows.

## Personal Preference

Some users simply prefer controlling exactly when their computer starts.

---

# What Changes After Disabling This Feature?

Disabling automatic startup does **not** prevent the Mac from being powered on normally.

You can still:

- Press the power button
- Use the keyboard or Touch ID button to start the device

The change only affects:

- Startup when opening the lid
- Startup when connecting external power

---

# Requirements

This feature applies to:

- Apple Silicon Macs (M1, M2, M3, M4, and newer)
- Recent versions of macOS, including macOS Sequoia

Administrative privileges are required because the configuration is stored in NVRAM.

---

# How to Configure Startup Behavior

Open Terminal:

**Applications → Utilities → Terminal**

You may also use any third-party terminal application.

Depending on your preference, run one of the following commands.

## Disable Automatic Startup for Both Lid Opening and Power Connection

```bash
sudo nvram BootPreference=%00
```

This prevents the Mac from automatically starting when:

- Opening the lid
- Connecting a charger

---

## Disable Automatic Startup Only When Opening the Lid

```bash
sudo nvram BootPreference=%01
```

The Mac will still automatically start when connected to power.

---

## Disable Automatic Startup Only When Connecting Power

```bash
sudo nvram BootPreference=%02
```

The Mac will still automatically start when the lid is opened.

---

## Restore Default Behavior

To restore Apple's default startup behavior:

```bash
sudo nvram -d BootPreference
```

After removing the setting, the Mac will once again automatically start when:

- Opening the lid
- Connecting power

---

# Applying the Change

After entering the command:

1. Enter your administrator password.
2. Press Enter.

Note that Terminal will not display characters while typing the password. This is normal behavior.

To verify the change:

1. Completely shut down the Mac.
2. Close the lid.
3. Connect power or open the lid.
4. Observe whether the Mac starts automatically.

If the system remains powered off until manually started, the configuration has been applied successfully.

---

# Why Apple Enabled Automatic Startup by Default

Apple's goal is to reduce friction and make the computer feel instantly available.

For most users:

- Open lid → Start working
- Connect power → Resume working

This behavior aligns with the appliance-like experience Apple has been moving toward for many years.

However, professional users often have different requirements than general consumers, which is why having the option to customize this behavior is valuable.

---

# Why This Matters for IT Support

In enterprise environments, support technicians frequently perform:

- Device deployment
- Hardware inspection
- Troubleshooting
- Asset inventory
- Repair and maintenance

Automatic startup can sometimes create unnecessary interruptions during these workflows.

Understanding how to control this behavior is useful knowledge for:

- IT Support Technicians
- Helpdesk Analysts
- Mac Administrators
- Jamf Administrators
- Endpoint Management Teams

---

# Final Thoughts

Automatic startup is one of those small macOS behaviors that many users simply accept without realizing it can be changed.

For IT professionals, support technicians, and power users, having control over when a device powers on can make maintenance, troubleshooting, and device management more predictable.

Apple's NVRAM-based configuration provides a simple and supported way to customize startup behavior while preserving the ability to power on the system normally when needed.

Small changes like this often improve the overall user experience and provide greater flexibility for technical workflows.