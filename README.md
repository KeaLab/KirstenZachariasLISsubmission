# KirstenZachariasLISsubmission
Kirsten Zacharias' LIS Submission Data for SynapticSci

**Table of Contents**
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

3. **Architecture and Data Model**

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


Approach for Objective 4:
Use formulas outside of the table instead to display the value without the time appearing or causing conflicts with the data type. Since verification due date of equipment is autocalculated, it needs to be a date value in the table, but the way the data is seen by the user can be modified by editing the canvas page. Formula example: Text(ThisItem.'Next Verification Due Date', "[$-en-US]yyyy-mm-dd")



























