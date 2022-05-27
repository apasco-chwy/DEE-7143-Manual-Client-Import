# Spike DEE-7143: Manual Client Import

A command line program for manually importing non-PIMS-integrated clinic data to PH.

<br>

## I. Background  

- **Problem:** There are currently over 600 production clinics using PH. Clinics that do not have a PIMS connection are less likely to use PH to write prescriptions for Chewy customers because **it is time consuming to manually enter existing client names, addresses, and pets.** Currently, 40% of clinics on Practice Hub have a cloud-based PIM where integration is not available. 
- **Primary Goal:** Offer new non-PIMS-integrated clinics the option to manually import all existing client data to PH at onboarding to reduce the amount of double-entry needed moving forward. Clinics will not have to enter their existing client data into PH.
- **Potential Scale:** Offer existing non-PIMS-integrated clinics the option to manually combine existing client data from their PIMS with existing client data in PH after they've already started using PH.
- **Task:** Design a process for manually importing existing client data to PH. Define the required data format. Implement a program that consumes data in the predefined format and manually creates and imports the data into the clinic's PH instance database.

<br><br>

## II. Usage Scenarios
<details>

<br>

A new clinic:  
1. Newly onboarded clinic is not able to integrate their PIMS to PH because we do not yet support their specific PIMS vendor
2. Clinic exports ALL customer data into some file
3. During onboarding week, the file gets shipped: From Clinic -> To ISR -> To PH Engineers 
4. PH engineers convert data file into a CSV file that follows the pre-defined format 
5. PH engineers feed the well-formatted, valid CSV file into the manual import tool
6. All clinic client data is now stored in the clinic's PH database  

**Impact:** The clinic can now write prescriptions to their *existing* customers without having to enter existing customer information into PH.  
**Note:** If the clinic recieves a *new* customer, they will have to enter new customer data in both PH and their existing PIMS system.  

<br>

An existing clinic:
1. An existing clinic was not able to integrate their PIMS to PH because we do not yet support their specific PIMS vendor
2. The clinic now experiences the pain point of having to ask existing clientele for their name and address to write a preapproval
3. Clinic exports ALL customer data into some file (some data may now overlap with existing data in PH)
4. Clinic ships the file: From Clinic -> To ISR -> To PH Engineers
5. PH engineers convert data file into a CSV file that follows the pre-defined format  
6. PH engineers feed the well-formatted, valid CSV file into the manual import tool, **ensuring that duplicated data is taken care of, and discrepancies in duplicated data do not crash the import**  

**Impact:** The clinic can now write prescriptions to their *existing* customers without having to enter existing customer information into PH.  
**Note:** If the clinic recieves a *new* customer, they will have to enter new customer data in both PH and their existing PIMS system.  


</details>

<br>

## III. Preliminary Research
<details>

<br>

**Definitions**  
- PIMS: Practice Information Management System  
- PHI: Pet Health Integrations  
- ISR: Internal Sales Representative (work with clinics at onboarding)  
- CSR: Customer Service Representative (work with clinics post-onboarding onward)  

<br>

<hr>

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

<hr>

**Customer Service/Business Needs**  
May 16th - Meeting Minutes with Lisa Kodya, PM of PH  

Technical background  
- We currently integrate with four PIMS. Anything outside these 4 are not able to be integrated:  
https://chewyinc.atlassian.net/wiki/spaces/D/pages/1526535756/PIMS+Integration+w+Practice+Hub  
- Point of Contact: Chris Miley - Chris is in engineering, currently working on expanding our offerings as far as integrations.  
- We are currently testing what it looks like for a clinic to become integrated POST activation. Retroactive integration has been an issue. We are looking for clients who are willing to be part of the beta testing for this.  
- Cornerstone is the only PIMS we have writeback capabilities for.  
- **Clinics are conerned with data privacy.** They are concerned that we will steal their data when we integrate. **Question: Will this stop them from sending us a data file containing all of their customer data? How will we securely transfer that? What if the file is too large to transfer?**
- Pet data is integral to clinic data. If this data migration does not support pet data, it is nearly useless. **Question: If this migration does *not* include pet data, what happens when pet parent places an order on chewy.com associated to their pet. How does that order appear in PH if the pet is not stored in PH?**

