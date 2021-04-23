---
id: version-1.3-concepts-rsnapshot
title: A review of concepts: Rsnapshot
sidebar_label: Rsnapshot
original_id: concepts-rsnapshot
---

As mentioned earlier, ElkarBackup is a tool based on other free tools, especially in rsnyc and rsnapshot, and since most of its logic responds to rsnapshot logic, it is worth stopping to analyze its behavior.

Rsnapshot is based on the following concepts:

* Origin of the data to copy: Where does he have to copy the data from?
* Frequency: How often does he have to copy? At certain times during the day, once a day the days that interest us, once a week, and once a month on the day we decide, ....
* Rotation: How many copies do you have to maintain at each of the frequencies mentioned above? That is, perhaps it is enough to keep the last 4 copies made during the day, but perhaps we want to keep the end-of-month copies for two years.

Next we will try to explain its operation

## Frequency
You have to define how often you have to make the copies, and these are the options:

* During the day \(Hourly\): We define the copies that the system will make at different times of the day. The system names them as Hourly.0, Hourly.1, Hourly.2 .....
* Daily \(Daily\): We will define the copies that the system will make each day of the week, specifying on which specific days and at what time it will do them. The system names them as Daily.0, Daily.1, Daily.2 .....
* Weekly: We will define the copies that the system will make once a week, specifying on which day of the week \(only one\) and at what time it will do them. The system names them as Weekly.0, Weekly.1, Weekly.2 .....
* Monthly: We will define the copies that the system will make once a month, specifying the day of the month and the time. The system names them as Monthly.0, Monthly.1, Monthly.2 .....

When I say that _**"the system will make"**_, I am not telling the whole truth. Sometimes it will make the copies and others it will rotate the copies previously made. We will see this shortly when explaining the rotation.

## Retention
With retention we are going to specify how many folders will have to save the system of each frequency. For example:

* If we put a retention of 4 on copies made during the day \(Hourly\), that means that the system will keep the last 4 copies in the folders Hourly.0, Hourly.1, Hourly.2 and Hourly.3, where Hourly.0 will be the newest and Hourly.3 the oldest.
* If we put a retention of 5 on the copies made once a day \(Daily\), that means the system will keep the last 5 copies in the Daily.0, Daily.1, Daily.2, Daily.3 and Daily.4 folders, where Daily.0 will be the newest and Daily.4 the oldest.
* The same logic applies to the retentions applied to Weekly and Monthly copies.


## Rotation
This is not complicated, but you have to understand it well. In defining frequency, we are also setting different levels of copy, specifying in some way that Weekly precedes Monthly, Daily precedes Weekly, and Hourly precedes Daily. There is no level that precedes Hourly.

![](assets/screenshots/rotation.png)

We can have different copy policies, but for the example imagine that our programming is composed as follows:

* Hourly copies at 11:00, 14:00 and 16:00, with a retention of 3
* Daily copies from Monday through Friday at 9:00 PM, with a retention of 10
* Weekly copies on Saturdays at 9pm, with a retention of 5
* Monthly copies on the 1st of each month at 9:00 PM, with a retention of 24

In this case the copies are made as follows:

* When the hour arrives to execute the _**Hourly**_ copy first of all it deletes the last one \(_**Hourly.2**_\), and next it changes the name of the rest of folders. _**Hourly.1 → Hourly.2**_ and _**Hourly.0 → Hourly.1**_. Then it makes the new copy in the _**Hourly.0**_ folder.
* When the hour arrives to execute the _**Daily**_ copy first of all it deletes the last one \(_**Daily.9**_\), and next it changes the name of the rest of folders. _**Daily.8 → Daily.9,**_ _**Daily.7 → Daily.8, ..., Daily.0 → Daily.1 **_. The difference comes now, in this case the system _**will not make a new copy to create the Daily.0**_, it will be generated by _**a rotation of the last copy of the previous level: Hourly.2 → Daily.0**_
* When the hour arrives to execute the _**Weekly**_ copy first of all it deletes the last one \(_**Weekly.4**_\), and next it changes the name of the rest of folders. _**Weekly.3 → Weekly.4,**_ _**Weekly.2 → Weekly.3, ..., Weekly.0 → Weekly.1 **_. _**Neither in this case will a new copy be made**_ to create the Weekly.0, it will be generated by a rotation of the last copy of the previous level: _**Daily.9 → Weekly.0**_
* When the hour arrives to execute the _**Monthly**_ copy first of all it deletes the last one \(_**Monthly.23**_\), and next it changes the name of the rest of folders. _**Monthly.22 → Monthly.23,**_ _**Monthly.21 → Monthly.22, ..., Monthly.0 → Monthly.1 **_. _**Neither in this case will a new copy be made**_ to create the Monthly.0, it will be generated by a rotation of the last copy of the previous level: _**Weekly.9 → Monthly.0**_

Here's what to understand: _**The rotation will only move the last copy of the previous level**_, it will never move any other folder.

That is to say:

* If the Hourly retention is 3, Daily rotation will only move the Hourly.2, never the rest.
* If the Hourly retention is 2, the Daily rotation will only move the Hourly.1, never the Hourly.0

In this logic there is a condition that we must have clear; Never touching the folders that end in .0, and for this reason _**the retention can not be 1 when in policies there are higher levels**_, the Daily will never touch the Hourly.0, and the Weekly will not touch the Daily.0 either.

It is important to understand this, since it is the logic that will be used below when we define the policies from the web interface.
