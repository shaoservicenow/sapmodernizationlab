---
layout: default
title: Exercise 2 - FCA
nav_order: 3
---

# Exercise 2: Automating Financial Close - The Journal Entry experience

## Introduction

The financial close is a critical process that happens regularly (Monthly, Quarterly, Yearly) in a business with the general goal of producing financial reports representative of the company's true financial position. This is a stressful exercise and there is typically a checklist of activities that the finance team has to undertake, for example: 
- Perform inventory count
- Reoncile account and post Journal Entries
- Update cash flow statements
- Balance petty cash funds, any many more.

Many of these activities also tie back to SAP as the system of record, but these activities are essentially workflows, not just record transactions! These activities have a start, an end and lifecycle events inbetween. Navigating across SAP, updating, checking then reporting becomes a huge headache. In fact, in a 2021 SAPinsider report, 58% of organizations see financial close as the biggest pain point for financial processing. Only 30% of organizations have implemented solutions to automate the SAP financial close process, far below areas such as Account Receivable or Accounts Payable. So can we make this better? Obviously!

In this exercise, let's focus on one of the key activites, which is Journal Entry posting from ServiceNow to SAP.

> A journal entry is used to record a financial transaction in the accounting records of a business. A journal entry is usually recorded in the general ledger, which is then used to create financial statements for the business. The logic behind a journal entry is to record every business transaction in at least two places (known as double entry accounting). <br> In the following diagram, you can se that posting journal entries in the general ledger is among the first tasks in the financial close process. Not only finance users, but sometimes business users as well, have to post hundreds or thousand of manual journal entries in each period! <br>
![relative](images/fcprocess.png)

Since you've already gone through the excercise of creating a new app and tables and forms, the setup of these application files for this exercise is already done for you.

1. Navigate back to *App Engine Studio Home* by clicking on the **Home** icon

1. Under *My recent apps*, look for **Exercise 2: Financial Close ...**. If not found, click on **See all of my apps**, then search for **Exercise 2**

    ![relative](images/openex2.png)

1. Under *Data*, you can see 2 tables added in this application: **Journal Entry Document** and **Journal Entry Lines**

1. First, let's see what a Journal Entry Document looks like in SAP

    ![relative](images/sapje.png)

    >The Journal Entry Document in our scenario refers to the "Document Header", and Journal Entry Lines are row items nested under the Document Header.

1. Now let's see how this works with our two related tables. Similar? You could have built this in App Engine Studio in less than 10 minutes ;)

    ![relative](images/snje.png)

## Building the user experience layer

For this part of the exercise, we will get our hands a little dirtier by building a Record Producer for users to create Journal Entry records. As we now need to enable the user to attach a dynamic number of Journal Entry Lines against a parent Journal Entry Document, there is some scripting needed as part of implementing this feature via a Multi-row Variable Set.

>**What is a Multi-row Variable Set (MVRS)?** <br>
From ServiceNow documentation: Use a multi-row variable set (MRVS) to capture variable data in a grid layout while submitting a catalog item request for a group of entities. For example, for HR during the reorganization of employees, a single record producer should be able to capture the relevant information such as the department and manager for a group of employees

First, let's create the variable set. This time, for this part, we will be working within Studio IDE.

1. On the App Home Screen, click the **Gear** icon
   
    ![relative](images/clickgear.png)

1. Click **Open app in Dev Studio**

    ![relative](images/openindev.png)

1. A new browser tab will open in *Studio IDE*

1. Click **Create Application File** on the top left

1. Search **Variable Set**, then click the **Create** button on the bottom right

    ![relative](images/createvariableset.png)

1. Click **Multi-Row Variable Set**

    ![relative](images/selectmvrs.png)

1. On the **Variable Set** form, fill in the Title as **Journal Entry**, then click out of the field. The **Internal name** should be automatically populated as **journal_entry**. Leave it as such.

1. Click **Submit**

    ![relative](images/variablesetform.png)

1. On the left panel, scroll down until you see **Service Catalog > Variable Sets > Journal Entry**

1. Click on **Journal Entry**

1. On the Variable Set, you should now see more related lists below. 

    > **NOTE:** It is important you follow the next few steps accurately, if not there might be some problems in the later part of this lab.

1. Under **Variables**, click **New**

1. On the new **Variable** screen, enter **100** under *Order*

1. Under the **Question** tab, enter **GL Account** under *Question*

    ![relative](images/glaccountvariable.png)

1. Click **Submit**

1. Create 3 more variables under the **Journal Entry** Variable Set (Repeat steps 12 to 14)

    Order | Question | Name (autofilled)
    ------------ | ------------- | -------------
    200 | Cost center | cost_center
    300 | Debit | debit
    400 | Credit | credit