ISR capabilities  
- ISR role does not include data manipulation in any form. This project should steer away from that expectation.  
- Onboarding Analyst verifies when everything required prior to activation is completed and coordinates with technical resources (ie Bence) to provide tech team with the lists/scripts to onboard the cohort of clinics each week.  
- Instead of expecting ISRs to manipulate data, the data migration option should be part of the list given to the tech team each week. Then, the script should live with the tech team to be run by the tech team.  
- **The question still stands: Who should be responsible for *formatting* data for the migration then?**  

What led to this manual import project becomming a need?  
1. A clinic had a PIM vendor we DO integrate with (atamark)
2. They were switching over to a vendor we DO NOT integrate with
3. They had the idea that they wanted to quickly MIGRATE (not integrate) all customer data from their old vendor into PH before switching to their new vendor in order to avoid having to re-enter existing customer data into PH after giving up their atamark system.  

<br>

**Updated Use Cases**  
| Use Case    | Integration | Migration | Result |
| ----------- | ----------- | ----------- | ----------- |
| Clinic uses non-integrated vendor | Not possible  | Does not want to migrate  | Clinic will have to double-enter all data moving forward  |
| Clinic uses non-integrated vendor | Not possible  | Migrates at onboarding  | Clinic will not have to re-enter existing data, but will have to double-enter all new data moving forward  |
| Clinic uses non-integrated vendor | Not possible  | Did not migrate at onboarding but x time later wants to migrate  | Clinic will not have to re-enter existing data, but will have to double-enter all new data moving forward, and we will have to deal with data discrepancies that rise out human error during previous double-entry  |
| Clinic uses integrated vendor | Integrated at onboarding  | No use case for migration  | Clinic is integrated  |
| Clinic uses integrated vendor | Did not chose to integrate at onboarding  | While migration is possible, we will strongly suggest they integrate instead of migration (esp once beta testing is complete)  | Clinic ought to be integrated  |

<br>

**Updated Purpose**  
- If this data migration tool does not exist, non-PIMS-integrated clinics will have to double enter ALL data for EVERY single client.  
- If this data migration tool does exist, non-PIMS-integrated clinics will only have to double enter data for NEW clients, not existing clients.  
- This data migration tool does not solve the non-PIMS-integrated pain point of double entry, it only minimizes it.  
- It will be important to be clear about that expectation with clients (clinics) in the future.  

<br>

<hr>

**Conclusion**  
- Integration > migration  
- Migration is only a temporary solution. It does not solve any paint point but is a crutch/lessens the double-entry problem.
- If a clinic has a PIMS vendor we are integrated with, we ought to never offer a data migration to them.
- Initially, this project aimed to create a tool to be used by ISRs during onboarding. In the initial plan, the ISRs were responsible for formatting data from clinics into a CSV file in a pre-defiend format. I no longer believe this to be an ideal solution. Data manipulation is not the responsibility of an ISR. 
- Initially, this project aimed to support data migrations for existing non-integrated clinics post-onboarding. I have since re-evaluated this case and have determined that it should be avoided. The data descrepancies that will arise via human error between the time the clinic is onboarded and when they wish to migrate ought not to cost Chewy engineers their time. This data migration service, if offered, should only be offered at the time of onboarding, but not after. 
- The biggest challenge of this project will be working with clinics to export the client data file.
- The use cases and background of this report have been updated to reflect the above findings. 

<br>

</details>

<br>

## IV. Project Implementation Stages
<details>

<br>

**Stage 1: (To complete during Coop)** Create a simple tool that reads in data of a canonical form and calls GraphQL mutations to create/populate that data in PH. Assumes the manual import is taking place **at onboarding time** not after.

**Stage 2: (To be completed by PHI)** Automate the process of converting the clinic-given file into the canonical file format. Requires pet data mapping.

