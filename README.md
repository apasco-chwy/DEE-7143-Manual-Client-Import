# Spike DEE-7143: Manual Client Import

A command line program for manually importing non-PIMS-integrated clinics to PH.

<br>

## I. Background  

- **Problem:** There are currently over 230 production clinics using PH. Clinics that do not have a PIMS connection are less likely to use PH to write prescriptions for Chewy customers because **it is time consuming to manually enter existing client names, addresses, and pets.**
- **Primary Goal:** Offer new non-PIMS-integrated clinics the option to manually import all existing client data to PH at onboarding to support an easy starting point. 
- **Potential Scale:** Offer existing non-PIMS-integrated clinics the option to manually combine client data from their PIMS with existing client data in PH to create a single point of storage of client data in PH, improving quality of life and increasing their chances of using PH for preapprovals.
- **Task:** Design and implement a process and mechanism for manually importing existing client data to PH from a CSV file containing the data in table format.

<br><br>

## II. Usage Scenarios
<details>

A new clinic:  
1. Newly onboarded clinic is not able to integrate existing client data to PH because we do not yet support their specific PIMS system
2. Clinic exports ALL customer data into some file format
3. Clinic ships that data file off to ISR for modification
4. ISR uses that data file to convert into/create a CSV file that follows the specifically defined format 
5. ISR ships the well-formatted, valid CSV file to PH engineers 
6. PH engineers use manual import tool to import the data to PH 
7. The clinic can now write prescriptions to their existing customers using PH

<br>

An existing clinic:
1. An existing clinic is not able to integrate existing client data to PH because we do not yet support their specific PIMS system
2. The clinic now experiences the pain point of having to ask existing clientele for their name and address to write a preapproval
3. Clinic exports ALL existing customer data into some file format (some data may now overlap with existing data in PH)
4. Clinic ships that data file off to ISR for modification
5. ISR uses that data file to convert into/create a CSV file that follows the specifically defined format 
6. ISR ships the well-formatted, valid CSV file to PH engineers 
7. PH engineers use manual import tool to import the data to PH, **ensuring that duplicated data is taken care of, and discrepancies in duplicated data do not crash the import**
8. The clinic no longer needs to spend time asking their existing customer for their name and address to write a prescription, increasing the changes of using PH to write one.

</details>

<br><br>

## III. Preliminary Research
<details>

**Definitions**  
- PIMS: Practice Information Management System  
- PHI: Pet Health Integrations  
- ISR: Internal Sales Representative (work with clinics at onboarding)  
- CSR: Customer Service Representative (work with clinics after onboarding onward)  

<br>

**Existing PIMS Integration Process**  
May 13th - Meeting Minutes with Prarabdh Gaur

1.	Overview of PIMS integration
2.	Once a clinic is PIMS integrated - do they need to keep their old system running forever? Are we always making calls to that system?
3.	What does “non-pims-integrated” clinic mean?
4.	How many PIMS vendors are we set up to handle? How many are there? What are some popular names of PIMS vendors?
5.	How does PIMS handle a clinic having more than 1 PIMS vendor? do we always need to call to BOTH to find the information we need?
6.	Stakeholders’ experiences:  
⁃	What is the VET experience with PIMS integration?  
⁃	What is the ISR experience with PIMS integration?  
⁃	What is the BE (PH) experience with PIMS integration?  
7.	Is integration a one-time process or is it something that needs maintenance after the clinic is set up?
8.	Can a clinic become PIMS integrated after already being onboarded?
9.	How do you test PIMS integration? How do you know it’s working? How do you handle errors? 

