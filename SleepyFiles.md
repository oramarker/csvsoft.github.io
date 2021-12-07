## Everpx

### What is Everpx?

Everpx is a light weight home media storage solution. It aims to address personal media files backup and management issues where most families are facing.'

## Why everpx?

Nowdays, people are creating pictures and videos everyday with their smart phones, cameras; and the pictures/video   becomes inreplacable memory for their lifes; they need to store these files somewhere safely and economically, one of most dominant solutions for now is to store the files to cloud storage, such as iCloud, google photos/drives, drop box etc; Cloud solution does provide the convenience of file storage, but it has following drawbacks:

* Free space is limited;
* Additonal space costs monthly fee, it is expensive for a long run; and once the payment is stopped, you are either risking losing all your years memories and get hard time to download all your medias.
* Picture and Video might not be in its raw format;
* Your asset is not under your control and your media is scanned by tons of applications for different purposes;
* 

  

So some people are seeking the home cloud solutions, home NAS, there are a lot of home NAS solutions on the market, such is Synology, Austor , home NAS let you store your files on your own NAS server and address the concerns of cloud solution, but it brings it own issues.

* Even NAS server is designed as energy efficient as possible, long running server cost electricities and fan noises are annoying in  familiy envrionments ; and most time people don't need such powerful server just for backing up pictures and videos.
* Technically it need some linux knowledge to set up and manage the NAS server; most people has no idea what Linux or RAID is for;
* Once NAS stop working for some reasons, the files and videos stored on the hard drives are not available to general people, they would need resort to IT professsionals to connect  the hard drives to new NAS servers.
* Limited harddrive slots, hard to extend.
* NAS servers themselve are expensive.

So most faimilies would need a ligtht weight file storage solution with following features;

* Easy setup plug and play, no professional knowledge is needed;
* Low power consumption;
* Easy to expand storage space;
* Server failure should not cause any hassles for people to access the files.

### Everpx  Features

Everpx are such a solution that would provide the following benefits.

* Everpx station software are designed to run on most popular operation systems. it is writtern in Java and virutally and java supported OS shold be able to run it.  Technically you can turn your home computer(windows, linux, macbook) into a everpx station with just installing the software. The recommended solution is to install single board computers, e.g. Raspberry PI , thery are silent with super low power consumption ;

* Commodity USB hard drive as the storage, which means you can easily to add new hard drives when your run out space or hard drives fails, virtualy it has no limits of attached hard drives. Also the USB drive could be attached to any other computers access the files if everpx station is out of service.

* Hot/Cold/remote USB drive provide enhanced safety and reduced power consumption ; files are automatically backup to hot/cold hard drives, where cold USB hard drive is powered on only when the hot to cold backup is running.*

* Free mobile application is provided to automatically synchronize mobile pictures and videos to everpx stations, once pictures/videos get backuped to everpx station, users can free up precious phone storage to take more pictures and videos.

* All NAS features , such as SMB, NFS file sharing etc. for advanced linux users.

* Commodity hardwares are cheap and easy to get : Raspberry PI SBC or any other mini PCs, USB hard drives.


### Everpx Mobile feature

* One buttton to inteligently  backup medias to everpx station;

* Comphrehensive media management functions:

  1. Media could be organized with albums and tags;
  2. Media title, description could be edited so that it could be searchable;
  3. Albums could be shared among family members;
  4. User could define custom filters  to speed up search.

  

 ``` 
Everpx solution need at least two USB hard drives, one is called HOT drive, it is always on line, mobile apps would backup the pictures and videos to this drive; Other drives we call cold drives, they are used to backup files on the hot drive and only online and power on (if powered with smart power plugs, e.g. tplinks) when backup is running.
 ```



## Feature Matrix

| Everpx Version                        | Community(Free) | Standard  | Premium   | Notes |
| ------------------------------------- | --------------- | --------- | --------- | ----- |
| Automatic Backup                      | *               | *         | *         |       |
| Storage Disks                         | Up to 2         | Unlimited | Unlimited |       |
| Smart Power Plug Support              |                 | Yes       | Yes       |       |
| Remote Backup                         |                 |           | Yes       |       |
| Automatic Upgrade                     | Manual          | Yes       | Yes       |       |
| Automatic SSL Certificate             |                 |           | Yes       |       |
| Technical support                     |                 | Email     | VPN       |       |
| Free Mobile App                       | Yes             | Yes       | Yes       |       |
| Import external Medias                |                 | Yes       | Yes       |       |
| Dynamic DNS Name(subdomain of everpx) |                 | Yes       | Yes       |       |



## Installation

## Configuration



### Architecture 

Each Sleep station has an unique ID and is stored in the image.

The sleep station could choose to register to sleep file server to get support;



### Everpx Server

