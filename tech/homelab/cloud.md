# Points to Keep in Mind
* RAID alone is not sufficient, also need to have backup on external drives
* 3-2-1 Rule: (1 primary backup, 2 copies) of your data, on two storage mediums (ie… cloud + backup drive) with 1 offsite and inaccessible without physical access. It’s worth it for the peace of mind. 
* Must have daily or weekly backups
* Have multiple backups in multiple locations (at least 3)

# RAID
* Raid1 - must have a spare ready to go in case 1 fails
* Software RAID - can't guarantee coherency in case of sudden power failure
* Raid6 - Needs 4 drives. Allows for 2 disks to fail.
* Other options: Unraid, FreeNAS
* RAID does not protect from data corruption, just HDD failures. It is NOT A BACKUP.

# ZFS and BTRFS
## Atomic COW Snapshots
* Contains pointers to the existing data, doesn't make copies of the actual data
* Makes copies only if the current data changes
* With time, snapshot size will increase as current data changes

## Self Healing Redundant Arrays
* Writes a checksum for every block of data written
* Verifies the checksum on every read, so data corruption is caught immediately
* Can self correct on detecting corruption if a clean copy (by means of RAID) exists
* Without redundancy corruption can only be detected, not corrected

## Incremental Replication
* The snapshots are portable - can take a snapshot on 1 computer and restore it on another
* Snapshots allow for only incremental data to be copied when syncing from 1 computer to another
* Allows taking backup of your entire machine on a remote server even with a slow internet connection


# BTRFS-only Features
## File-level Cloning
* Can make copies of (large) files without using any extra space
* Extra disk space is used only when the files change
```
    me@server:~$ cp --reflink=always 200GB_virtual_machine_drive.qcow2 clone_of_200GB_virtual_machine_drive.qcow2*
```
* Allows copying specific files back from the snapshot to the filesystem without restoring the entire snapshot
    * Doesn't require extra storage, and doesn't take time
    * In ZFS this takes extra space and a long time to copy as

## Online Balncing
* Can modify the RAID level, change partition size, etc on the fly while still using the system

## NODATACOW
* For optimal performance, can mark files/directories as NODATACOW, meaning COW won't apply to them.

## File/Directory-level Compression
* Can control compression at filesystem, subvolume, directory, or file level. ZFS only allows filesystem level.
    * Can specify whether to compress, and if so, which algorithm to use.

