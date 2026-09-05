---
title: "From an Old Mac mini to a Linux Home Server"
date: 2026-09-03
draft: false
description: "After experimenting with macOS on my 2012 Mac mini, I finally decided to turn it into a Linux home server."
tags:
  - Mac mini
  - Ubuntu
  - Linux
  - Home Server
  - Docker
  - File Server
  - Print Server
categories:
  - Homelab
  - Linux
---

After spending some time thinking about how to reuse my old 2012 Mac mini, I finally made a decision:

**I am going to turn it into an Ubuntu Linux server.**

This was not my original plan.

When I started this project, I thought the Mac mini might still be useful as a lightweight macOS machine or perhaps even a small home server. After installing macOS Monterey and experimenting with it, however, I realized that Linux would be a much better fit for what I actually want to do.

## Why Ubuntu?

There are several reasons behind this decision.

### 1. macOS Monterey is becoming too old

The 2012 Mac mini is limited to older versions of macOS.

Although macOS Monterey still works, many modern applications are gradually dropping support for older macOS versions. This makes the machine less useful as a general-purpose desktop computer.

For a server, however, the situation is different.

I don't need a graphical desktop environment, and I don't need to run modern macOS applications.

What I really need is a stable operating system that can run services continuously.

Linux is a much better match for that requirement.

### 2. A graphical desktop is unnecessary for a server

A server doesn't need a desktop environment.

Running a full graphical interface consumes additional CPU, memory, storage and background resources. On an old Mac mini, those resources are better used by the services I actually want to run.

With Ubuntu Server, I can manage the machine primarily through SSH.

That also makes the system easier to administer remotely.

Instead of thinking of the Mac mini as an old computer that happens to be running Linux, I can start treating it like a real server.

### 3. Linux gives me much more flexibility

Another important reason is flexibility.

I already use Linux, Docker and networking technologies in my home lab, so Ubuntu fits naturally into the environment I already have.

Linux also gives me access to a huge ecosystem of server software, open-source projects and documentation.

More importantly, I can install exactly what I need instead of maintaining a desktop operating system that provides many features I don't need.

This is especially useful for a machine that is going to run continuously.

## The unexpected reason: my old printer

There is another very practical reason for choosing Linux.

I still have a **Fuji-Xerox DocuPrint CP105b** color laser printer.

The printer is old, but it still works.

The problem is that the manufacturer no longer provides current drivers for modern operating systems. The official support page now identifies the CP105b as an End of Service product.

While researching possible solutions, I found an open-source HBPLv1 printer driver project.

The CP105b uses Fuji-Xerox's Host Based Printer Language (HBPL), and the open-source Linux driver supports this printer.

This was actually one of the things that convinced me that turning the Mac mini into a Linux server made sense.

Instead of replacing a perfectly usable printer simply because its original driver is obsolete, I can use the Mac mini as a dedicated print server.

The printer can then become a network printer for the other computers on my home network.

This is a good example of why old hardware can sometimes still be useful.

The hardware itself isn't necessarily the problem.

Often, the real problem is software support.

## The new role of the Mac mini

So the purpose of this old Mac mini has changed quite a bit.

It is no longer going to be my old desktop computer.

Instead, I want to turn it into a small home server with several roles.

### File Server

The first role will be a file server.

I want the Mac mini to provide shared storage for computers on my local network.

This will give me a simple place to store files and exchange data between different machines.

I also want to keep the storage architecture simple.

The goal is not to build a NAS with enterprise-level features.

It is simply to make use of hardware that I already have.

### Docker Server

The second role will be a Docker host.

I already use Docker in my home lab, so running containers on this machine will be useful for experimenting with different services.

For example, I could use it for:

- Home lab services
- Monitoring
- Development environments
- Small web applications
- Utility services
- Testing new Docker images

This is probably where the Mac mini will become much more interesting than its original purpose.

Instead of installing every application directly onto the operating system, I can keep many services isolated inside containers.

### Print Server

The third role will be the print server.

This is the most specific requirement.

I want Ubuntu to run CUPS and the HBPLv1 driver, allowing the old Fuji-Xerox printer to be shared over the network.

The basic architecture should be straightforward:

My computers will send print jobs to the Ubuntu server.

The Ubuntu server will handle the printer queue and driver.

The physical printer will only need to be connected to the server.

This means I don't need to install the legacy printer driver on every computer in the house.

It also gives the old printer a new life as a network printer.

## What about the Mac mini hardware?

The machine is old, but it is still a relatively capable little computer for a home server.

It has several characteristics that make it interesting for this project:

- Intel x86-64 architecture
- Compact form factor
- Relatively low power consumption
- Built-in Ethernet
- Multiple USB ports
- Ability to run Linux
- Enough CPU and memory for lightweight server workloads

Of course, I don't expect it to compete with a modern server.

That's not the point.

The goal is to find a useful job for hardware that would otherwise probably sit unused.

For a file server, Docker host and print server, the performance should be more than adequate for my home environment.

## The new architecture

I am now thinking about the Mac mini as a small infrastructure node rather than a personal computer.

The plan is roughly:

**Mac mini → Ubuntu Server**

Running:

- File sharing
- Docker
- CUPS
- HBPLv1 printer driver

And potentially other lightweight services in the future.

This also fits nicely with my existing home lab.

I can manage the machine remotely, monitor it, update it and experiment with different services without needing to connect a monitor or keyboard.

## What I want to learn from this project

There is another reason I like this project.

It gives me an opportunity to practice Linux server administration in a realistic environment.

Instead of simply installing Ubuntu and running a few commands, I want to treat the machine like a real server.

That means thinking about:

- Network configuration
- Static IP addressing
- SSH access
- User and permission management
- Storage
- File sharing
- Docker
- Service management
- Logging
- Updates
- Backups
- Monitoring
- Security

These are all practical skills that are useful beyond a home lab.

And because this is my own server, I can experiment without worrying about breaking a production system.

If something goes wrong, I can troubleshoot it, rebuild it and document what I learned.

## From an obsolete Mac to a useful server

Looking back, I think the most interesting part of this project isn't actually installing Ubuntu.

It is changing the way I think about old hardware.

A 2012 Mac mini is no longer a particularly useful modern desktop computer.

But that doesn't mean the hardware is useless.

Once I remove the requirement to run modern desktop applications, the machine becomes much more interesting.

It can become a small Linux server.

It can host Docker containers.

It can provide network file sharing.

It can become a print server for an old printer that is still perfectly functional.

And, perhaps most importantly, it becomes another piece of infrastructure that I can use to learn.

That's the direction I want to take with this Mac mini project.

The next step is to install Ubuntu Server and start building the machine piece by piece.

I don't want to install everything at once.

I'll start with the operating system, networking and SSH, then gradually add file sharing, Docker and the print server.

That should make the project much easier to troubleshoot — and much more interesting to document.