1. Your Variable Set should now look like this

   ![relative](images/completemvrs.png)

> If you cannot see the new Variables, click the related list filter condition to refresh the list.

   ![relative](images/refresh_journal_entry.png)

### Creating a Record Producer

Now that we have the variable set to capture Journal Entry Lines, let's use this in a new Record Producer

> We will do this section in ServiceNow Studio IDE for quicker access, but you could definitely use catalog builder as well.

1. Click **Create Application File**, then search for and create **Record Producer**

1. In the **Record Producer** screen, fill in the form as follows

    Field | Entry
    ------------ | -------------
    Name | Post Journal Entry
    Table name | Journal Entry Document
    Script (Scroll down) | Copy and paste the script below (Overwrite default script)

    ```
    (function() {

	var mrvs = producer.journal_entry;

	var l = mrvs.getRowCount();
	for(var i = 0; i < l; i++) {
		var row = mrvs.getRow(i);
		var cells = row.getCells();

		var journaldocument = new GlideRecord('x_snc_exercise_2_0_journal_entry_line');
		journaldocument.initialize();
		journaldocument.setValue('journal_entry_document', current.getUniqueValue());
		journaldocument.setValue('gl_account', cells[0]);
		journaldocument.setValue('cost_center', cells[1]);
                journaldocument.setValue('debit', cells[2]);
                journaldocument.setValue('credit', cells[3]);
		journaldocument.insert();
	}

    })();
    ```

3. Right click on the form header and click **Save**

    ![relative](images/postjerp.gif)

1. Scroll down to the **Variables** tab and click **New**

1. Fill in the form as follows

    Field | Entry
    ------------ | -------------
    Map to field | **Checked**
    Order | 100
    Field | Company code
    Question | Company code    

    ![relative](images/companycodevar.png)

    > We will only work on this one variable for the sake of time, but you could fill out the rest of the fields if you feel necessary. (*Fiscal year, Posting date*)

1. Click **Submit**

1. Navigate back to the **Post Journal Entry** Record Producer tab

1. Click the **Variable Sets** tab

1. Click **Edit...**

1. On the **Edit Members** screen, move **Journal Entry** to the right column, then click **Save**

    ![relative](images/shiftvs.png)

1. Back on the Record Producer screen, click the **Accessibility** tab, 

1. Next to **Catalog**, click on the **Lock** icon

1. Where it says *Select target record*, search and add **Service Catalog**

1. Under category, search and select **Can We Help You?**

    ![relative](images/addtocatalog.gif)

1. Right click the form header and click **Save**

1. We can now test our Record Producer. Click **Try It**

    ![relative](images/tryit.png)

1. For *Company Code*, enter **1002**

1. Click **Add**

1. On the pop-up modal, enter the following details

    Field | Entry
    ------------ | -------------
    GL Account | 622210
    Cost center | 10001000
    Debit | 8000
    Credit | 0 

    ![relative](images/addrow.png)

1. Click **Add**

1. Click **Add** once again for a new Journal Entry

1. This time, enter these details

    Field | Entry
    ------------ | -------------
    GL Account | **622250**
    Cost center | 10001000
    Debit | 0
    Credit | 8000

1. Click **Add**

1. Your screen should now look like the following

    ![relative](images/filled1002.png)

1. Click **Submit**

1. Your screen should now show the submitted record. Ensure that the *Company code* is captured, and you have the two Journal Entry Lines under the related table

    ![relative](images/submittedrp.png)

Congratulations! You have now built the front end user experience to capture Journal Entries, and this record producer can be added to any catalog on any portal. It can even be used on the mobile app. Obviously, this is only one approach, and in reality, some of our customers still prefer to have everything entered via an excel template, which is uploaded against the record producer/catalog item before being processed through an import set.

I personally prefer the MVRS approach as there is little room for error. You can also build validation logic in directly via a client script or business rule.

## Integrating to SAP

Now that we can capture this data, let's close off the process by building the integration to SAP

1. Navigate back to the App Engine Studio interface, if you have closed it before, open the **Exercise 2: Financial Close Automation** app

1. Under *Logic and automation*, click **Add**

1. Click **Flow**

1. Click **Build from scratch**

1. Under *Name*, enter **Post Journal Entry to SAP**

1. Click **Continue**

1. Click **Edit this flow**

1. Click **Add a trigger**

1. Under **Record**, select **Created**

1. Under **Table**, search and select **Journal Entry Document**

    ![relative](images/jetriggercreated.png)

1. Click **Done**

1. Under **Actions**, click **Add an Action, Flow Logic, or Subflow**

1. Click **Action**

1. Search for **sap erp**, then select **Post Journal Entry**

    ![relative](images/selectpostje.png)

