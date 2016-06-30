# fmeasysync
EasySync is an award-winning, alternative, open source sync framework for the FileMaker platform. It's native, simple, transactional, fast, and flexible.



# EasySync Setup

These instructions are designed to help you configure EasySync for use in your own FileMaker solution.



## Configuring Your Hosted Database


**Step 1. Add the EasySync fields to the tables that you want to sync.**

• ES_Record_UUID: Text field, Auto-Enter with calculated value: Get ( UUID )

• ES_UTC_Time: Number, Auto-Enter with calculated value: Let ( trigger = GetField ( "" ); If ( IsEmpty ( $script_override ) ; Get ( CurrentTimeUTCMilliseconds ); Self ) ). Be sure to uncheck the "Do not replace existing value of field (if any)" option.

• ES_Device_ID: Text, Auto-Enter with calculated value: Let ( trigger = GetField ( "" ); If ( IsEmpty ( $script_override ) ; Get ( PersistentID ); Self ) )

• ES_Account: Text, Auto-Enter with Modification Account Name

• ES_Exclude: Number
The easiest way to add the fields to tables in your database is to copy and paste the from the demo database.

If there is existing data in the tables that you want to sync, then you will need to update the records in those tables so that they have values in their "ES_Record_UUID" and "ES_UTC_Time" fields. To do so:

• Find all records. 

• In the ES_Record_UUID, field do a "Replace Field Contents" using formula: Get ( UUID )

• In the ES_UTC_Time, field do a "Replace Field Contents" using formula: Get ( CurrentTimeUTCMilliseconds )


**Step 2. For any container fields that you want to sync, setup corresponding "B64_" fields for.**

For example, if the container field is named "Photo_Container" then setup a new field named "B64_Photo_Container." Setup the field as a calculation with this formula: Photo_Container & "<b64>" & Base64Encode ( Photo_Container ) & "</b64>" The calculation result should be set to Text.


**Step 3. Import the "EasySync" table from the demo database into your database.**

The "EasySync" table is identical in both databases, so it doesn't matter which database you copy the table from.


**Step 4. Update your relationship graph.**

• If there is an EasySync table occurrence on it, rename it to EasySync_Payloads. (If not, then add a new table occurrence, name it "EasySync_Payloads," and base it on the EasySync table.)

• Add a second occurrence of the EasySync table to the graph. Call this one "EasySync_Payload_Details."

• Setup a relationship between the EasySync_Payloads and EasySync_Payload_Details table occurrences. Base the relationship on: EasySync_Payloads::Payload_UUID = EasySync_Payload_Details::ES_Parent_Payload_UUID

• On the EasySync_Payload_Details side of the relationship, "Allow creation of new records" and "Delete related records."

• Add table occurrences for the tables that you want to sync. Name them with an ES_ prefix followed by a name that means something to you. For example, you might setup a table occurrence for "Surveys" with the name "ES_Surveys." For each of those table occurrences, setup a relationship between them and the EasySync_Payload_Details table occurrence.

• Base the relationship on: EasySync_Payload_Details::ES_Child_Record_UUID = ES_Table::ES_Record_UUID. On the "ES_Table" side of the relationship, "Allow creation of new records." (Do not set the the "Delete related records" option.)
EasySync supports both "push-only" and "pull-only" tables. To indicate that a table is to be pushed from the mobile device, but not pulled, use an "ES_PUSH_" prefix for the table occurrence's name. To indicate that a table is to be pulled from the host, but not pushed from the mobile device, use an "ES_PULL_" prefix for the table occurrence's name.


**Step 5. Import the "EasySync Server" script folder from the sample "hosted" database into your hosted database.**

The "Import Summary" should report that 8 items were imported, with nothing renamed and no errors.


**Step 6. Add the "EasySync_Payloads" layout.**

The easiest way to add the layout is to copy all of the layout objects from "EasySync_Payloads" layout in the sample hosted database. Otherwise refer to the layout in the demo database and reconstruct it.


