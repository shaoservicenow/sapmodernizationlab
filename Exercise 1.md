---
layout: default
title: Exercise 1 - DOA
nav_order: 2
---

# Exercise 1: Delegation of Authority

## Introduction

When it comes to automating ERP workflows, nothing is more painful, or more time consuming than the approval cycle. Couple that with a rigid system like SAP, and it becomes difficult to track, let alone automate approvals on everyday transactions like Purchase Orders, Sales Orders, Invoices, etc. A common term for this process is Delegation of Authority (DoA), or an Approval Matrix.

Within SAP, [there is such a feature](https://help.sap.com/doc/132b1ea8da1d4281a2da23f3cf506809/2.0.06/en-US/07dc534035524965a902c5bd6ffdbc3a.html), but it is limited in capability and does not scale for exceptional workflows.


## Tales from the field

In one of our customer's internal landscape, they reported having 54 different workflows that covers multiple business objects, and their solution to that was over 12,000 lines of code in a custom .NET application for approvals and authority. Some of these workflows require 7 or more levels of approvals, and any changes to personnel or approval logic requires a major rewrite of the code, which they currently spend over 30 days a year doing. Approvers only have one desktop interface they can use to run these approvals. Obviously not scalable, and a terrible user experience! 

![relative](images/approvalsmatrix.png)
> The sample that we will work with, but it usually gets a lot more complicated than this ☠️

Thankfully, we have the solution for this, so let's start by showing how you can run these approval matrices on top of the Sales Order documents from SAP. 
<br><br>
For easy reference, this is how the Standard Order (Sales Order) document looks like in SAP ECC. You navigate to this module by entering the T code *VA01*. The T Code (Transaction code) is a 4 digit shortcut key to access a requested transaction, sort of like carrying out an *incident.list* command in the ServiceNow UI.

![relative](images/sapstandardorder.png)

## Let's start

1. Under **All**, search and navigate to **App Engine Studio**

    ![relative](images/aesnavigate.png)

1. Click **Create app** on the top right of the screen

    ![relative](images/createapp.png)

1. On the Create App page, name the app "Delegation of Authority", and for description, enter "Create an approval matrix for various SAP documents"

    ![relative](images/appname.png)

1. Click **Continue**

1. Leave the default roles - *admin* and *user*, and click **Continue**

1. Click **Go to app dashboard**

## Create a Sales Order table

We will now create a table to store Sales Order document data from SAP.

1. Under **Data**, click **Add**

    ![relative](images/adddata.png)

1. Click **Create a table**

1. Click **Get started**

1. On the *Add Data* page, click **Create from an existing table**

1. On the next page, search and select **Task**

    ![relative](images/selecttask.png)

1. Click **Continue**

1. For Table label, enter **SAP Sales Order**. Table name should be auto-populated.

1. Check **Auto number**

1. For Prefix, enter **SAPSO**

    ![relative](images/tabledetails.png)

1. Click **Continue**

1. Allow all access for *admin* and *user*, then click **Continue**

    ![relative](images/tabacl.png)

1. Click **Edit table**

1. If presented with the **GETTING STARTED** pop-up, close it.

1. You should now be on tha *Table Builder* interface. Click **Add new field**, and add the following fields:

    Column label | Type
    -------------- | --------------
    Document number | String
    Document type | String
    Amount | Decimal 
    Sales organization | String 
    Status | Choice (Dropdown with --None--) : New, Approval triggered, Approved, Rejected, Updated SAP

1. Your screen should look like this

    ![relative](images/addedfields.png)

1. Click **Save**

1. Click **Form views** on the top of the screen

    ![relative](images/formviewsselect.png)

1. Click the **Default view** dropdown

1. Click **+ Create**

1. Enter **Sales order view**

    ![relative](images/soview.png)

1. Click **Create**

1. Rename the section to **Sales Order** by clicking on the section and editing the **Section label** on the right panel

    ![relative](images/renamesalesorder.png)

1. Drag the following fields onto the form:
    1. Number
    1. Status
    1. Document number
    1. Document type
    1. Amount
    1. Sales organization

    ![relative](images/formedit.gif)

1. Your completed view should look like this

    ![relative](images/completedsoview.png)

1. Click **Save**

Great, you now have a simple table to store Sales Order data via the SAP integration that we will be configuring next.

## Get Sales Order data via the SAP ERP Spoke

1. Go back to the app home page 

1. Under *Logic and Automation*, click **Add**

1. In the *What do you want to add* screen, click **Flow**

    ![relative](images/addflow.png)

1. Click **Build from scratch**

1. Under **Name**, enter "Get Sales Orders from SAP every 2 hours"

1. Click **Continue**

1. Click **Edit this flow** once you see the *Success! Your flow is ready.* screen

1. Close the pop-up box

1. Click **Add trigger**

1. Under the *Date* section, click **Repeat**

1. Change the repeat duration to 2 hours

    ![relative](images/2hourtrigger.gif)

1. Click **Add an Action, Flow Logic, or Subflow**

1. Click **Action**, and you should see a dropdown appear

1. In the *Search* bar, enter **SAP**

    ![relative](images/sapsearch.png)

> You should be able to see a few different SAP spokes show up. There are 2 different out of the box integration methods to both SAP ECC (Older version) and SAP S/4 HANA. These integration spokes are available in the Enterprise package of Automation Engine. 
<br> <br>
Let's break down the available spokes and when to use them: 
<br><br>
**SAP ECC**: Most of the companies using SAP are running on SAP ECC. It is the offering for a large enterprise. Companies with large volume, complex business processes and operating in multiple geographies go for SAP ECC. It is built on ABAP stack. 
<br><br>
**SAP S/4 HANA**: This is the latest release of SAP ERP and it can run only on HANA database. With this release, SAP has simplified their core database architecture. This together with in-memory processing enables business to do complex business computation faster than ECC.
<br><br>
**Integration via IDoc**: IDOCs or intermediary documents are another way to exchange information to and from SAP. If you are more aware of web technologies, consider IDOCs as XML. It consists of neatly defined data segments with parent and child nodes. There are specific steps to configure inbound and outbound IDOCs.
<br><br>
**Integration via RFC**: If you are looking at a real time SAP system integration scenario, RFC is probably the best way to go. In this case, certain functions are enabled for remote call. One such function could be, for example, sales order creation. Third party applications can integrate with SAP using these RFCs for a real time communication and business process validation (example price computation, minimum order check etc.).

15. Click **SAP ERP**, for this lab we will be using a custom spoke which simulates integration to SAP. This spoke includes only a few of the actions available in the OOTB spokes.

1. Click **Look up Sales Orders**

    ![relative](images/lookupspokeselect.png)

1. Have a look at the action that shows on the screen. These are a subset of the filter options available in the OOTB spokes to retrieve a list of Sales Orders. In this exercise, we will only return one Sales Order record for easier accesibility.

    ![relative](images/lookupsalesorderaction.png)

1. Notice how the **Delivery Block** field is populated with **ServiceNow approval**. Delivery block when applied, blocks the sales order for delivery i.e., delivery cannot be created until the delivery block is removed by the authorized person. This list can be customised in SAP, and the logic would be that as Sales Orders are created in SAP, this Delivery Block status is automatically applied until the approval is completed in ServiceNow.

1. Leave everything as it is, and click **Done**

1. Click **Add an Action, Flow Logic, or Subflow** and add another action

1. Under **ServiceNow Core**, click **Create Record**

    ![relative](images/createrecord.png)

1. On the table field, search and select **SAP Sales Order** (the custom table you created)

1. Click **Add field value**, select **Document number**

1. Drag the **Document number** data pill into the input field

1. Repeat the above 2 steps for **Amount, Document type** and **Sales organization**

    ![relative](images/createrecordfields.gif)

1. Add one final field **Status** and set the value to **New**

1. Your entire action should look like this

    ![relative](images/completedaction.png)

1. Click **Save** on the top right of the screen.

1. Click **Test**

1. Click **Run Test**

1. Click **Your test has finished running. View the flow execution details.**

    ![relative](images/testresult.png)

1. In the new tab that opens, click the **Create Record** action

1. Click the Record, if you followed the guide so far, the record should be **SAPSO0001001**

    ![relative](images/execution1.png)

1. In the pop-up box, click **Open Record**, it should open in a new browser tab.

1. Click on the Hamburger icon on the top left, and under **View**, select **Sales order view**

1. Ensure that all the fields are populated. (Your output will be different)

    ![relative](images/previewrecord1.png)

1. Click on the Hamburger icon again

1. Under **Configure**, click **Related Lists**

1. Search for **Approvers**, and shift that into the **Selected** column

    ![relative](images/addapproversrelateditem.png)

1. Click **Save**

Congrats! You have built the table to store Sales Order data from SAP. Any approvals that you trigger later can also be seen on this view. Go back to the previous browser tab, but don't close anything yet.

## Building the approval matrix using decision builder

For this part of the exercise, we will rebuild this following approval matrix

![relative](images/approvalsmatrix.png)

1. Go back to the **App Home** tab

1. Under *Logic and automation*, click **Add**

1. Click **Decision**

    ![relative](images/selectdecision.png)

1. Click **Get started**

1. Under Name, enter **Sales Order Approval Matrix**

    ![relative](images/namedecision.png)
    > If you face an issue with the Continue button not turning blue, you might have to close this screen and click Decision again

6. Click **Continue**

1. Click **Edit decision table**

1. Click **Add an input**

1. Under label, enter **Sales order record**

1. Under type, select **Reference**, and under Table, search and select **SAP Sales Order**

    ![relative](images/inputso.png)

1. Click **Save** on the top right

1. Click **Add result column**

    ![relative](images/approvalgroup.gif)

1. Under Result column label, enter **Approval group**

1. Click **Result type**, then select **Reference**

1. Under **Result table**, search **Group** and select **sys_user_group**

1. Click **Done**

1. Under the **Approval group** column, add these selections as per our matrix at the start of this section: Finance managers, Finance controllers, CFO, CFO (Ensure that CFO is created twice as we have two different scenarios that require CFO approval)

    ![relative](images/selectgroups.gif)
    
1. Now we have the results, we need to add the conditions.

1. Click **Add condition column**

1. In the pop-up, select the fields as per the image below

    ![relative](images/conditionsotype.png)

1. Click **Done**

1. Click on the **Plus sign** in between the 2 columns

    ![relative](images/plussign.png)

1. Click **Add condition column**

1. Fill in the pop-up modal as per the image below

    ![relative](images/salesordamountcond.png)

1. Click **Done**

1. Click in the first cell under **Sales order amount**, select **less than**, and Value **5000**

    ![relative](images/lessthan5k.png)

1. In the corresponding cell for **Sales order type**, select **is**, and Value **Standard sales order**

    ![relative](images/standardsoselect.png)

1. See if you can fill in the rest according to the matrix below (You have just completed the first row)

    ![relative](images/approvalsmatrix.png)

29. Your screen should look like the following once complete

    ![relative](images/completedecision.png)

30. Click **Save** on the top right

## Using approval decision matrix in a flow

1. Navigate back to **App Home**

1. Under *Logic and automation*, click **Add**

1. Click **Flow**

1. Click **Build from scratch**

1. Under **Name**, type **Sales Order Approval Flow**, then click **Continue**

1. Click **Edit this flow**

1. Click on **Add a trigger**, and under *Record*, select **Created**

1. Under *Table*, search and select **SAP Sales Order**

    ![relative](images/socreatedtrigger.png)

1. Click **Done**

1. Click **Add an Action, Flow Logic, or Subflow**

1. Click **Action**, then select **Update Record** under **ServiceNow Core**

    ![relative](images/updaterecordaction.png)

1. Drag the **SAP Sales Order Record** from **Trigger - Record Created** within the data column on the right, onto the **Record** field

1. Click **Add field value**

1. Set field to **Status** and select **Approval triggered**

    ![relative](images/approvaltriggered.png)

1. Click **Done**

1. Click **Add an Action, Flow Logic, or Subflow**

1. Click **Flow Logic**, then **Make a decision**

1. Under **Decision Label**, type **SO Approval Matrix**

1. Under **Decision Table**, search for the decision table we just created, **Sales Order Approval Matrix**

1. Drag in the **SAP Sales Order Record** data pill from **Trigger - Record Created** within the data column on the right, onto **Sales order record**

    ![relative](images/soapprovaldecisionflow.gif)

1. Click **Done**

1. You should now see three branches appear for each *Group* we selected as approvers earlier

    > **IMPORTANT** For this lab, we will not spend time building out the complete approval workflow for each branch. The idea here is that you now have the ability to cater to any specific workflow approval pattern using the ServiceNow Core *Ask for Approval* action.

23. Click on **+** under the first branch

1. Click **Action**

1. Search for **Ask For Approval**, then select the action

    ![relative](images/askforapproval.png)

1. Drag **SAP Sales Order Record** from **Trigger - Record Created** within the data column on the right, onto the **Record** field

1. Since we extended the **Task** table, the **Approval Field** and **Journal Field** should be automatically populated

1. Under rules, change **-Choose approval rule** to **Anyone approves** (We are not factoring an Rejection flow here)

1. Drag over **Approval group** reference from **2 - Make a decision > Result Record** onto the empty field

    ![relative](images/approvalgroup.png)

1. Click **Done**

1. On the **Ask For Approval** action you just added, click on the **Duplicate** icon twice

1. Drag each of the 2 duplicated actions under each of the other branches

    ![relative](images/dupapproval.gif)

1. Outside of the Decision flow, click **Add an Action, Flow Logic, or Subflow**

1. Click **Action**

1. Search for **SAP ERP**, then select **Update Sales Order**

    ![relative](images/updatesoselect.png)

20. Under the **Sales Document** field, drag the **Document number** pill from **Trigger - Record Created > SAP Sales Order Record** within the data column on the right, onto the field

21. Change the **Delivery Block** field to **None**

    ![relative](images/addupdateso.png)

1. Click **Done**

> For reference, this action will remove the delivery block as you see in the image from SAP ECC below, which will allow the order to move to the next stage, which is generally creating a delivery document. 
![relative](images/removedeliveryblock.png)

39. Under the *Update Sales Order* action, add a new **Action**

1. Select **Update Record** under **ServiceNow Core**

1. Drag the **SAP Sales Order Record** pill from **Trigger - Record Created** within the data column on the right, onto the **Record** field

1. Add a field, select **Status** and select **Updated SAP**

    ![relative](images/updatedsap.png)

1. Click **Done**

1. Your entire flow should now look like this

    ![relative](images/completedflow.png)

1. Click **Activate** on the top right

1. Click **Test**, and select the record that was created via the first flow we created, it should be **SAPSO0001001**

    ![relative](images/testdecisionflow.png)

1. Click **Your test has finished running. View the flow execution details**

1. Check the execution flow, did it match our Approval Matrix? Your results will vary based on your record and should now be in Waiting for approval state after identifying the right approval group

    ![relative](images/testdecision.png)

1. If it did not, examine your Decision Builder table again to see if there was anything you missed.

1. Open up the Sales Order record tab (SAPSO0001001)

1. **Refresh** the page

1. Confirm that the **Status** has changed to **Approval triggered**

1. Right click on any **Requested** approval records, and click **Approve**

    ![relative](images/approveso.png)

1. Return to the flow execution screen and click the **Refresh** icon

    ![relative](images/refresh.png)

1. Your flow should now be completed and the record updated

As you can imagine, now that we have these 2 workflows designed, one will essentially trigger off the other: Our custom app will pull Sales Orders from SAP every 2 hours, and for any new Sales Orders picked up, it will be processed via our Decision Table, before routing to the right parties for Approval. Only after approval, will we then update the same Sales Order back in SAP (to remove the delivery block).

Well done! You've come to the end of Exercise 1, where you've built a very powerful approval matrix alternative that sits on top of any SAP document to drive the Delegation of Authority process. We've gone from 12,000 lines of code to 0 and modifying this workflow to include new decisions will take minutes, not days.

Of course, this is only the very start, and you can start to include platform capabilties such as SLAs, Approval Delegation periods, Conversational integrations, Mobile Approvals and more that makes the entire process seamless.