1. Map the data pills for each of **1. Company code, 2. Fiscal year** and **3. Posting date** from the **Trigger - Record Created** step Record to the corresponding fields in the action

    ![relative](images/mappillspostje.png)

1. However, notice that for the **Journal Entry Lines** field, it only accepts **array.object** type pills. Recall how we structured our table earlier, there are multiple Journal Entry Lines against one Journal Entry Document. What we need to do here is get all the Journal Entry Lines linked to this trigger record.

1. Fortunately, the action to convert a list of records into an array object has already been configured for you.

1. Click **Add an Action, Flow Logic, or Subflow**

1. Click **Action**

1. Search **Get Journal Entry Lines**, and select the one action under the Exercise 2 spoke

1. On the right of the action, open the Action by clicking on the **Open Action in Action Designer icon**

1. Work through the different action steps here to understand what is being done

    ![relative](images/openaction.gif)

    > This action takes the input of the Journal Entry Document, then finds all related Journal Entry Lines associated with the Journal Entry Document and converts each record to an Array.Object

1. Navigate back to the Flow

1. Drag the **Journal Entry Document Record** data pill onto the **Journal Entry Document** field under *Get Journal Entry Lines*

1. Move this action before the **Post Journal Entry** action so that we have access to the **Journal Entry Lines Array.Object** to pass into the **Post Journal Entry** action

1. Click the **Post Journal Entry** action

1. Drag the **Journal Entry Lines Array.Object** onto **Journal Entry Lines**

    ![relative](images/shiftactions.gif)

1. Click **Done**

1. Click **Add an Action, Flow Logic, or Subflow**

1. Click **Action**, then under **ServiceNow Core**, search and select the **Update Record** action

1. For *Record*, drag in the **Journal Entry Document Record** data pill

1. Click **Add field value**

1. Select **Document number**, and under *2 - Post Journal Entry*, drag in the **Journal Entry Document Number** data pill

    ![relative](images/updatejeaction.gif)

1. Click **Activate** on the top right of the screen

    > **What are we missing here?**<br>
    Once again we are skipping the approvals that has to happen before a Journal Entry actually gets posted into SAP in the interest of time. There are also often many validation rules for different Journal Entry types (Intercompany Posting, Asset Depreciation Posting, etc.) that we are not configuring in this exercise. Here is a common workflow that you will see across this Journal Entry posting process: <br>![relative](images/jeprocess.png) <br> The method used (Excel template) is slightly different but the overall approach is the same.

## Testing the experience and flow

1. Navigate back to the ServiceNow Polaris UI, and search for **Service Portal** under *All*

1. Click **Service Portal Home**, and it will open in a new browser tab

    ![relative](images/serviceportal.png)

1. Under the search bar, enter **post journal entry**, then hit search

1. Click **Post Journal Entry**

    ![relative](images/requestje.png)

1. This was the record producer you created earlier.

1. Enter **1003** for Company code

1. Click **Add**, then enter the following

    Field | Entry
    ------------ | -------------
    GL Account | 622210
    Cost center | 10001000
    Debit | 8000
    Credit | 0 

    ![relative](images/addrow.png)

1. Click **Add**

1. Click **Add** once again for a new Journal Entry

1. This time, enter these details

    Field | Entry
    ------------ | -------------
    GL Account | **622250**
    Cost center | 10001000
    Debit | 0
    Credit | 8000

    ![relative](images/sppostje.png)

1. Click **Submit**

1. Go back to App Engine Studio, with the **Post Journal Entry to SAP** flow open

1. On the top right, click on **more** then click **Executions**

    ![relative](images/clickexecutions.png)

1. You should now see one execution record, click it

    ![relative](images/executionrecord.png)

1. Confirm that the workflow was executed successfully, then click the **Update Record** action to expand it

1. Click the **Record** number, and confirm that the **Document number** field is now populated

1. This is the document number you can use to identify the Journal Entry Document in SAP

    ![relative](images/reviewrecord.png)

1. For your reference, the Journal Entry Document will show up as such in SAP

    ![relative](images/sapje.png)

## Summary

![relative](images/celebrate-happy.gif)

Congratulations on completing this lab! You've built 2 apps that will help Modernize SAP. One for automating approvals on any object from SAP, and another for assisting in the Financial Close process by giving users a better experience when posting Journal Entries. Just these alone will tremendously alleviate a great amount of manual effort that was previously done through SAP + multiple channels.

There are obviously so many more areas Creator Workflows can help, from P2P to OTC. Shifting customizations out of SAP is the number 1 priority for many customers. ServiceNow Creator Workflows allows you to do that all on our industry leading Low-code Application Development Platform.