# Tips
* Run your servers as virtual machines on top of Xenserver (xcp-ng is the open source free ver) or ESXi. Then you can take snapshots of your systems and include those in your backups.
* If you want to expose some of your network services to the actual internet, DON'T use port forwarding and dyndns solutions. Get yourself a virtual private server (vps) and expose them via reverse proxy, either with ssh -f -N -R or wireguard+routing rules 
* Can use Proxmox as hypervisor
* If using proxmox, use the native storage tools for pool creation but use a truenas vm for the NAS features
* Can use cloudflare’s zero trust tunneling to open services up, using a google account with 2 factor out front.
* Can use guacamole with MFA enabled 
* Use takeout.google.com to export all data in Google accounts (photos, drive data, email)
* Use rclone to sync Google drive data - converts from Google's proprietary format to standard formats like MS office
* gphotos-cdp tool can be used to download the original image files from Google photos (by default you don't get the original)
* Use ZFS or BTRFS (see why [here](https://arstechnica.com/information-technology/2014/01/bitrot-and-atomic-cows-inside-next-gen-filesystems/))
* Use harddrives in a ZFS pool. 
    * ZFS technology can ensure data is written in a way that is resilient to both drive failures and bitrot on both the writing and reading side.
    * Supports creating separate filesystems as easily as creating folders, with their own snapshots.
    * Each docker container running a tool (eg rclone or gphotos) writes into its own ZFS filesystem.
* Use znapzhot for creating automatic snapshots of ZFS filesystems
* Snapshot policy:
    * Hourly snapshots retained for one day. 
    * Daily snapshots retained for one month. 
    * Monthly snapshots retained for one year.
* For self hosted email:
    * Fastmail
* For backing up gmail: mbsync from isync project
* Check zfs status regularly through scripts, run data scrub
* Telegraf/Influx/Grafana for hardware monitoring
* Prefer ZFS/BTRFS over Unraid (see why [here](https://www.reddit.com/r/HomeServer/comments/sn9gcn/why_you_dont_want_unraid_and_a_few_reasons_you/))
* BTRFS can grow/shrink partitions at any point without data loss
    * But tricky to understand, lacks good GUIs
    * Raid5/6 can be risky
* Validate your backups
* Checksum your data while backing up
* Setup public key authentication via ssh for the server
    * Change default port from 22 to something higher than 1024
    * Disable root login via password
    * Set timeout to disconnect inactive users
* Setup firewall using ufw or similar
* Check `netstat` command and disable unneeded services


# Options
# Manual/Custom Setup
* Mount the drives
* Share over network using Samba - Can choose to add user authentication.
* rsync, cron for backups

# TrueNAS (OS)
* TrueNAS Core: FreeBSD based, TrueNAS Scale: Debian based
* Runs OpenZFS
* Provides HCI (Hyper-Converged Infrastructure) which allows you to create VMs and Linux containers.
* Can install on USB
* Has plugins for stuff like media server, torrent client, etc.
* Has graphical web interface
    * Can create ZFS partitions etc

# Proxmox (OS)
* Virtual environment that is a type-1 hyperviso
* Debian based
* Hypervisor, not a NAS OS, but you can virtualize a NAS OS like TrueNAS or Unraid on it
* Easily create VMs
* Free for personal use
* Built-in web client for management
* Supports ZFS, BTRFS
* Can run on PCs that don't support virtualization
* Android app to manage and monitor containers


# TrueNAS Scale vs Proxmox
* While Proxmox will allow you to install a NAS OS as a virtual machine, everything must be done inside of that NAS VM. TrueNAS Scale will allow you to pick the best file system based on your needs, create shares that can be accessed by SMB or NFS, set up granular permissions, and much more
* TrueNAS runs apps natively, need to set up docker containers, portainer for Proxmox
* TrueNAS has a more modern/intuitive GUI

# To Explore
* NextCloud
* UrBackup
* BackupPC
* Wireguard
* Xenserver
* Hypervisor
* Docker
* guacamole
* cloudflare
* NAS vs SATA
* rclone
* takeout.google.com
* gphotos-cdp
* healthchecks.io
* https://github.com/JakeWharton/docker-gphotos-sync
* https://github.com/bcardiff/docker-rclone
* znapzhot
* rsync.net
* https://github.com/linuxserver/docker-nextcloud
* https://github.com/oznu/docker-cloudflare-ddns
* https://containo.us/traefik/
* https://netdata.cloud/
* https://www.portainer.io/
* http://isync.sourceforge.net/
* https://github.com/JakeWharton/docker-mbsync/
* Telegraf/Influx/Grafana
* Kubernetes
* PiHole
* https://tteck.github.io/Proxmox/


# Resources/References
* [TrueNAS vs Proxmox - wundertech](https://www.wundertech.net/truenas-scale-vs-proxmox/)
* [Comparing TrueNAS and Proxmox- blog.key.dev](https://blog.kye.dev/proxmox-vs-truenas)
* [TrueNAS vs Proxmox Homelab- b3n.org](https://b3n.org/truenas-vs-proxmox/)
* [Home Server – Everything you want to Know!](https://lazyadmin.nl/home-network/home-server/)
* [Home server security best practices - reddit](https://www.reddit.com/r/HomeServer/comments/vwoyri/home_server_security_best_practices/)
* [Bitrot and atomic COWs: Inside “next-gen” filesystems](https://arstechnica.com/information-technology/2014/01/bitrot-and-atomic-cows-inside-next-gen-filesystems/)
* [Removing google as  a single point of failure](http://jakewharton.com/removing-google-as-a-single-point-of-failure?utm_source=pocket_shared)
* [In response to Linus Tech Tip's recent storage disaster, here's what I do at home - reddit](https://www.reddit.com/r/HomeServer/comments/sgsaff/in_response_to_linus_tech_tips_recent_storage/)
* [Why you don't want UnRaid (and a few reasons you might!) - reddit](https://www.reddit.com/r/HomeServer/comments/sn9gcn/why_you_dont_want_unraid_and_a_few_reasons_you/)
* [BTRFS Documentation](https://btrfs.readthedocs.io/en/latest/)
