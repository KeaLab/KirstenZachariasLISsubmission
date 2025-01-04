**Table of Contents
1. **Introduction**
2. **Functionalities**
3. **Architecture and Data Model**
4. **User Interface (UI) Development**
5. **Workflow and Processes**
6. **Status Tracking and Business Rules**
7. **Challenges and Solutions**
8. **Potential Improvements**
9. **Conclusion**

---

1. **Introduction**
The model-driven app is named *LIMS Lite* (referred to as the "app").

The purpose of this model-driven app is assisting a laboratory's technicians with referencing and capturing on-site data for tasks regarding sample tracking, documentation access, equipment maintenance and chemical inventory. These records are required to adhere to the lab's quality management system under regulatory accreditation. The app serves as an example of a quick, user-friendly way to manage records at the lab technician security level.

---

2. **Functionalities**
The main activities the lab technician can perform using the app are as follows:

- Function A: View a read-only quick snapshot of work to be done using a canvas page nested within the model-driven app

- Function B: Track/edit the status, assigned testing, and disposal of samples

- Function C: Manage the chemical inventory of the lab's chemicals

- Function D: Record performance of routine maintenance on lab equipment

- Function E: Access/Edit documentation file links for procedures, policies and safety data sheets (SDS) 

---

3. **Architecture and Data Model

| Tables                               | Forms                                                                                                                                        | Views and Charts                                                                                                                                                                                                                                                                                                                                                                                           | Rules & Data Validation                                                                                                                                                                                                                                                                                                                | Relationships, Lookups and Choices                                                                                                                              |
| ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| *Chemical Inventory*<br><br><br><br> | Main form with subgrids to view & edit                                                                                                       | Main view for chemical names, lot, expiration, SDS, storage. <br><br>Another custom view for chemicals that expire within 30 days                                                                                                                                                                                                                                                                          | None                                                                                                                                                                                                                                                                                                                                   | SDS file links to SharePoint <br><br>Lookup chemical storage location from Locations                                                                            |
| *Documents*                          | Main form with subgrids to view & edit                                                                                                       | Main view for doc ID, title, link.                                                                                                                                                                                                                                                                                                                                                                         | Links must be available (no blanks)                                                                                                                                                                                                                                                                                                    | Company policy and SOP file links to SharePoint<br><br>Choose document source as internal or external                                                           |
| *Equipment*                          | Main form with subgrids to view & edit                                                                                                       | Main view for equipment ID, type, serial no., location and verification tracking                                                                                                                                                                                                                                                                                                                           | Formulated table column to give 1 of 3 values based on the equipment's verification dates and verification frequency:<br><br>- "Verification in Date" (no verification needed)<br><br>-"Verification Overdue!" (next verification due date has passed)<br><br>-"Verify Soon" (for equipment requiring verification in the next 7 days) | Lookup where the equipment resides from the rooms of the Locations table                                                                                        |
| *Locations*                          | Main form, one section                                                                                                                       | Main view for facility locations (room numbers), off-site status and personnel responsible for maintenance                                                                                                                                                                                                                                                                                                 | A member of staff must be responsible for the location                                                                                                                                                                                                                                                                                 | Lookup staff member responsible for room housekeeping and maintenance                                                                                           |
| *Personnel*                          | Main form, one section                                                                                                                       | Main view for all lab staff members and                                                                                                                                                                                                                                                                                                                                                                    | None                                                                                                                                                                                                                                                                                                                                   | Choose a staff member's department                                                                                                                              |
| *Samples*                            | Main form with subgrids to view & edit as well as 2 additional sections for viewing samples available to test and samples ready for disposal | Main view for sample ID, test assigned, status, matrix, location<br><br>Sample Disposal View & Chart 1: View only samples that have been approved for disposal, bar chart for viewing breakdown of all sample statuses<br><br>Available Samples for Testing & Chart 2: View all samples with the "Ready to Test" status, bar chart for viewing breakdown of which tests were ordered for available samples | Business rule: Sample disposal data remains locked until the sample status is ""                                                                                                                                                                                                                                                       | Lookup where the sample is stored on site from Locations table<br><br>Lookup staff member that received sample from Personnel table<br><br>Choose sample status |

---

4. **User Interface (UI) Development**

Objective 1: to remove/hide any table data that isn't relevant or necessary for the lab technician level. For example, a lab tech might need to know who is required to authorize the disposal of a sample but the *hire date* of the person (located in the *Personnel* table) is not relevant to the disposal task itself.
	Result: To-Do List home page in app is the first screen seen by the user, containing essential priority job tasks (samples to test or dispose of, equipment that needs calibrating and chemicals that will be expired soon and may need disposal or re-ordering)

Objective 2: design the app for a lab technician role, providing a main quick-reference screen but also separate navigation tabs in the app sidebar for each relevant area of work such as samples, equipment and chemical inventory so the user can easily navigate to the area needed to capture data.
	Result: Data tables listed in the app sidebar have unique svg icons for quick reference and visual appeal. Associated forms are divided into multiple columns for easy lookup of specific records for editing.

Objective 3: design the forms in the app to be user-friendly enough to search and edit records as well as create new ones.

Objective 4: remove any specific **time of day** from the To-Do list as verifications of equipment and expirations of chemicals are not specific to the time and are not of use to a lab tech. 

> [!NOTE] Approach for Objective 4:
> Use formulas **outside** of the table instead to **display** the value without the time appearing or causing conflicts with the data type. Since verification due date of equipment is autocalculated, it needs to be a date value in the table, but the way the data is seen by the user can be modified by editing the canvas page. Formula example: Text(ThisItem.'Next Verification Due Date', "[$-en-US]yyyy-mm-dd")

