
---
title: "Reviving an Old Fuji-Xerox Printer with Linux, CUPS, and AirPrint"
date: 2026-10-23
draft: false
description: "How I turned a 2012 Mac mini into a Linux print server for a Fuji-Xerox DocuPrint CP105b using an open-source HBPLv1 driver, CUPS, and AirPrint."
tags:
  - Linux
  - Debian
  - Ubuntu
  - Mac mini
  - CUPS
  - AirPrint
  - Open Source
  - Printer
categories:
  - Linux
  - Homelab
---

Sometimes, an old piece of hardware becomes difficult to use not because it is broken, but because its software support has disappeared.

That was exactly the situation with my Fuji-Xerox DocuPrint CP105b color laser printer.

I originally chose this printer because it offered good value for money. It was an affordable color laser printer that met my needs, and I didn't expect it to become a problem years later.

What I overlooked was the printer language it used: **HBPLv1.0**.

After experimenting with different ways to keep the printer useful, I eventually found a solution that gave both the printer and my old 2012 Mac mini a new purpose.

I installed Linux on the Mac mini, compiled an open-source printer driver, and configured CUPS to share the printer over my home network. I also got AirPrint working, allowing supported Apple devices to discover and use the printer without installing the original manufacturer driver on each device.

Here is how I approached the problem.

## 1. The hidden cost of choosing an inexpensive printer

When I bought the Fuji-Xerox DocuPrint CP105b, my main consideration was value for money.

At the time, it was a practical choice. However, I didn't pay much attention to the printer's underlying printing language.

Unlike printers that support widely used page description languages or modern driverless printing standards, the CP105b uses **Host Based Printer Language version 1 (HBPLv1)**.

This matters because the printer depends on software to convert documents into a format it understands.

