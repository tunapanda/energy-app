**Table of Contents**  *generated with [DocToc](http://doctoc.herokuapp.com/)*

- [Introduction](#introduction)
- [Dictionary](#dictionary)
- [Domain Model](#domain-model)
- [Functional requirements](#functional-requirements)
	- [Actors](#actors)
	- [Business processes](#business-processes)
		- [B1. Working with organizational structure for given month](#b1-working-with-organizational-structure-for-given-month)
			- [UC.B1.1 Import  Organization Structure from Podio](#ucb11-import--organization-structure-from-podio)
			- [UC.B1.2 Preconfigure import process](#ucb12-preconfigure-import-process)
			- [UC.B1.3 Configure Organization Structure](#ucb13-configure-organization-structure)
			- [UC.B1.4 Set "Total amount to be paid"](#ucb14-set-total-amount-to-be-paid)
			- [UC.B1.5 Archive results](#ucb15-archive-results)
		- [B1.1 Assigning Energy Points](#b11-assigning-energy-points)
			- [UC.B1.1.1 Assign EP for Roles](#ucb111-assign-ep-for-roles)
			- [UC.B1.1.2.2 Assign EP for Circles](#ucb1122-assign-ep-for-circles)
	- [Use cases](#use-cases)
		- [Standard User](#standard-user)
			- [UC.1. View Dashboard](#uc1-view-dashboard)
			- [UC.2. Browse Circles](#uc2-browse-circles)
			- [UC.3. View reports](#uc3-view-reports)
			- [UC.4. Login](#uc4-login)
		- [Lead Link](#lead-link)
		- [Administrator](#administrator)
		- [Technical Administrator](#technical-administrator)
			- [UC.5. Manage Individuals](#uc5-manage-individuals)
		- [Member of "Secretary" Role](#member-of-secretary-role)
			- [UC.6. Define "Thought Leaders"](#uc6-define-thought-leaders)
- [Non-functional requirements](#non-functional-requirements)
- [Architecture](#architecture)
	- [Logical](#logical)
	- [Physical](#physical)
- [Interface specification (Podio)](#interface-specification-podio)
- [Reports](#reports)
	- [General requirements](#)
	- [UC.3.1 Point distribution of all circles](#)
	- [UC.3.2 Overall breakdown of circles’ Energy Points](#)
	- [UC.3.3 Overall breakdown of roles’ Energy Points](#)
	- [UC.3.4 Overall breakdown of basis points and cash assignment in EPA](#)
	- [UC.3.5 Get a table of roles with energy points over X months for a given circle](#)
	- [UC.3.6  Timeline of energy points for individuals, roles, or circles over a given time period](#)
	- [UC.3.7  Chart for each Individual, showing what Circles/Roles give them the most BPs](#)
	- [UC.3.8  Chart for each Circle, showing what Individuals give the circle the most BPs](#)

# Introduction

The purpose of this document is to provide technical specification of **Energy Points** **Application.** The document sets the boundaries to the scope of application and describes  all  functional and non-functional (technical) requirements. 

The content is intended to be entry point for any developer to start designing the technical aspects of application (database model, screens, screen flows, technology stack) and writing the code. But it is assumed that, during their work, developers will always cooperate closely with business users (people who will use the application) and agree with them any relevant details which are not covered by the document (user interface stuff like how exactly the screens should look like, colors, messages,  labels, validations for particular fields, etc.).  

 

**Important:**

The application described in this specification takes its origins from holacracy concept so it’s essential for anyone who reads this document to be familiar with it in first place. Following link is a good place to start:

[https://hbr.org/2016/07/beyond-the-holacracy-hype](https://hbr.org/2016/07/beyond-the-holacracy-hype)

#  

# Dictionary

<table>
  <tr>
    <td>Term</td>
    <td>Definition</td>
  </tr>
  <tr>
    <td>Energy Points Application (EPA)</td>
    <td>Application which specification is described in this document.</td>
  </tr>
  <tr>
    <td>Circle</td>
    <td>In a holacracy, a group of Roles working toward the same purpose; in essence, a team that forms or disbands as the organization’s needs change.</td>
  </tr>
  <tr>
    <td>General Company Circle</td>
    <td>The only Circle not nested within another. It contains any other Circle and its sub-Circles.</td>
  </tr>
  <tr>
    <td>Role</td>
    <td>In a holacracy Circle, a set of responsibilities for a certain outcome or process. Roles can be created, revised, or destroyed; Individuals usually have more than one, in multiple Circles.</td>
  </tr>
  <tr>
    <td>Individual</td>
    <td>Member of the organization that can belong to one or more circles.</td>
  </tr>
  <tr>
    <td>Energy Point</td>
    <td>During Tactical Meetings Energy Points are assigned to Roles and Circles.
The more points the more role is energized by its contributor (the more effort he/she put in it). </td>
  </tr>
  <tr>
    <td>Tactical Meeting</td>
    <td>Meeting held periodically, once a month, which gathers all Individuals that belong to the given Circle. During the meeting they discuss the work done and assign Energy Points to the Roles.</td>
  </tr>
  <tr>
    <td>KPI</td>
    <td>Key Performance Indicators are assigned to Roles and Circles. Based on the KPIs, the work of contributors to the Roles is assessed and exchanged to Energy Points.</td>
  </tr>
  <tr>
    <td>Bonus</td>
    <td>(optional) Financial compensation allocated to an individual based on his/her energy points relative to total energy points for a given time period.</td>
  </tr>
  <tr>
    <td>Podio</td>
    <td>Already existing application where the Organization Structures are created. Then it is imported into EPA. </td>
  </tr>
  <tr>
    <td>Organization Structure (OS)</td>
    <td>Organization Structure consists of Circles, sub-Circles and Roles. It changes once a month.</td>
  </tr>
</table>


# Domain Model

![image alt text](image_0.png)

1. Circle can have other Circles inside which are called sub-Circles.

2. Circle can have Roles inside.

3. Circle can’t have Roles and sub-Circles at the same time. If this is a case (Roles + sub-Circles) we handle it like for example in case of "Learning Space" Circle (refer to “July Circles” sheet in [Structure and Strategy EP](https://docs.google.com/spreadsheets/d/1deRXZKuSXjsCWmPHwAoTII2KKwF30wYB-eB2AqIKass/edit#gid=1810273927)): Within “Learning Space” Circle we have 3 sub-Circles, including one called “Learning Space” again - this is the special sub-Circle dedicated solely for Roles of “Learning Space” Circle.

4. Individuals can belong to:

    1. One or more Roles within Circle

    2. Circle if he belongs to any Role within the Circle 

    3. Circle if he belongs to any Role within any of its sub-Circles at any level downwards the hierarchy. 

5. Tactical Meetings are held for each Circle separately, starting from the lowest-level sub-Circles and going upwards. 

# Functional requirements

## Actors

Below please find the list of types of users that will interact with the system. It includes also external systems if there are any.

<table>
  <tr>
    <td>Actor</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>Standard User</td>
    <td>Default role for any Individual in the organization. Can assign energy points for roles in the circles that he/she is a member of. </td>
  </tr>
  <tr>
    <td>Lead Link</td>
    <td>A Lead link can make changes to the circle(s) of which he/she is lead link, but not any others.
This will be the user who holds Lead Link Role in given Circle.</td>
  </tr>
  <tr>
    <td>Member of  X Role</td>
    <td>Depending on a Role in a Circle that Individual holds, he can have certain privileges.</td>
  </tr>
  <tr>
    <td>Administrator</td>
    <td>Manages the whole Organization Structure.
This will be the user who holds one defined Role (Lead Link) in "Structure & Strategy" or the “GCC” Circle.</td>
  </tr>
  <tr>
    <td>Technical Administrator</td>
    <td>This is built-in role in EAP, it  has nothing to do with Organization Structure. It’s always present and assigned  to at least 2 people.</td>
  </tr>
</table>


## Business processes

### B1. Working with organizational structure for given month

This process is run every month. It populates Energy Points application with current Organization Structure for previous month. Then users of the application populates it with relevant data (Energy Points).

![image alt text](image_1.png)

#### UC.B1.1 Import  Organization Structure from Podio

**Prerequisites:** Files with OS structure from Podio

**Main scenario:**

1. Technical Administrator uploads files with OS structure.

2. Automatic validation is performed:

    1. whether structure is not broken (no disconnected Circles)

    2. whether all Circles have Lead Links

3. The uploaded OS is displayed on application screen.

4. The uploaded OS has status "New".

**Alternative scenario:**

1. Technical Administrator uploads files with OS structure.

2. Automatic validation is performed

3. Validation fails.

4. Log message with details (why failed?) is displayed to Administrator. 

5. OS is not loaded.

Example of loaded OS:

![image alt text](image_2.png)

#### UC.B1.2 Preconfigure import process

**Prerequisites:** OS with status "New".

**Main scenario:**

1. Administrator displays OS.

2. Administrator validates OS (manually checks if import succeeded).

3. He defines "Threshold" and “Total Relative Points” values for Roles and Circles.

4. He defines whether the "Thought Leader" feature is active.

    1. If active, he defines whether during assigning of energy points (B1.1) users can see all others’ points, or just see selected "Thought Leaders".

5. He can mark some Roles as not energised.

6. After making any needed changes he marks OS as "Validated".

7. Constraint: There can’t be more than 1 OS with status "Validated" at any time.

**Alternative scenario:**

1. Administrator displays OS structure.

2. He comes to conclusion that OS structure is invalid.

3. Administrator delete OS structure so it can be revised and imported once again from Podio.

Example of OS:

![image alt text](image_3.png)

#### UC.B1.3 Configure Organization Structure

**Prerequisites:** OS with status "Validated".

**Main scenario:**

1. All Individuals can browse all Circles they belong to.

2. Lead Link of the Circle can mark some Roles as not energised.

3. Any Individual can change assignments of weights to Role in a Circle, that is:

    1. By default there is an assumption that everyone in a given Role contributes to it equally (so weight for each member is the same and equals: 1 divided by number of members for the Role).

    2. Any Individual can change the mentioned default assignment, for example he can decide that contribution of one member is 80% and the second one is 20%.  The final decision about assignment belongs to Lead Link.

    3. Weights for the Role must sum up to 100%.

4. After making any needed changes Lead link marks Circle as "Active".

#### UC.B1.4 Set "Total amount to be paid" 

**Prerequisites:** All Circles and Roles have assigned EPs. 

**Main scenario:**

1. GCC Lead Link sets global value: "Total amount to be paid". This is used in report module.

#### UC.B1.5 Archive results

**Prerequisites:** All Circles and Roles have assigned EPs and "Total amount to be paid" value is set.

**Main scenario:**

1. Administrator changes status of OS to "Finished".

2. Data from OS is now available in reporting module. 

### B1.1 Assigning Energy Points

![image alt text](image_4.png)

#### UC.B1.1.1 Assign EP for Roles

**Prerequisites:** Circle with status "Active" and contains Role(s).

**Main scenario:**

Remark: The whole scenario usually takes place after the meeting of the whole team is held (all members of the Circle). The purpose of the meeting is to discuss how the Roles were energised. 

1. Standard User browses OS.

2. He assigns Energy Points to all Roles in the Circle. 

3. Energy Points he assigns for Roles in given Circle have to add up to "Threshold" value (suggestion: we should have toggles here with sliders that people can adjust as well as numbers that can be changed manually or toggled up/down).

4. He can see allocation of EP made by other users (but can’t change it).

#### UC.B1.1.2.2 Assign EP for Circles 

**Prerequisites:** All Energy Points for Roles are assigned. 

**Main scenario:**

Remark 1: The whole scenario usually takes place after the meeting of the whole team is held (all members of the Circle). The purpose of the meeting is to share the metrics and KPI’s of the Circle to know how much effort was put or the value created by the Circle.

Remark 2: Assigning EP is started from the bottom of the hierarchy (leaf) and goes up to (GCC). 

1. All Individuals assign EP to Circles they belong to. Important: assigning takes place only on Circle level, not for Roles within Circles.

2. Energy Points assigned to given Circle have to add up to "Total relative points" value.

3. All Individuals can see allocation of EP made by other users (but can’t change it).

## Use cases

### Standard User

![image alt text](image_5.png)

#### UC.1. View Dashboard

**Description:** This is the main screen that is visible to Standard User just after entering the application. It consists of: 

1. Short statistics about number of Circles and Roles Standard user belongs to (in current "Validated" OS).

2. List of all Circles Standard user belongs to. For details please see UC.2. 

3. Link to screen with reports

**Main scenario:**

1. User enters Dashboard screen.

2. User starts UC.2.

**Alternative scenario:**

1. User enters Dashboard screen.

2. User starts UC.3.

#### UC.2. Browse Circles

**Description:** Standard user can browse the whole OS which is in "Validated" status. All information for OS, if not stated otherwise elsewhere, is visible to Standard user (that is for example: who is member of what Role).

Standard User uses the list also for assigning Energy Points. For details see B1.1.

**Main scenario:**

1. User enters Dashboard screen.

2. User sees the OS with all information.

#### UC.3. View reports

**Description:** Standard User has access to several types of reports. All of them are described in detail in another section of the document.

#### UC.4. Login

**Description:** All Individuals including Standard Users before using EPA must log in to it, using credentials (login + password) supplied by Administrator. After first login, or after login with password changed by Administrator for any reason, password must be changed by Standard User.

**Main scenario:**

1. User enters Login screen.

2. User enters credentials

3. User is successfully logged into application.

4. User is redirected to UC.1.

**Alternative scenario:**

1. User enters Login screen.

2. User enters wrong credentials

3. User gets information that login attempt failed.

4. User can try to log in once again.

5. If user fails to log in three times in a row - he gets form which he can fill in and ask administrator for new password.

### Lead Link

Lead Link can perform at least the same actions as Standard user + any other that is specifically attributed to him in the processes’ description (like UC.B1.3). 

### Administrator

Administrator can perform at least the same actions as Lead Link + Member of X Role + any other that is specifically attributed to him in the "Business processes" section. 

### Technical Administrator

#### ![image alt text](image_6.png)

#### UC.5. Manage Individuals

**Description:** This functionality relates to adding/editing/blocking Individuals that can use EPA.

**Main scenario:**

1. Technical Administrator enters screen for managing Individuals.

2. He can add new Individual.  To that end he:

    1. Provides unique "login"

    2. Provides password. Password needs to be changed by Individual during first login.

    3. Provides "Podio name" for Individual (like: “Jay Larson”). Based on that matching is done when files  from Podio are imported. Matching is needed to match Roles/Circles (from Podio) to Individual (from EPA).

**Alternative scenario:**

1. Technical Administrator enters screen for managing Individuals.

2. He can see all Individuals that can or could use EPA.

3. He can select one/many Individuals and block/unblock them (blocked Individual can’t login to application).

**Alternative scenario:**

1. Technical Administrator enters screen for managing Individuals.

2. He can see all Individuals that can or could use EPA.

3. He can click Individual to see detailed information for that Individual.

4. From there he can:

    1. Change password for Individual. New password needs to be changed by Individual during first login.

### Member of "Secretary" Role

#### ![image alt text](image_7.png)

#### UC.6. Define "Thought Leaders"

**Prerequisite:** Feature of defining "Thought Leaders" must be switched on for OS by Administrator.

**Main scenario:**

1. On "UC.2 Browse Circles" screen user can check/uncheck “Is Thought Leader” flag for each member of the Circle .

2. Lead Link is automatically checked as "Thought Leader".

# Non-functional requirements

<table>
  <tr>
    <td>no</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>1</td>
    <td>All actions in EPA should be accessible as links/buttons (for example: to assign EP for Circle we need to click at Circle link and it points us to proper form). There should be no need to enter any commands (like in terminal windows).</td>
  </tr>
  <tr>
    <td>2</td>
    <td>There shouldn’t be the need of too much navigation within EPA.  Ideally the whole process of assigning EP should take place on one screen. Using application should be as simple as possible.</td>
  </tr>
  <tr>
    <td>3</td>
    <td>Technology stack:
Web Client (browser): Google Chrome (min. 53.0), Safari (min. 9),  Firefox (min. 47), IE (min. 9)
Web Client (technology): HTML 5, CSS 3, Angular 2
Backend (Technology): PHP, Apache, Ubuntu
Database: MySQL, Ubuntu</td>
  </tr>
  <tr>
    <td>4</td>
    <td> Before entering EPA any Individual must log in to it. Administrator creates users/passwords, but roles for current month are based  on PODIO.</td>
  </tr>
  <tr>
    <td>5</td>
    <td>There must be the possibility to run the app in more than one location at the same time. Each location uses its own instance of the app and there is no guarantee that there is a working network connection between them. There should be the way to synchronize these instances manually (triggered by Administrator) and automatically (every X hours). 
Below you can find example of how it was already implemented for other application used in Tunapanda:
https://github.com/tunapanda/wp-remote-sync </td>
  </tr>
</table>




# Architecture 

Web application will be created using Single Page Application architecture pattern.

## Logical

![image alt text](image_8.png)

## Physical

![image alt text](image_9.png)

# Interface specification (Podio)

Organization Structure is loaded from two files generated from Podio (details described as part of B1 business process). The structure of the files is the following:

[JulyCircles.xlsx](http://drive.google.com/open?id=0B0i5YH0WDgTybTVWWjJDXzZsVHVVWmZLZVNscW9JcFM0N2VV)

[JulyRoles.xlsx](http://drive.google.com/open?id=0B0i5YH0WDgTyVDVnU0NFWm9LYU9xVHZEWDZjMWtXT1pRVGhV)

The above files are what the EPA should expect as input. However, they will be provided as CSV files not in XLSX binary format.

Currently, files generated  from Podio, are transformed into temporary format which contains only needed information from EPA perspective:

[JulyCircles.csv](http://drive.google.com/open?id=1dpGG3Yip95le2-5Oh5_23KE8UaGcjPaqimj-hOn-k-E)

[JulyRoles.csv](http://drive.google.com/open?id=1GwHKuih-shafPYIy5jphidGsX466sQQXInHyGUzKFpY)

And then loaded using scripts to WorkBook. Final version of the workbook can be check out here (July Circles and July Roles sheets are relevant):

[Structure & Strategy EP](https://docs.google.com/spreadsheets/d/1deRXZKuSXjsCWmPHwAoTII2KKwF30wYB-eB2AqIKass/edit#gid=1405289019)

The whole process (loading XLSX files,  filtering out only needed information and loading into EPA) should be automatic.

# Reports 

## General requirements

* Reports are generated for every OS in "Finished" state.

* All reports accessible from "Reports" screen (UC.3) as links to specific reports.

## UC.3.1 Point distribution of all circles 

**Main scenario:**

1. Report is generated for GCC and its sub-circles.

2. User can click on any sub-circle and the same report is generated for that sub-circle together with all sub-circles of sub-circle.

3. Reports are shown in the form of the pie chart.

[Point distribution of all circles](https://docs.google.com/spreadsheets/d/1G_uGz-XHImc_KuyeequOF14PtO7HISC4kCovLqJ5Ed4/edit#gid=0)

## UC.3.2 Overall breakdown of circles’ Energy Points

**Main scenario:**

1. User can select appropriate OS and click "Generate report" button. 

2. Report for given OS and all its Circles is generated.

Example of generated report for one Circle (this was taken from September Circles, [Structure & Strategy EP](https://docs.google.com/spreadsheets/d/1deRXZKuSXjsCWmPHwAoTII2KKwF30wYB-eB2AqIKass/edit#gid=1405289019)) :

[Overall breakdown of circles’  Energy Points](https://docs.google.com/spreadsheets/d/1P2kL-aahddEQ0CV6xLZiDAj651h7dYZrZupg_gz8zvw/edit#gid=0)

## UC.3.3 Overall breakdown of roles’ Energy Points

**Prerequisites:** UC.3.2 calculations should be made first. 

**Main scenario:**

1. User clicks link with Circle which contains Roles.

2. Report for given Circle and all its Roles is generated.

Example of generated report for one Circle is provided below (it was taken from September Roles, Employment Circe, [Structure & Strategy EP](https://docs.google.com/spreadsheets/d/1deRXZKuSXjsCWmPHwAoTII2KKwF30wYB-eB2AqIKass/edit#gid=1405289019)). Employment Circle got 6412 points to distribute. Process of distribution of points between Circles is described in UC.3.2.

[Overall breakdown of roles’ in Energy Points](https://docs.google.com/spreadsheets/d/1b2_tmj215yJuu62_DVZ9N1yR5pVAJeSjNPXTV7sX4FY/edit#gid=0)

## UC.3.4 Overall breakdown of basis points and cash assignment in EPA

**Prerequisites:** UC.3.3 calculations should be made first. 

**Main scenario:**

1. User can select appropriate OS and click "Generate report" button. 

2. Report for given OS and all its Circles is generated.

**Algorithm for generating the report:**

1. For every Individual in OS (like: Jacky Kimani) go down to the level of Role he/she is assigned to. In report UC.3.3 it’s Employment Circle, "Lead Link" Role (there are more Roles Jacky is assigned to but we go one by one).

2. Take value of "%" column for the Role (11,43%) and multiply it with weight value assigned to Jacky Kimani for that Role (weight can be for example: 80%).

3. The outcome from point above multiply by  "%" assigned to Employment Circle (28,89%). 

4. Go up the hierarchy and multiply the value with "%" assigned to Circle that contains Employment Circle (Positioning Strategic Grouping Circle in our case - 22,20%). Finish when GCC is reached.

5. Repeat 1-4 steps for every Role for given Individual and sum up all values from step 4.

6. The outcome in step 5 is "Proportion" value for one Individual. Now it should be repeated for everyone.

7. Multiply "Proportion" value with money that is to be distributed between members of OS for given month (19,778 in our example). In such a way you get “Bonus” value.

Example of  calculations for September OS (weight for this calculations is assumed to be equally distributed between Circle members, that is weight for each member = 1/number of members):

<table>
  <tr>
    <td>Name</td>
    <td>Proportion</td>
    <td>BPs</td>
    <td>Bonus</td>
  </tr>
  <tr>
    <td>John Gitonga</td>
    <td>0.1075 </td>
    <td>1075</td>
    <td>2,126</td>
  </tr>
  <tr>
    <td>Susan Nempiris</td>
    <td>0.0114</td>
    <td>114</td>
    <td>225</td>
  </tr>
  <tr>
    <td>Dennis Lighare</td>
    <td>0.0198</td>
    <td>198</td>
    <td>391</td>
  </tr>
  <tr>
    <td>Patrick Gichini Waruingi</td>
    <td>0.0245</td>
    <td>=Proportion * 10000</td>
    <td>484</td>
  </tr>
  <tr>
    <td>Fredrick Odhiambo</td>
    <td>0.0342</td>
    <td>=Proportion * 10000</td>
    <td>676</td>
  </tr>
  <tr>
    <td>Mwalugha Bura</td>
    <td>0.0377</td>
    <td>=Proportion * 10000</td>
    <td>746</td>
  </tr>
  <tr>
    <td>Kelvin Mungai</td>
    <td>0.0179</td>
    <td>=Proportion * 10000</td>
    <td>353</td>
  </tr>
  <tr>
    <td>Jacky Kimani</td>
    <td>0.0335</td>
    <td>=Proportion * 10000</td>
    <td>663</td>
  </tr>
  <tr>
    <td>Cetric Oyavo</td>
    <td>0.0049</td>
    <td>=Proportion * 10000</td>
    <td>96.2</td>
  </tr>
  <tr>
    <td>Luka Sopia</td>
    <td>0.0372</td>
    <td>=Proportion * 10000</td>
    <td>736</td>
  </tr>
  <tr>
    <td>Onesmus Muturi</td>
    <td>0.0499</td>
    <td>=Proportion * 10000</td>
    <td>987</td>
  </tr>
  <tr>
    <td>Zahra Ismail</td>
    <td>0.0303</td>
    <td>=Proportion * 10000</td>
    <td>600</td>
  </tr>
  <tr>
    <td>Josephine Miliza</td>
    <td>0.0488</td>
    <td>=Proportion * 10000</td>
    <td>964</td>
  </tr>
  <tr>
    <td>Renice Owino</td>
    <td>0.0248</td>
    <td>=Proportion * 10000</td>
    <td>490</td>
  </tr>
  <tr>
    <td>Minzilet Ijai</td>
    <td>0.0215</td>
    <td>=Proportion * 10000</td>
    <td>426</td>
  </tr>
  <tr>
    <td>Jay Larson</td>
    <td>0.0609</td>
    <td>=Proportion * 10000</td>
    <td>1,205</td>
  </tr>
  <tr>
    <td>Mick Larson</td>
    <td>0.0484</td>
    <td>=Proportion * 10000</td>
    <td>957</td>
  </tr>
  <tr>
    <td>James Otieno</td>
    <td>0.0354</td>
    <td>=Proportion * 10000</td>
    <td>699</td>
  </tr>
  <tr>
    <td>Elias Kinyua</td>
    <td>0.0345</td>
    <td>=Proportion * 10000</td>
    <td>682</td>
  </tr>
  <tr>
    <td>Nixon Kanali</td>
    <td>0.0551</td>
    <td>=Proportion * 10000</td>
    <td>1,089</td>
  </tr>
  <tr>
    <td>Elizabeth Ochieng</td>
    <td>0.0364</td>
    <td>=Proportion * 10000</td>
    <td>719</td>
  </tr>
  <tr>
    <td>Dickson Morande</td>
    <td>0.0144</td>
    <td>=Proportion * 10000</td>
    <td>284</td>
  </tr>
  <tr>
    <td>Maureen Moraa</td>
    <td>0.0147</td>
    <td>=Proportion * 10000</td>
    <td>291</td>
  </tr>
  <tr>
    <td>Jackyletty</td>
    <td>0.0337</td>
    <td>=Proportion * 10000</td>
    <td>667</td>
  </tr>
  <tr>
    <td>Michael Okiri</td>
    <td>0.0042</td>
    <td>=Proportion * 10000</td>
    <td>83.8</td>
  </tr>
  <tr>
    <td>Anne Nyokabi</td>
    <td>0.0681</td>
    <td>=Proportion * 10000</td>
    <td>1,348</td>
  </tr>
  <tr>
    <td>Wendy Mwangi</td>
    <td>0.0106</td>
    <td>=Proportion * 10000</td>
    <td>209</td>
  </tr>
  <tr>
    <td>Mohammed Dena</td>
    <td>0.0295</td>
    <td>=Proportion * 10000</td>
    <td>583</td>
  </tr>
  <tr>
    <td>cecilia wangui mukima</td>
    <td>0.0261</td>
    <td>=Proportion * 10000</td>
    <td>516</td>
  </tr>
  <tr>
    <td>Wambua Mutunga</td>
    <td>0.0228</td>
    <td>=Proportion * 10000</td>
    <td>450</td>
  </tr>
  <tr>
    <td>Mikael Lindqvist</td>
    <td>0.0000</td>
    <td>=Proportion * 10000</td>
    <td>0</td>
  </tr>
  <tr>
    <td>Mia Tengco</td>
    <td>0.0015</td>
    <td>=Proportion * 10000</td>
    <td>29.8</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Total Amount to be paid</td>
    <td>1.0000</td>
    <td></td>
    <td>19,778
</td>
  </tr>
</table>


**Remarks:**

1. Only "BPs’ and “Bonus" columns should be visible for users.

2. "Proportion" column is included for the sake of clarity but should not be shown to users.

3. "Total Amount to be paid" is manually entered in the EPA.

## UC.3.5 Get a table of roles with energy points over X months for a given circle 

**Main scenario:**

1. User selects Circle for which report is generated.

2. User enters the number of months for which report is generated.

3. Report is generated for a given period and Circle. It shows a table with the following columns: Role, % of the total (energy points for the Role for given period / energy points for all Roles for given period within Circle), percent change of EP (positive or negative) between first and last month in the period for which points are calculated. All columns are sortable. Energy points for Roles are taken from "Total" column for each Role for given Circle (for example: [Overall breakdown of roles’ in Energy Points](https://docs.google.com/spreadsheets/d/1b2_tmj215yJuu62_DVZ9N1yR5pVAJeSjNPXTV7sX4FY/edit#gid=0), Secretary = 27)

## UC.3.6  Timeline of energy points for individuals, roles, or circles over a given time period  

**Main scenario:**

1. User can enter type of report (Individuals/Roles/Circles) and period (3 months/6 months/ 1 year/ other) 

2. Report is generated in the form of timeline. 

3. The following values are taken into consideration when the report is generated:

    1. Circles - "Total" column for given Circle

    2. Role - "Total" column for given Role

    3. Individual - For each Role Individual belongs to, "Total" column for that Role divided by number of people within the Role, then sum up all values.

## UC.3.7  Chart for each Individual, showing what Circles/Roles give them the most BPs

**Main scenario:**

1. User selects Individual for whom the report is generated.

2. User selects whether BPs should be aggregated by Circles or Roles.

3. By default BPs are calculated according to algorithm from UC.3.4 (we start at Role level and then go up the hierarchy until we reach GCC). But user can additionally select whether BPs calculation stops at any level of the OS hierarchy  (at any intermediate Circle). 

4. User selects OS.

5. Report is generated in the form of sortable table with the following columns: Role/Circle, Total BPs earned. 

## UC.3.8  Chart for each Circle, showing what Individuals give the circle the most BPs

**Main scenario:**

1. User selects Circle for whom the report is generated.

2. User selects OS.

3. Report is generated in the form of sortable table with the following columns: Individual, Total BPs provided. 