**Step 7. If you are planning to use EasySync's deletion support,** be sure to setup layouts for each of the tables that you are syncing, and name them with an ES_ prefix.

These layouts are used by EasySync to delete records that have been deleted on the mobile device.


**Step 8. Review Settings.**

That is all that is involved in configuring your hosted database. You might want to review the hosted database's EasySync settings. (All of the settings are configured as global variables in the "EasySync Settings" script.)



## Configuring Your Mobile Database


**Steps 1 thru 4.**

Repeat steps 1 thru 4 from the "Configuring the Hosted Database" instructions above.


**Step 5. Import the "EasySync Mobile" script folder from the sample mobile database.**

The "Import Summary" should report that 9 items were imported, with nothing renamed, and only 1 or 2 errors encountered.

If you review the errors log, you should see a message indicating that a "File reference "Hosted_Database" missing" and that the "Missing file reference "Hosted_Database" created and imported automatically."

If you are not importing the EasyDeploy scripts at this time, then you might see a second error indicating that a script is missing. You can either correct those errors as well, or ignore them.


**Step 6. Update the "Hosted_Database" external datasource so that points to your hosted database.**

The datasource should automatically be setup for you (as a result of importing the scripts). However, you will need to change the "Details" of the datasource so that the path is set to your hosted database.


**Step 7. Update the "Ping Server" script.**

You will need to fix the following references to scripts that are in the hosted database:

• Approximately Line 13: Perform Script on Server should point to: "Respond to Client Ping" in the "Hosted Database"

• Approximately Line 17: Perform Script should point to: "Respond to Client Ping" in the "Hosted Database"


**Step 8. Update the "Push Payload" script.**

You will need to fix the following references to scripts that are in the hosted database:

• Approximately Line 161: Perform Script on Server should point to "Receive Payload Segment from Client" in the "Hosted Database"

• Approximately Line 164: Perform Script should point to "Receive Payload Segment from Client" in the "Hosted Database"

• Approximately Line 184: Perform Script on Server should point to "Process Payload from Client" in the "Hosted Database"

• Approximately Line 187: Perform Script should point to "Process Payload from Client" in the "Hosted Database"


**Step 9. Update the "Pull Payload" script.**

You will need to fix the following references to scripts that are in the hosted database:

• Approximately Line 22: Perform Script on Server should point to "Prepare Payload for Client" in the "Hosted Database"

• Approximately Line 25: Perform Script should point to "Prepare Payload for Client" in the "Hosted Database"

• Approximately Line 40: Perform Script on Server should point to "Send Payload Segment to Client" in the "Hosted Database"

• Approximately Line 43: Perform Script should point to "Send Payload Segment to Client" in the "Hosted Database"


**Step 10. Update the "Sync Check" script.**

You will need to fix the following references to scripts that are in the hosted database:

• Approximately Line 39: Perform Script on Server should point to "Sync Check" in the "Hosted Database"

• Approximately Line 43: Perform Script should point to "Sync Check" in the "Hosted Database"


**Step 11. Setup the "EasySync_Payloads" layout.**

The easiest way to add the layout is to copy all of the layout objects from "EasySync_Payloads" layout in the sample mobile database.

Note: In the demo database database, there are several objects on the "EasySync_Payloads" layout that are positioned off of the visible (right-hand) edge of the layout. Be sure to copy those objects as well.


**Step 12. If you are planning to use EasySync's deletion support...**

Setup layouts for each of the tables that you are syncing, and name them with an ES_ prefix. These layouts are used by EasySync's "sync check" function and "wipe" functions to more efficiently delete records. For the tables that you want users to be able to delete from, add "delete" buttons to your UI and wire them up to EasySync's "EasySync Delete Record" script. (Review the header comments for that script so that you can be sure that you are passing the correct parameters to it.)


**Step 13. Review EasySync settings.**

You might want to take a moment to review the mobile database's EasySync settings. Like the hosted database, all of the settings for the mobile database are configured as global variables in the "EasySync Settings" script.