The open-source [HBPLv1.0 Plus Printer Driver](https://github.com/macos2/HBPLv1.0_Plus-Printer-driver) explicitly lists the Fuji-Xerox DocuPrint CP105b among its supported models.

Without a compatible driver, having a working USB connection is not enough. The computer still needs a way to translate a document into the printer's language.

In other words, the printer itself was still usable. The problem was the software required to communicate with it.

Looking back, I learned that the purchase price of a printer is only part of its long-term cost. Driver availability and support for future operating systems matter too.

## 2. Why I stopped relying on macOS

For a while, my 2012 Mac mini running macOS Catalina (10.15) remained useful because it could still run the old printer driver.

However, this solution had several limitations.

### The old Mac mini could print, but not conveniently share the printer

The printer worked locally on the Mac mini, but I couldn't get the cross-device printing experience I wanted with the available driver setup.

If I needed to print something from another computer, the practical workaround was to copy the file to the Mac mini and print it locally.

That was inconvenient, especially for a printer that should ideally be available to every device on my home network.

### Modern software support was disappearing

The Mac mini was also becoming less useful as a general-purpose computer.

macOS Catalina was too old for many of the newer applications and services I wanted to use. Keeping the machine running primarily to support one printer no longer seemed like a good use of the hardware.

### The driver situation was getting worse

Newer Apple Silicon Macs also presented a problem: I couldn't rely on a current manufacturer-provided driver for this legacy printer.

The manufacturer no longer maintains the product in the way it once did, and I couldn't count on receiving a new driver for newer macOS releases.

I didn't want to replace a printer that was still working simply because its software support had become obsolete.

I needed a different approach.

## 3. Finding an open-source HBPLv1 driver

The turning point was finding the [HBPLv1.0 Plus Printer Driver on GitHub](https://github.com/macos2/HBPLv1.0_Plus-Printer-driver).

This project provides a Linux driver for printers that use HBPLv1.0, including the DocuPrint CP105b.

The driver converts print data into the format required by the printer and integrates with CUPS.

The Plus version also includes several useful improvements, such as printer wake-up support, manual duplex printing, additional print-quality options, and configurable installation paths.

The repository provides a build process using standard tools such as `gcc` and `make`. It also requires `liblcms2-utils` for the `psicc` utility used to process the driver's color profile files.

This was exactly the kind of solution I was looking for: an open-source project that could keep my existing hardware useful without depending on a new proprietary driver.

## 4. Turning the Mac mini into a Linux print server

I had already decided to repurpose the 2012 Mac mini as a Linux server.

Instead of keeping macOS Catalina solely for printing, I installed Debian 13.7 (Trixie) and used the machine as a dedicated print server.

The printer connects to the Mac mini through USB. The Mac mini handles the driver, print queue, and network sharing.

The basic software components are:

- **CUPS** — manages printer queues and print jobs.
- **HBPLv1.0 Plus driver** — converts documents into a format the CP105b understands.
- **Avahi** — provides local network service discovery through mDNS/DNS-SD, which is important for Bonjour and AirPrint discovery.

This setup separates the printer hardware from the computers that use it. Client devices send print jobs to the Linux server, and the server handles the printer-specific work.

## 5. Installing CUPS and the build dependencies

After installing Debian, I installed CUPS, the tools required to compile the driver, and the packages needed for service discovery.

```bash
sudo apt update

sudo apt install \
  git \
  build-essential \
  autoconf \
  automake \
  libtool \
  pkg-config \
  liblcms2-utils \
  cups \
  cups-filters \
  cups-ipp-utils \
  avahi-daemon \
  avahi-utils
```

The main packages have different responsibilities:

- `build-essential` provides the compiler and standard build tools.
- `git` downloads the driver source code.
- `liblcms2-utils` provides `psicc`, which the driver uses for color profile processing.
- `cups` provides the printing system and administration interface.
- `cups-filters` provides additional document-processing filters.
- `cups-ipp-utils` provides useful IPP diagnostic utilities.
- `avahi-daemon` and `avahi-utils` support local network discovery.

I then checked that the printer was detected over USB:

```bash
lsusb
```

The output identified the device as:

```text
0550:00f5 Fuji Xerox Co., Ltd DocuPrint CP105 b
```

This confirmed that the operating system could see the printer at the USB level.

It was an important first check, but it didn't yet prove that printing would work. The driver and CUPS configuration still needed to be installed.

## 6. Compiling and installing the driver

I downloaded the source code from the project repository:

```bash
git clone https://github.com/macos2/HBPLv1.0_Plus-Printer-driver.git

cd HBPLv1.0_Plus-Printer-driver
```

The repository includes a `makefile` and a `configure` script. Because the default installation path is `/usr`, I could use the default configuration.

I compiled and installed the driver:

```bash
make
sudo make install
```

The `make` command compiled the driver, while `make install` installed the driver components and printer description files.

The project includes PPD files under its `ppd` directory. These files describe the printer's capabilities and tell CUPS which driver and print options to use.

After installation, the next step was to create a CUPS printer queue using the appropriate installed PPD.

The key point is that installing the driver alone doesn't automatically create a working printer queue. CUPS still needs to know which physical device to use and which driver to apply to it.

For reference, the source project documents the build process and installation options here:

[HBPLv1.0 Plus Printer Driver — README](https://github.com/macos2/HBPLv1.0_Plus-Printer-driver)

## 7. Adding the printer to CUPS

Once the driver was installed, I configured the printer in CUPS.

The CUPS web interface was available at:

```text
http://localhost:631
```

From the administration interface, I added the USB-connected Fuji-Xerox printer and selected the corresponding PPD installed by the driver.

The printer queue was named:

```text
Fuji_Xerox_CP105
```

After completing the configuration, I sent a CUPS test page to the printer.

**The test page printed successfully.**

That was the most important milestone in the project. It confirmed that the operating system, USB connection, CUPS queue, and HBPLv1 driver were working together.

At this point, the printer was usable from the server itself. The remaining task was to make it accessible to other devices on the network.

## 8. Sharing the printer over the network

CUPS supports sharing a locally connected printer with other computers.

I enabled printer sharing and marked the queue as shared:

```bash
sudo cupsctl --share-printers

sudo lpadmin \
  -p Fuji_Xerox_CP105 \
  -o printer-is-shared=true
```

These commands enable CUPS printer sharing and mark the specified queue for sharing.

CUPS uses IPP for network printing. This means compatible client devices can send print jobs to the Linux server rather than communicating directly with the USB printer.

I also verified that the CUPS service was advertising on the local network. The server was using the address `192.168.2.6` and the standard CUPS port, `631`.

The printer was no longer limited to applications running directly on the Mac mini.

Instead, it could be presented as a shared printer for other devices on the same local network.

For more information, see the [CUPS printer-sharing documentation](https://www.cups.org/doc/sharing.html).

## 9. Enabling AirPrint discovery

Network printing is useful, but I also wanted the printer to be convenient for iPhones, iPads, and Macs.

That's where Bonjour and AirPrint come in.

CUPS manages the print queue and the printer driver. Avahi provides local network service discovery through mDNS/DNS-SD, allowing compatible clients to discover advertised services without manually entering an IP address.

In my setup, Avahi was installed alongside CUPS, and the shared printer became discoverable through the local network's service discovery mechanism.

This allowed the printer to be used through AirPrint-compatible client devices.

The important distinction is that **AirPrint discovery and printer-language support are two different things**.

AirPrint makes the printer easier to discover and use from supported devices. The HBPLv1 driver on the Linux server still performs the printer-specific conversion needed by the CP105b.

Together, these components bridge the gap between modern client devices and an older USB printer.

## 10. The result: a printer that works across the network

After compiling the driver, creating the CUPS queue, and configuring printer sharing, the original problem was solved.

The Fuji-Xerox DocuPrint CP105b could now be used as a shared network printer rather than requiring me to copy files to the old Mac mini and print locally.

The new arrangement has several advantages:

- I can keep using a printer that still works.
- I no longer need to log in to macOS Catalina just to print.
- Other devices can access the shared printer over the local network.
- Compatible Apple devices can discover it through AirPrint.
- The Mac mini now runs a useful Linux service instead of being dedicated to an obsolete desktop operating system.

The hardware hasn't changed. The difference is the software architecture around it.

## 11. What I learned from this project

This project reminded me that hardware can outlast the software ecosystem that originally supported it.

When I bought the CP105b, I focused on its price and capabilities. I didn't consider how much its long-term usability would depend on a proprietary printer language and manufacturer driver support.

The printer eventually became difficult to use not because the hardware had failed, but because the original software support was no longer a practical option.

Linux, CUPS, and an open-source driver provided a way forward.

The 2012 Mac mini also turned out to be a good fit for the job. It doesn't need a graphical desktop or modern desktop applications to act as a print server. It only needs to run a few lightweight services reliably.

Most importantly, this wasn't just about keeping old hardware alive. It was about making it more convenient to use than before.

A printer that once required local operation on an aging Mac can now be shared across the home network, including with compatible Apple devices.

## Conclusion

Choosing an inexpensive printer seemed like a good decision at the time, but I underestimated the long-term importance of driver support.

Fortunately, the CP105b uses a printing language for which an open-source Linux driver exists.

By repurposing my old Mac mini, compiling the HBPLv1.0 Plus driver, and configuring CUPS and Avahi, I turned an aging local printing setup into a shared network printing service with AirPrint support.

It's a small home-lab project, but it demonstrates something I find valuable about Linux and open source: when official support disappears, a community-maintained solution can sometimes extend the useful life of hardware for years.

My old Mac mini now has a clear purpose, and my old Fuji-Xerox printer is once again a convenient part of my home network.