1. Accept customer registration, once customer registered, everpx station will ping the everpx server periodically as heart beat.
2. Provide upgrade metadata:
3. Provide upgrade instructions and supporting artifiacts.



### Mobile App

Main Page

1. If no sleep statiion is configured, the first page would need a button to find the sleep station server, once is configured, the configuration should be stored  and the sleep station server could be changed later on.
2. If  sleep station is configured, if no login is configured, login in page is shown up, since it is for family all log in user should be listed to let user choose, once user login, user's credentials should be persisted so that user won't need to log in again;
3. Main page would need to show the backup history and files to be backed up.

Settings Page

1. Set up sleeping station server;

2. Free space settings: 

   2.1 When system space is below certain threshold or fixed space, then cleanup the oldest already backup pictures and videos;

   2.2 Clean up pictures videos onced backedup.

   

   

   3. Share settings; User could choose to share files with other family members to view.

   

   File Browser Page

   User should be able to browse/download files on sleep stations

   

   Upload Page

   Users should be able to manually trigger the uploading process.

   

Admin Functions

If user logs in as admin:

The main page should list all USB drives: name, harddrive type, size, file system type, free space etc.

Admin should be able to choose one or more hard drives as HOT drive, which is working storage and always be online;

Admin should be able set other hard drives as cold drives as backup drives; optionally if the code hard drive is powered through a tp-link power plug, admin should able to specify the plug name so that its power can be automatically managed (This is an advanced feature)

Admin also should be able to setup the hot to cold backup time schedule;



Once the cold and hot harddrive is set, auto mount and unmount , rsync task should be setup on the server side.



User Managment

Admin should be able manage family users, each  user will have a photo and video backup directory on HOT drive.

Add user: Setup user name, password, backup directory

Disable User:

Change user password

Remove user.



Alert Management

Admin should be able to enable the alert when the hard drives fail or free space is below predefined threshold.



System Upgrade

When compatible new version is available, admin should be able to upgrade the sleep station.



## Remote Backup

Media file should have the capability to backup to remote site.



### Backup Server 

* Receiving files

Backup server should track the media that have been sent from remote sites, the medias from remote site should be associated with their own tenant ID, the media meta can be stored in the same Media table;

Media files should be stored to disks that are owned by the sync user, ideally it should be stored to the disk with same disk number as that they are stored locally; otherwise, it should be stored under a directory with the disk number information.



Media files could be uploaded by chunks, it should be assembled  all parts into its original format and store the media metadata.



Media need to add two additional columns:

tenant_id  integer;  -- the tenant id of the sync user

src_media_id long;  -- the media id from client side



* Serving files

Backup server should provide the following APIs:

1. API to  let client side to retrieve files by  original media IDs.
2. API to let client to retrieve the number of copies of the file. 



### Backup Client

Backup client should be able upload media files to remote sites based on the remote storage configurations with the sync user account;

Media files should be able to broke into chunks to upload to remote sites;; each chunk should be have checksums;

Media metadata should be sent along with the last chunk of file upload.

Client side should be able retieve the number of copies of remote backup and the media files.







1. 

2. 

3.  

   

### Everpx Web Site

The website should include following functions:

1. Home page to highlight everpx features and selling points;
2. Document page to detail everpx user guide;
3. FAQ
4. Customer selfservice page:
   * Registration
   * Profile
   * Everpx disks spaces usages and monitoring
   * Subscription

   

### Customer Management Module

Any user who registered its everpx station is treated a customer, the customer is categorized by its subscriptions package: standard, premium.



Customer is defined as person who is using everpx station

Everpx station will be default to community version, customer can choose to register the everpx station through the everpx  station admin or not , registered customer will have chance to get notice of software updates.

Customer can subscribe the service packages  

#### Customer Registration

	1.	Customers should be able to register with their email  as customer ID and set password; customers should also register with google , apple, facebook, twitter ID ; if the email address already registered, sign in form should be displayed to let user log in
 	2.	Once customer registered, an email should be send over to the user's email address with details of the registration.
 	3.	Forget password function: User should be able get a reset password if use forget password.

User Registration Models:

The user registration model, each user has following attributes:

* customerID (system generated unique systemID)
* customerEmail (Required and encyrpted when stored)
* password( pasword multiple-round hash) 
* customerName (optional encrypted)
* phone( optional encyrpted)
* registrationType: primitive| google | apple | facebook

When user registers, the everpx station basic meta should be stored, later when customer everpx station software or hardware get upgraded, a new entry should be created to reflect history

* everpxStationId
* everpxStationVer
* osType
* osVer
* CPU
* Mem
* active
* customerId
* createTime
* 

### Customer Subscription

Customer should be able subscribe service packages: standard and premium on annual basis,

on backend, subscription model should be looking like :

* SubscriptionId (systemgenerated)
* customerId
* packageType
* startDate
* endDate
* createTime
* active
* promotionPkg









