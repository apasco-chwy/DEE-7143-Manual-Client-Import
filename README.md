# Spike DEE-7143: Manual Client Import (MCI)  

A command line program for manually importing non-PMS-integrated clinics to PH.

<br>

## Background  

- **Problem:** There are currently over 230 production clinics using PH. Clinics that do not have a PMS connection are less likely to use PH to write prescriptions for Chewy customers because **it is time consuming to manually enter existing client names, addresses, and pets.**
- **Goal:** Offer new non-PMS-integrated clinics the option to manually import all existing client data to PH at onboarding to support an easy starting point. Offer existing non-PMS-integrated clinics the option to manually combine client data from their PMS with existing client data in PH to create a single point of storage of client data in PH, improving quality of life and increasing their chances of using PH for preapprovals.
- **Task:** Design and implement a process and mechanism for manually importing existing client data to PH from a CSV file containing the data in table format.



<br>
<details><summary>Usage Scenarios</summary><br>

A new clinic:  
1. Newly onboarded clinic is not able to integrate existing client data to PH because we do not yet support their specific PMS system
2. Clinic exports ALL customer data into some file format
3. Clinic ships that data file off to ISR for modification
4. ISR uses that data file to convert into/create a CSV file that follows the specifically defined format 
5. ISR ships the well-formatted, valid CSV file to PH engineers 
6. PH engineers use MCI to import the data to PH 
7. The clinic can now write prescriptions to their existing customers using PH

<br>

An existing clinic:
1. An existing clinic chose not to integrate existing client data to PH at onboarding due to lack of understanding of the process
2. The clinic now experiences the pain point of having to ask existing clientele for their name and address to wrire a preapproval
3. Clinic exports ALL existing customer data into some file format (some may now overlap with existing data in PH)
4. Clinic ships that data file off to ISR for modification
5. ISR uses that data file to convert into/create a CSV file that follows the specifically defined format 
6. ISR ships the well-formatted, valid CSV file to PH engineers 
7. PH engineers use MCI to import the data to PH, **ensuring that duplicated data is taken care of, and discrepancies in duplicated data do not crash the import**
8. The clinic no longer needs to spend time asking their existing customer for their name and address to write a prescription, increasing the changes of using PH to write one.



<br><br>
</details>
<details><summary>Preliminary Research</summary><br>

**Definitions**  
- ISR: Internal Sales Rep 
- PMS: Practice Management Software (pronounced "pims")
- PHI: Pet Health Integrations 

<br>

**Onboarding**  
TODO: As of now, how do we integrate *with* PMS? What is the **vet experience** connecting their PMS to PH? How can we fit the manual import into onboarding? How can we fit the manual import into existing clinics?

<br>

**PMS**  
TODO: connect with Prarabsh and Asha for Overview of PMS service 

<br>

**ISR**  
TODO: connect with Cindy Hearn to understand ISR capabilities 



<br><br>
</details>
<details><summary>Technical Planning</summary><br>

**Import File Format**  
To start, we will collect
- customer email
- customer first name
- customer last name 
- shipping address (how do we parse this in our mutation?)

TODO: create an example file and link here

Unique customer identifier options
- customer email
- combination of customer first and last name
- combination of customer first, last name and email



<br>

**Design Decisions**  
TODO:  logic plan  
TODO: tool plan (which language, frameworks)  
TODO: how to handle identical, duplicate date  
TODO: how to handle duplicate data with descrepancy (eg the same user and email, but different address)  
TODO: how to handle errors  
TODO: can this tool be run multiple times? what happens when we rerun an import? should it replace existing records? if im already in there but my address has changed should it change my address?  
TODO: unique ID is email for the customer - if someone updates their email address , they will appear in the system TWO times (is that okay? is there another checking mechanism?)  
TODO: will I leverage existing mutations or create my own? do the existing ones suffice?

<br>

**Test Plan**  
TODO: if the tool can go both ways - generate the CSV file from a PH instance as well as PH to CSV and the files will be the same 

<br>

**Further Development**  
TODO: how can we also manually import PET information??  

TODO: how to scale this tool to be used in the PH platform as a front end supported feature of PH  
eg click import and point to a file to import them 




<br><br>
</details>
<details><summary>User Guide</summary><br>
-What's the exact format for ISR to convert into ?  
-What are the requirements  
-how to use this tool at the command line 
<br>
</details>




