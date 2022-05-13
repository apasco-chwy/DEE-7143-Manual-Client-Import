# Spike DEE-7143: Manual Client Import

A command line program for manually importing non-PIMS-integrated clinics to PH.

<br>

## I. Background  

- **Problem:** There are currently over 230 production clinics using PH. Clinics that do not have a PIMS connection are less likely to use PH to write prescriptions for Chewy customers because **it is time consuming to manually enter existing client names, addresses, and pets.**
- **Primary Goal:** Offer new non-PIMS-integrated clinics the option to manually import all existing client data to PH at onboarding to support an easy starting point. 
- **Potential Scale:** Offer existing non-PIMS-integrated clinics the option to manually combine client data from their PIMS with existing client data in PH to create a single point of storage of client data in PH, improving quality of life and increasing their chances of using PH for preapprovals.
- **Task:** Design and implement a process and mechanism for manually importing existing client data to PH from a CSV file containing the data in table format.

<br>

## II. Usage Scenarios

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
1. An existing clinic chose not to integrate existing client data to PH at onboarding
2. The clinic now experiences the pain point of having to ask existing clientele for their name and address to write a preapproval
3. Clinic exports ALL existing customer data into some file format (some data may now overlap with existing data in PH)
4. Clinic ships that data file off to ISR for modification
5. ISR uses that data file to convert into/create a CSV file that follows the specifically defined format 
6. ISR ships the well-formatted, valid CSV file to PH engineers 
7. PH engineers use manual import tool to import the data to PH, **ensuring that duplicated data is taken care of, and discrepancies in duplicated data do not crash the import**
8. The clinic no longer needs to spend time asking their existing customer for their name and address to write a prescription, increasing the changes of using PH to write one.

<br>

## III. Preliminary Research

**Definitions**  
- ISR: Internal Sales Rep 
- PIMS: Practice Information Management System
- PHI: Pet Health Integrations 

<br>

**PIMS**  
1.	Overview of PIMS integration - I have little to no knowledge of this process so you can break it down as much as you can
2.	Once a clinic is PIMS integrated - do they need to keep their old system running forever? Are we always making calls to that system?
3.	What does “non-pims-integrated” clinic mean?
4.	How many PIMS vendors are we set up to handle? How many are there? What are some popular names of PIMS vendors?
5.	How does PIMS handle a clinic having more than 1 PIMS vendor? do we always need to call to BOTH to find the information we need?
6.	Stakeholders’ experiences:
⁃	What is the VET experience with PIMS integration?
⁃	What is the ISR experience with PIMS integration?
⁃	What is the BE (you) experience with PIMS integration? What do you do? How do we use your services in PH?
7.	Is integration a one-time process or is it something that needs maintenance after the clinic is set up?
8.	Can a clinic become PIMS integrated after already being onboarded?
9.	Milos said that PIMS is a setting in the instance of a clinic-but I do not see anything in the settings. What does this mean?
10.	How do you test PIMS integration? How do you know it’s working? How do you handle errors?

Meeting Minutes with Prarabdh  
May 13th  
<br>
TBD


<br>

**ISR**  
TODO: connect with ISR to understand scope and ISR tools 


<br>

## Technical Planning
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

<br>

## User Guide
<details>

-What's the exact format for ISR to convert into ?  
-What are the requirements  
-how to use this tool at the command line  

</details>