**Stage 3:** Improve the manual import tool to handle potential duplicate data, such that it can be offered to clinics after onboarding. Requires some mechanism for handling duplicate data because by this time the clinic may have already started entering existing client data into PH, so when we pull all the data from their PIMS vendor into PH there will be overlaps. 

</details>

<br>

## V. Technical Planning (In progress)
<details>

<br>

**Import File Format**  
For each clinic performing a manual import, there will exist two CSV files:
1. The input CSV: raw data from the clinic (uncleaned data)
2. The formatted CSV: the canonically formatted CSV that is ready to be fed into the manual import tool (cleaned data) 

<br>

The formatted CSV will have the following columns:

| Column Title| Type and Restrictions | 
| ----------- | ----------- | 
| **firstName** | a String | 
| **lastName** | a String | 
| **email** | a valid email address | 
| **streetAddress1** | a String | 
| **streetAddress2** | a String | 
| **city** | not abbreviated, must exist within the countryArea | 
| **postal code** | must be valid given the countryArea | 
| **countryArea** | accepts formats “MA” “Massachusetts” “ma” “Ma” | 
| **country** | optional, default is “US” | 
| **phone** | a valid phone number, MUST be prepended with country code (eg +1)  | 
| **name** | a String | 
| **kind** | one of ['Reptile', 'Bird', 'Cat', 'Horse', 'Fish', 'Large Animal', 'Dog', 'Small Pet'] | 
| **breed** | must exist in Chewy’s breed mapping | 
| **gender** | one of [‘Male’, ‘Female’, ‘Unknown’] | 
| **weight** | a Float, must be > 0.0 | 
| **birthday** | a String formatted YYYY-MM-DD | 

<br>
 

**Design Decisions**  

Step 1 : determining tools/frameworks, plan logic, plan use of mutations
Python, pandas, etc 
any way to create an executable

Step 2 : execute using test data 
-meet with prarabdh to determing the string matching algorithm 
— if a pet is deceased then we do not want to collect that info -> this should be taken care of in the first step where the cleaning takes place

Step 3 : handling errors/Error reporting
At the end of the import, a report will print with any errors that occured during the import
What if there is a network error in the middle of a sync? -> the report should indicate where it stopped such that we can re-start it from where it left off. Perhaps include an option to say where to start the import from?
What happens when the tool is run multiples times? by mistake or because of a network error? -> as of right now in this first step we will not be checking for duplicate data we will simply always create new data as we go though the input file. The next step will be checking for duplicate data.
How to report invalid data during import? eg invalid birthday format -> hopefully this will be taken care of before the data reaches the import tool. All formatting will be done prior to the import tool being run such that the formatted CSV will not produce any formatting errors in the mutations.

What if a clinic's previous PIM vendor did not collect information that is required for us to collect? -> perhaps we should create two new mutations for creating customers and pets that allow some data to be missing. But then moving forward will the missing data cause crashes? probably.

Minor: encoding? UTF-8 or ASCII
Minor: can accept CSV OR EXCEL? it will be pretty common for clinics to export in Excel files 

<br>

**Test Plan**  
Test Cases:
- data format invalid -> expected: take that row of data and add it to the output report along with the error 
- network error in the middle of sync -> expected: take that row of data and add it to the output report along with the error (probably a network error) because it will output a report with all the rows of data that never got imported, we can just use that output report CSV as the new input of running it a second time.

<br>

**Further Development**  
Things to think about when approaching stage 3, handling duplicate data for post-onboarding imports:
- unique ID is email for the customer - if someone updates their email address , they will appear in the system TWO times (is that okay? is there another checking mechanism?) 
- how to handle identical, duplicate data that exists in the PIMS system before the import: Can look at the MOST RECENTLY updated record and keep that one? Create some kind of “score” that helps the user understand which data might be the most up to date and let the user decide which data to keep?
- how to handle duplicate data with descrepancy (eg the same user and email, but different address)  

</details>

<br>

## VI. User Guide (In progress)
<details>

<br>
Important to be transparent with clinics that this migration does not solve their problem - they still need to double enter all new client data. Setting expectations is important for a solution that does not entirely solve the problem.
-What are the requirements  
-how to execute this tool at the command line  
</details>