---
5. **Workflow and Processes**

Objective: Use Power FX to automate Equipment table verification data for lab tech to perform timely verification and then use that automatic status to display the next verification date
- If a piece of equipment is due for verification in the next 7 days, verification status is "Verify Soon"
- If a piece of equipment is due for verification  >7 days from current user date, verification status is "Verification in date"
- If equipment is due for verification because the verification due date has passed, verification status is "Verification Overdue!"
Result: Success using the following formula: 
> [!NOTE] Power FX Equipment Date Automatic Status
> If(UTCToday() > 'Next Verification Due Date', "Verification Overdue!", If('Next Verification Due Date' >= UTCToday() && 'Next Verification Due Date' < UTCToday() + 7,"Verify Soon","Verification in Date"))


Objective: send an email with COA when sample testing complete

---
6. **Status Tracking and Business Rules**
Rule made for incongruent data entry logic: disposal data for a sample cannot be entered until the sample has reached appropriate status.

---

7. **Challenges and Solutions** (select drop arrow beside each challenge for solutions)

Challenge 1: Unable to add PDFs of SDS documents or pictures to each staff member in dataverse tables because columns created with the *file* or *image* type were read-only by default.
	Solution: searched the web to determine if it was a normal default setting or if I had broken something. Discovered it was these  column types were read-only by default but there was indeed a way to add files and images to these column types by utilizing forms to upload the document and image attachments. The columns I had made for these data types needed to be added to a form that was then utilized to edit the table.

Challenge 2: Dataverse table columns couldn't be deleted due to dependencies.
	Solution: learn what dependencies were, where to find them and assess whether or not I needed them. Realized that there are dependencies that exist in forms AND views (or other things like flows and business rules). Since there were few of them, I opted to go through associated dependencies one by one to remove the form or view component linked to that column for proper deletion. If I had many dependencies to table columns that needed deletion, I would have sought a quicker or "bulk" action solution.

Challenge 3: Constant interruptions to app testing due to Microsoft requesting repeated sign-ins. When using the canvas page for the To-Do list, errors were displayed and selecting "Sign In" froze the app preview.
	Solution: this was due to browser pop-up and security settings. Microsoft Power Platform sites were whitelisted and the issue was resolved easily.

Challenge 4: Scope Creep
Throughout my time working on the app, I struggled with having a rigid boundary of goals. I found myself expanding and making more tables or forms and exploring random ways to do things without focusing on one area and making that solution/function more robust.
	Solution: Narrow focus onto the functionality of main tables that a lab tech would use most and reduce resources spent on the Personnel and Locations tables.

Challenge(?) 5: Not necessarily a challenge, but I wanted to try making a workflow for the samples table where a client would be emailed when a certificate of analysis was added to their sample. I managed to create the workflow and tests were "successful". However, within the workflow, adding a certificate of analysis was passed in the system as a "False" condition result, so instead of sending an email, the flow resulted in no action. So the flow had no direct "errors" but the end result was not what I wanted.

---

8. **Potential Improvements**
Improvement 1: Create more charts for helpful data for the following scenarios:
- Add turnaround times (data column) for sample tests that can be ordered and implement calendar/timelines for workload scheduling. This would help coordinate the lab resources.
- Add table columns linked to a new pie chart for documents for useful administrative information such as seeing how many documents are in certain stages like "in review", "in edit". This is useful to implement if the scope expands from just lab techs to admin department as well.

Improvement 2: Create form for external users
- Create a Microsoft Form for external clients to submit sample requests that would then partially populate the sample table and automatically give the samples a status of "planned". 
	From this, charts could be used to aid the lab in seeing how many samples they can expect in the coming weeks and then, based on the test requested, establish a rough work schedule from the turnaround times for those tests and the number of samples.


Improvement 3: Add WHMIS pictograms to chemical inventory
- Being able to click a link to an SDS is great but those documents are many pages long. If a lab tech wants to just get a peek at the hazards they are dealing with, or need to know where a chemical can be stored on site, a quick visual reference with pictograms (and possibly the hazard statements from the SDS) would be ideal. I would create a separate table for pictograms and create a one-to-many relationship to the chemical inventory table so that the pictograms for each chemical can be seen in a sub-grid. Doing this using JavaScript or PowerApps Component Framework (PCF) approach would be out of my skillset at this time.



Innovation Idea 1: for Future Use:  Explore document file storage in dataverse as opposed to SharePoint. Then, build off of the forms used for browsing documents and use iframe to actually display a selected document as an image or preview inside the power app itself (similar to Outlook preview for email attachments). This would be a great approach for clients doing sampling in rural locations (soil sampling, farms, environmental sample collection etc.). Files could be viewed offline for any remote techs doing sample collection in rural/remote areas where internet could be limited or unreliable.

---

Don't use lorem ipsum placeholder text for every single procedure in the document system. This was utilized to prove the concept of the app functionality but in reality would be unique files actually used by the company and may include formats other than PDF specifically.

9. **Conclusion**







<details>  
<summary>Click to expand</summary>  
  
This is the content that will be hidden by default  
and revealed when the user clicks on the toggle.  
  
</details>


Other notes:
- In an app, why does the table chart option disappear after only being used once?
- In Dataverse tables, why can't images be added directly to the table without the need to add pictures via the form? SAME APPLIES FOR FILES?!
- Why, when editing dataverse table via form, does it ask me to sign in EVERY TIME?!


Functionality Goals:
- "View" the contents of a PDF so a form will have an easy-access to a chemical SDS
- Add a new SDS to the chemical inventory
- 