**PIMS Definition:** A PIMS is a software system that supports scheduling appointments, storing patient data, medications, orders, and everything that needs management in a vet clinic.  
PIMS integration is: PH makes a call to a 3rd party aggregator (not Chewy's) that then makes a call to the PIM to fetch the requested data.  
**Non-PIMS-Integrated clinic** refers to a clinic whose existing PIMS vendor is not yet integrated by the 3rd party aggregator.  
**Each clinic has 1 PIMS vendor.** There are many different PIMS vendors so clinics chose one of many different vendors. From the aggregator's perspective they need to handle data from all of the different vendors. The aggregator then sends Chewy data in canonical form, so PIMS integration service handles data in a unified format. (But for the manual import, we will have to be the entity handling data in differing formats from different vendors)  
**Process:** Integration is set up manually. Clinic needs to sign an agreement with the 3rd party provider that we consume the data from. Takes time. Emails sent back and forth. Because PIMS sw is on-premise, the 3rd party aggregator must install something on the clinic system, get assigned a site ID, etc. Once side ID is assigned we create a record in the PIMS service for that clinic with that site ID. We look up site ID using our own Kyrios ID.  
**Error handling:** If the aggregator service goes down or if the on-premise server goes down then PH is not able to look up customer information etc.  
**Issue:** ISRs do not have access to prouction data. 

<br>

**Customer Service/Business Needs**  
May 16th - Meeting Minutes with Lisa Kodya, PM of PH  

Technical background  
We currently integrate with four PIMS. Anything outside these 4 are not able to be integrated:  
https://chewyinc.atlassian.net/wiki/spaces/D/pages/1526535756/PIMS+Integration+w+Practice+Hub  
Point of Contact: Chris Miley  
Chris is in engineering, currently working on expanding our offerings as far as integrations.  
We are currently testing what it looks like for a clinic to become integrated POST activation. Retroactive integration has been an issue. We are looking for clients who are willing to be part of the beta testing for this.  
Cornerstone is the only PIMS we have writeback capabilities for.  
**Clinics are conerned with data privacy.** They are concerned that we will steal their data when we integrate.  
Pet data is integral to clinic data. If this data migration does not support pet data, it is nearly useless. **Question: If this migration does *not* include pet data, what happens when pet parent places an order on chewy.com associated to their pet. How does that order appear in PH if the pet is not stored in PH?**

ISR capabilities  
ISR role does not include data manipulation in any form. This project should steer away from that expectation.  
Onboarding Analyst verifies when everything required prior to activation is completed and coordinates with technical resources (ie Bence) to provide tech team with the lists/scripts to onboard the cohort of clinics each week.  
Instead of expecting ISRs to manipulate data, the data migration option should be part of the list given to the tech team each week.Then, the script should live with the tech team to be run by the tech team.  
**The question still stands: Who should be responsible for data migration then?**  

What led to this manual import project becomming a need:
1. A clinic had a PIM vendor we DO integrate with (atamark)
2. They were switching over to a vendor we DO NOT integrate with
3. They had the idea that they wanted to quickly MIGRATE (not integrate) all customer data from their old vendor into PH before switching to their new vendor in order to avoid having to re-enter existing customer data into PH after giving up their atamark system.  

Updated use cases:  
| Use Case    | Integration | Migration | Result |
| ----------- | ----------- | ----------- | ----------- |
| Clinic uses a vendor we are not integrated with| Not possible  | Does not want to migrate  | Clinic will have to double-enter all data moving forward  |
| Clinic uses a vendor we are not integrated with| Not possible  | Wants to migrate at onboarding  | Clinic will not have to re-enter existing data, but will have to double-enter all new data moving forward  |
| Clinic uses a vendor we are not integrated with| Not possible  | Did not migrate at onboarding but x time later wants to migrate  | Clinic will not have to re-enter existing data, but will have to double-enter all new data moving forward, and we will have to deal with data discrepancies that rise out human error during of double-entry  |
| Clinic uses a vendor we are integrated with  | Chose integration at onboarding  | No use case for migration  | Clinic is integrated  |
| Clinic uses a vendor we are integrated with  | Did not chose integration at onboarding  | While migration is possible, we will strongly suggest they integrate instead of migration (esp once beta testing is complete)  | Clinic ought to be integrated  |

Updated Purpose:  
If this data migration tool does not exist: non-PIMS-integrated clinics will have to double enter ALL data for EVERY single client.  
If this data migration tool does exist: non-PIMS-integrated clinics will only have to double enter data for NEW clients, not existing clients.  
This data migration tool does not solve the non-PIMS-integrated pain point of double entry, it only minimizes it.  
It will be important to be clear about that expectation with clients (clinics) in the future.  

<br>

**Conclusion**  
Integration >>>>> migration  
Migration is only a temporary solution  
It does not solve any paint point but is a crutch/lessens the double-entry problem  
If a clinic has a PIMS vendor we are integrated with, we ought to never offer to perform a data migration for them  

<br>

**Outstanding Questions**
1. Who should be responsible for formatting the data for the manual import then?
2. If this migration does *not* include pet data, what happens when pet parent places an order on chewy.com associated to their pet. How does that order appear in PH if the pet is not stored in PH?

</details>

<br><br>

## IV. Technical Planning (In progress)
<details>

**Import File Format**  
To start, we will collect
- customer email
- customer first name
- customer last name 
- shipping address (how do we parse this in our mutation?)

TODO: create an example file and link here
Where will we store the files? (ISRs should)
What is the output or report that is generated after a file syncs?


Unique customer identifier options
- customer email
- combination of customer first and last name
- combination of customer first, last name and email

<br>

TODO: MVP and incremental development plan 

**Design Decisions**  
TODO:  logic plan  
TODO: tool plan (which language, frameworks)  
TODO: how to handle identical, duplicate date  
TODO: how to handle duplicate data with descrepancy (eg the same user and email, but different address)  
TODO: how to handle errors  
TODO: can this tool be run multiple times? what happens when we rerun an import? should it replace existing records? if im already in there but my address has changed should it change my address?  
TODO: unique ID is email for the customer - if someone updates their email address , they will appear in the system TWO times (is that okay? is there another checking mechanism?)  
TODO: will I leverage existing mutations or create my own? do the existing ones suffice?
How will we map files to clinics? will each clinic have its own file/folder per PIM vendor? what if a clinic has >1 PIM vendor, do they need to consolidate into one file or do we run it multiple times?

what if there is a network error in the middle of a sync?? - the report should indicate where they stopped. How would we continue from there if stopped in the middle of the process? Should we repeat the process from the beginning or start from where we left off? (i think from the beginning and the thing will handle it)

How to report invalid data during import? eg invalid birthday 
What if a clinic's previous PIM vendor did not collect information that is required for us to collect? Will it lead to errors? should the system not conclude without the ISR inputting data?

maybe the people running this do not have the right python environment 
client command line application tool instead 
however you can create an executable 

Minor: encoding? UTF-8 or ASCII
Minr: can accept CSV OR EXCEL? it will be pretty common for clinics to export in Excel files 
CSV is better for developers 


<br>

**Test Plan**  
TODO: if the tool can go both ways - generate the CSV file from a PH instance as well as PH to CSV and the files will be the same 

<br>

**Further Development**  
TODO: how can we also manually import PET information??  
- PIMS will have different mappings for breed and type etc
- PIMS service abstracts over that
What about if a customer is deactivated from the system or if a pet is deceased?

TODO: how to scale this tool to be used in the PH platform as a front end supported feature of PH  
eg click import and point to a file to import them 

</details>

<br><br>

## V. User Guide (In progress)
<details>

-What's the exact format for ISR to convert into ?  
-What are the requirements  
-how to use this tool at the command line  

</details>




