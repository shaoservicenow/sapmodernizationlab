# Introduction

## Goal

In this lab, you will learn how ServiceNow Creator Workflows can help you to modernize your ERP processes with the tools and functionalities available on the ServiceNow platform.

## Introduction

A business process refers to a set of activities that have to be performed to complete an end-to-end business scenario. There are multiple business processes in ERPs, some common ones are listed below:

Name | What is it?
------------ | -------------
Order to Cash | Order to cash (OTC or O2C) is a set of business processes that involve receiving and fulfilling customer requests
Procure to Pay | Procure to pay (PTP or P2P) is the process of requisitioning, purchasing, receiving, paying for, and accounting for goods and services, covering the entire process from point of order right through to payment.
Hire to Retire | Hire to retire (H2R) is a human resources process that includes everything that needs to be done over the course of an employee's career with a company.
Plan to Produce | Plan-to-Produce (Pl2P) encompasses an end-to-end perspective on producing goods. It depicts the shop-floor procedures and focuses on the integration of surrounding aspects into production processes.
Plan to Inventory | Plan to Inventory (P2I) the process of determining the optimal quantity and timing of inventory for the purpose of aligning it with sales and production capacity.
Quote to Cash | Quote-to-cash (QTC) process encompasses many sales, account management, order fulfillment, billing, and accounts receivables functions.
Record to Report | Record to report (R2R) is a finance and accounting management process that involves collecting, processing and presenting accurate financial data.

Often times, there are multiple exceptions outside of an expected business process flow (at times refered to as a "Happy Flow") that organizations need to account for - either by customizing their ERP systems or relying on manual processes that sit outside of the ERP systems. 

Then, there is also the issue of data availability. There is a learning curve to navigating ERP systems due to the complexity of the business objects, everyday business users might not have access to the right environment and training to use these ERP systems, and need a friendlier way to interact with the data.

ServiceNow Creator Workflows solves these challenges with it's low code capabilities paired with easy to configure integrations to various ERP systems. We will focus on SAP in this lab.

## Getting started

To follow the steps in this lab guide, you need a San Diego instance with Virtual Agent (VA) and NLU plugins installed and activated.

***If you are doing this lab as part of CreatorCon, all the necessary plugins are already activated on your instance
and you can skip the following steps and jump to Exercise 1.***

## Activate the Necessary Plugins

There are a few plugins required on your instance to complete this lab. The main plugin you will need to activate is *Glide Virtual Agent*. It requires you to have the **admin** role. Once activated a number of supporting plugins will also be activated.

1. Search for and select **Plugins** in the left hand navigation panel.

    ![relative](images/pluginsearch.png)

1. You may be presented with a blue prompt that says *You have been redirected to All Applications. To see the Plugins list click here.* Click **X** to close this message.

    ![relative](images/bluemessageplugin.png)

1. Use the Search Bar at the top of the screen to search for **Glide Virtual Agent**.

1. Locate the plugin and click the **Install** button to activate the plugin.

    ![relative](images/installglideva.png)

1. The system also lets you know about the other plugins that are to be installed. Click the **Activate** button. (It might take up to 10 minutes to activate)

    ![relative](images/activateva.png)

1. After the plugin activation has completed you'll see a Success notification. Click **Close & Reload Form**.

    ![relative](images/pluginsuccess.png)

# Exercise 1: Set up and create your first topic using keywords

## Goal

In lab CCL1054-K22, we built a Student Permission application for parents to grant permission for students to participate in activities.

In this exercise, we will be working within the custom application scope *Student Permission*, so we will first select this so all our work will be saved against this custom scope.

1. On the top navigation bar, click the **Globe** icon towards the right.

1. Click **Application scope: Global** under the drop down.

1. In the list that appears, select **Student Permission**.

    ![relative](images/studentpermscope.gif)

    The system automatically refreshes the screen.

We can now start building a Virtual Agent topic that allows parents to withdraw students from the registered activities by changing the state of the Permission Task from *Approved* to *Withdrawn*.

1. From the top menu, navigate to **All > Conversational Interfaces > Virtual Agent > Designer**.

    ![relative](images/menu-vadesigner.png)

1. Click **Create**.

    ![relative](images/createtopic.png)

1. In the **Topic Properties** page, follow the list below to fill in the necessary fields.

    Field | Value
    ------------ | -------------
    Name | Withdraw from activity (1)
    Description | Withdraw a student from a registered activity (2)
    Type | Topic (3)
    Resume originating topic flow | **Toggle On** (4)
    Keywords | You will notice that *Withdraw from activity* is already there when we typed in the name, now type in more key words/phrases that's related to the topic by typing in the word and hitting **tab**: *cancel, deregister, remove, drawout* (5)

    When you're done, your screen should look like this:

    ![relative](images/topicprops.png)

1. Click **Create** on the top right. (6)

1. On the topic flow designer page, take a minute to familiarize yourself with the options on the page.

    Our first objective is to let the user choose the activity to withdraw from.

1. Drag **Reference Choice** (blue) from the left **User Input** area and drop it in between **Start** and **End**.

    ![relative](images/referencechoice.gif)

1. Click the **Reference Choice** that you dragged in.

1. Now you will notice that there are *5 issues* showing up on the screen, we need to fill in the *user input property* on the right hand side.

1. Set the fields as follows:

    Field | Value
    ------------ | -------------
    Name | Choose an activity
    Variable Name | (autofilled) choose_an_activity
    Prompt | Which activity would you like to withdraw from?
    Acknowledge message | Here are the details of this activity:

1. Scroll down and set the fields as follows:

    Field | Value
    -------------- | --------------
    Reference Type |   Record
    Table | Permission Task \[x_snc_student_pe_0_permission_task\]
    No records response message |   No activities found for student.

1. Choice Value Expression: *Condition Builder* and click **Add condition**.

    The system presents a pop up titled **Assign Condition**. Add *two* conditions as follows:

    Click the **Data Pill Picker** ![relative](images/datapillpicker.png)

    **\[Student Parent\] \[is\] \[Input variables-\> User\]**
 
1. Click the arrow near the **Input Variables**, and click **User**.

1. Click **and** on the right that will create another condition and enter the following:

    **\[State\] \[is\] \[Approved\]**

    ![relative](images/referencechoiecondition.gif)

    This retrieves the activities that the parent has approved for the student.

1. Click **Save**.

1. Your **User Input Properties** should look like this once completed:

    ![relative](images/refchoiceuserinputcomplete.png)

1. Click **Save** on the top right of the screen.

1. Preview your work by clicking the **Test** button on the top right corner of your screen. The system presents a pop-up window.

1. You should see your registered activities appear for selection.

    ![relative](images/vatest1.png)

    > IMPORTANT: If a new Virtual Agent window doesn't launch
    check your pop up blocker.

1. You can select one of the *Permission Task records*, but note that nothing happens as we have not defined what to do with the chosen record.

Congratulations you've finished the first step, and now you can close this window.

## Look up records

Our next task is to look up the chosen activity details based on the *Permission task* selected by the user so we can use this across the rest of the conversation.

1. Use the same drag and drop method to add **Lookup** (which is under *Utilities*, in grey) between **Choose an activity** and **End**.

    ![relative](images/lookupdrop.gif)

1. On the right side of your screen, fill out the **Prompt properties** as follows:

    Field | Value
    -------------- | --------------
    Name |   Look up selected activity
    Table | Activity \[x_snc_student_pe_0_activity]

1. Under **Script**: Select the **Condition** option and click **Add condition**.

    ![relative](images/addcondition.png)

1. A popup appears titled **Assign Condition**. Add a condition:

    **[Actitvity] \[is\] \[Input variables-\> Choose An Activity-\> Activity-\> Activity\]**

    > Hint: You should see 4 panels altogether, refer to the walkthrough below:

    ![relative](images/activitymatch.gif)

    This retrieves the *activity record* based on what the user has selected in the first step.

1. Click **Save**.

## Display details

Next, we will display the details of the *Activity record* we just looked up.

1. Drag and drop **Card** (*which is under Bot Response, in Orange*) between **Look up selected activity** and **End**.

    ![relative](images/dropcard.gif)

1. On the right side of your screen, fill out the *Response properties* as follows:

    Field | Value
    ----- | --------------
    Name |   Show activity details
    Card type | Record
    Record | Look Up Selected Activity
    Fields | Use the **Add Field** to insert more lines

    * **Activity**

    * **Start time**

    * **End time**

    * **Teacher**

1. Click **Save** on the top right corner.

    Your screen should look like this:

    ![relative](images/completecard.png)

1. Preview it again by clicking the **Test** button on the top right corner. You will have the *Virtual Agent* pop up, and you can choose any *Permission Task*. Your screen should look similar to this one:

    ![relative](images/vatest2.png)

1. Close the pop up *Virtual Agent* window.

Congratulations, if your conversation works, close this window and move to the next steps.

## Prompt the user

We will now ask the user if they would like to withdraw from the activity.

1. Drag and drop **Boolean** to the next step.
 ![relative](images/boolean.gif)

1. The system displays *2 issues* on the screen. Fill the **user input property** on the right hand side as follows:

    Field | Value
    -------------- | --------------
    Name |   Confirm withdrawal
    Variable Name | (autofilled) confirm_withdrawal
    Prompt | Would you like to withdraw **\[Input variables-\> Choose An Activity-\> Student-\> Name\]** from this activity?

    Here is a walkthrough of how to complete the **Prompt** field above:

    ![relative](images/buildprompt.gif)

1. Click **Save** on the top of the page.

1. Your flow should look like this:

    ![relative](images/uptoconfirm.png)

1. In order for the user to make a *Yes/No* decision, let's drag and drop **Decision** from **Utilities** to the next step. To find **Decision**, scroll down on the left hand side of the designer page.

    ![relative](images/dropdecision.gif)

1. Rename **Always** by clicking it, and go to the right hand side **Prompt properties** to change **Always** to **Yes**.

    ![relative](images/yesdecision.gif)

1. Click **Add Condition**, and change the condition to the following:

    **[Confirm Withdrawal][is][Checked]**

    ![relative](images/confirmwithdrawalcheck.png)

    Click **Save**.

1. Drag and drop a **User Input Text** on **Yes**.

    ![relative](images/reasontextdrop.gif)

1. On the right, set the response properties to the following:
    Field | Value
    -------------- | --------------
    Name | Reason for withdrawal
    Prompt | Why are you withdrawing student from this activity?
    Acknowledge message | Ok, we will proceed to withdraw student from the activity.

     **Optional**: To give more context, it is a good idea to include the student's name and activity name. We did this earlier, and we can include this again here for **Prompt** and **Acknowledge message** as follows:

    Field | Value
    -------------- | --------------
     Name | Reason for withdrawal
    Prompt | Why are you withdrawing **\[Input variables-\> Choose An Activity-\> Student-\> Name\]** from this activity?
  
    ![relative](images/inputprompt.png)
    Field | Value
    -------------- | --------------
    Acknowledge message | Ok, we will proceed to withdraw **\[Input variables-\> Choose An Activity-\> Student-\> Name\]** from **\[Input variables-\> Choose An Activity-\> Activity-\> Activity\]**.

    ![relative](images/acknowledgemessage.png)

1. Drag and drop **Record Action** under **Utilities** between **Reason for withdrawal** and **End**.

    ![relative](images/recordactiondrop.gif)

1. Set the **Prompt properties** as follows:

    Field | Value
    -------------- | --------------
    Name |   Update Permission Task to withdrawn
    Action type | Update Record
    Record  |   Choose An Activity

    Click **Add Field**, and in the **Field Values** pop-up window, add the following field values:

    * State: **Withdrawn**

    * Work notes: **Input variables-\> Reason For Withdrawal**

    ![relative](images/updatepermtask.gif)

    Click **Save**.

1. The **Prompt Properties** panel should look like this:

    ![relative](images/updaterecordpromptproperties.png)

1. Click on the Blue **+** button under the **Decision** button. Another branch appears.

    ![relative](images/splitblue.gif)

1. Rename **Always** on the left by clicking on it. In the **Prompt properties**, change **Always** to **No**.

1. Click **Add condition**, and set the condition to the following:

    **\[Confirm Withdrawal\] \[is\] \[Unchecked\]**

    ![relative](images/nowithdrawal.png)

1. Click **Save**.

1. Click the **Fit Content to Window** button on the top right of the diagram to see the topic in it's entirety. Drag the
*arrow* below **No** to connect to the node **Choose an
activity**.

    ![relative](images/nooption.gif)

    > HINT: Click and drag the **arrowhead** and NOT the dot.

    ![relative](images/arrowhead.png)

1. Click **Save** on the top right corner.

1. Now you've finished building the whole topic flow, let's preview what we just built by clicking **Test**.

## Preview

1. In the pop-up conversation, select one of the *assigned activities*.

    ![relative](images/testtop1.png)

1. Select any one of the activities and select **No** when asked if you would like to withdraw from the activity.

    ![relative](images/testtop2.png)

1. Try selecting another activity from the list.

1. This time, select **Yes**, and then enter a reason for withdrawal.

    ![relative](images/testtop3.png)

1. This will change the state of the permission task from **Approved** to **Withdrawn** and enter the *parent's comments* in the worknotes. If you want to verify these changes, **DO NOT close the window**.

1. Congratulations! You've successfully created a topic in Virtual Agent.

## Validation

1. Scroll up in the conversation.

1. Under the *Activity card*, click the **activity name** on the top right of the card.

    ![relative](images/testveri1.png)

    A new browser tab opens showing the **Activity** record (this is opening because we looked up the *activity record* earlier in the exercise).

1. Click **Permission Tasks** under **Related Links**.

    ![relative](images/testveri2.png)

    The *Permission Tasks* page is displayed.

1. Click the **Permission Task** on the left to open the **Permission Task** record.

1. Confirm that the state is **Withdrawn**, and the reason for withdrawal is populated in the **Activity**.

    ![relative](images/topveri3.png)

1. Close this tab once you have validated the record.

# Exercise 2: Build your NLU Model

## Goal

In this exercise, you will use the NLU Workbench to create, train and test a *NLU model* to be used in the topic created in the previous exercise. The NLU model is built to help the system understand human-expressed intent regarding user requests for access to systems, data, roles, equipment, and other entities. You will use this model to help the system understand the same use case as before (a parent requesting a student withdrawal from an activity).

1. Navigate to **All > NLU Workbench > Models**.

    ![relative](images/nlunav.png)

1. The system displays the *NLU Workbench* page.

1. Close any pop-ups that appear on the screen.

1. Click **Start from blank** on the top right on the screen to create a new model.

    ![relative](images/nluworkbench.png)

1. In the next screen, define the fields as follows:

    Field | Value
    -------------- | --------------
    Name | Student activities
    Short description | Manage different aspects of student activities
    What is the primary language? (autofilled) | English -en 
    What are you creating this for? (autofilled) | Virtual Agent
    What business area is this for? (optional) | *--Not specified--*

    ![relative](images/modeldetails.png)

1. Click **Next**.

1. Click **View Model**.

1. In the pop-up window, Click **Add content**.

    ![relative](images/addcontent.png)

1. Click **New intent**.

    ![relative](images/newintent.png)

1. Define the intent properties as following:

    Field | Value
    -------------- | --------------
    Intent Name | Withdraw from activity
    Description| Withdraw a student from approved activity

    ![relative](images/intentname.png)

1. Click **Add intent**.

1. Provide some *utterance examples*.

    * withdraw from activity

    * cancel student permission

    * deregister student from event

    * student unable to attend activity

    * remove after school program

    For the purpose of this exercise, we are only going to have 5 utterances, but you can always add more to improve accuracy.

    ![relative](images/withdrawintent.png)

    Next, we'll create another intent for this model.

1. Click **Manage your model content** on the top *breadcrumb navigator*.

    ![relative](images/breadcrumb.png)

1. Confirm that **#Withdraw from activity** shows up in the list, then click **New Intent**.

    ![relative](images/2ndintent.png)

1. Define the intent properties as follows:

    Field | Value
    -------------- | --------------
    Intent Name | Approve permission task
    Description| Approve an assigned permission task

    ![relative](images/permtaskdetail.png)

1. Click **Add intent**.

1. Provide 5 utterances:

    * approve permission task

    * grant activity permission

    * view open activities to approve

    * allow student to attend activity

    * give permission for activity

     > A second intent is required as part of a NLU model. However, we will not be using this second intent in our topic for this session.

1. Click **Train model**. (1)

1. In the side bar that appears on the right, click **Train**. (2)

    ![relative](images/approveintent.png)

1. If all above steps are configured correctly, the system displays a message: *The model has been successfully trained*.

    ![relative](images/trainingsuccess.png)

    We can now run a quick test of the model.

1. Click **Try Model**.

1. Enter any phrase related to withdrawing a student from an activity. For example:

         I want to remove student from an activity.

1. Locate **Top Prediction(s)** under the phrase.

1. Verify the *Withdraw from activity* intent is identified with a percentage of confidence.

    ![relative](images/trymodel.gif)

1. (Optional) Try another phrase with the intent of approving a permission task. For example:

        Grant student permission to attend an activity

    The **Top Prediction(s)** should identify as *Approve permission task*.

    ![relative](images/grantpermtry.png)

Congratulations! You've created a NLU model. We will now use this in the *Withdraw from activity* topic we created in Exercise 1.

# Exercise 3: Implement the NLU model and design the chatbot

## Goals

* Use *Conversational Interfaces* to change some visual elements of the Virtual Agent interface.

* Activate NLU to make it available in Virtual Agent topics for intent discovery, before we apply the NLU model we created in the previous exercise on our custom Virtual Agent topic.

In the first part of the exercise, we will be making some changes to how the Virtual Agent appears in the *Employee Center* Portal. To do this we need to first change to the *Employee Center Core* scope.

1. On the top navigation bar, click the **Globe** icon towards the right.

1. Click **Application scope: Student Permission** under the drop down.

1. In the list that appears, select **Employee Center Core**.

    ![relative](images/globalscope.gif)

    The system automatically refreshes the screen.

## Applying Virtual Agent brand elements

1. Navigate to **All > Conversational Interfaces > Home**.

    ![relative](images/conversationalhome.png)

    The system opens a new browser tab for the *Conversational Interfaces Home*. All the settings and configurations you need to set up and launch your interfaces, such as *Virtual Agent* and *Agent Chat*, are found here.

1. Spend a few moments to get acquainted with the page, then click the **Chat Settings** tab on the top of the page.

    ![relative](images/chatsettings.png)

1. On the *Branding* block, Click **View all**.

    ![relative](images/brandingselect.png)

1. In the *Branding* list, click **EC Branding**.

1. Change **Chat Header** to **Parent Support**.

1. Save the following image to your local drive. (Right-click -> Save Image As...)

    ![relative](images/va-avatar.png)

1. Under *Chat Header Logo*, Click the **Trash Bin icon**, and in the **Delete image** pop-up, select **Delete**.

    ![relative](images/deleteheaderlogo.png)

1. Click **Attach image**, then upload the file you just downloaded.

1. Click **Save** on the top right, and your screen should look like the following. If it does not, refresh the browser window.

    ![relative](images/ecbrandingfin.png)

## Turning on NLU

1. On the left navigation bar, select **Virtual Agent**. The screen should now display **Virtual Agent Settings**. (1)

    Notice how under the *Natural Language Understanding (NLU)* block, **Status** is set to **Off**.

1. Click **View settings**. (2)

    ![relative](images/vasettings.png)

1. Under the *NLU Service Provider* dropdown, select **ServiceNow NLU**. (1)

1. Leave the two options below **off**.

1. Toggle the **Activate** switch on the top right of the screen. (2)

    Your screen should now look like the following:

    ![relative](images/nluactivate.png)

1. Click **Save**. (3)

## Applying NLU on Withdraw from activity topic

1. Navigate back to the main *ServiceNow UI* browser tab.

1. Using the same method as before, change the application scope back to **Student Permission**.

    ![relative](images/stupermscope.gif)

1. Navigate to **All > Conversational Interfaces > Virtual Agent > Designer**.

    ![relative](images/menu-vadesigner.png)

1. Scroll down and click the **Withdraw from activity** topic card.

    ![relative](images/openwithdrawactivity.png)

1. Click **Properties** on the top left.

    ![relative](images/vaprop.png)

1. In the section *Set up Natural Language Understanding (NLU)*, set the following in the respective order:

    Field | Value
    -------------- | --------------
    NLU Model |   Student activities (English)
    Associated Intent | Withdraw from activity

    ![relative](images/setupnlu.gif)

    > The NLU model will now be used for topic discovery/identification in place of the keywords that you entered before.

1. Click **Save**.

1. Click **Test**.

    The system displays a checkbox that allows you to *Include topic discovery*.

1. Enter a statement in the chat of your choice with the objective of withdrawing from an activity. For example:

        I would like to withdraw my child from an activity.

    ![relative](images/nlutestphrase.png)

1. Verify that the test phrase has been analyzed and the *Intent* has been invoked.

1. You can continue testing more phrases by clicking the **refresh icon** on the top bar.

    ![relative](images/refreshdiscovery.gif)

1. Close the pop-up window.

    Now that we have verified that the NLU Model works, the next step is to publish it along with the topic.

1. Click the **NLU Intent tab**. (1)

1. Click **Publish Model**. (2)

1. On the right sidebar that now appears, Click **Publish**. (3)

1. In the pop-up, Click **Publish**. (4)

1. Click **Publish** on the top right.

    ![relative](images/publishtopic.png)

1. Confirm that the topic is **Active**.

    ![relative](images/activetrue.png) 

Congratulations! Your topic is now Active and ready to be used in Virtual Agent conversations.

# Exercise 4: Testing the topic in Employee Center

## Goal

In this final exercise, we will test our published Virtual Agent topic in *Employee Center*, and will also review the design changes we made to the Virtual Agent.

The first step is to *impersonate* a Parent.

1. Click the **Profile avatar** on the top right of the screen.

1. Select **Impersonate user** under the list of options.

1. In the pop-up, search for user **Fred Luddy**.

    ![relative](images/impersonatebeth.gif)

1. Navigate to **All > Self Service > Employee Center**.

    The system opens *Employee Center* in a new browser window.

    ![relative](images/openec.png)

1. Click the **Chat bubble** at the bottom right of the screen to open Virtual Agent.

    ![relative](images/ecpage.png)

1. Confirm the visual changes that you made are reflected in the conversation window.

    ![relative](images/startconvo.png)

1. Type in a request related to withdrawing a student from an activity. For example:

         I want to withdraw student from an activity

1. Choose any of the activities to withdraw from, and complete the rest of the conversation.

    ![relative](images/jeanfrench.png)

1. Trigger the topic one final time by clicking on **Click here to start a new conversation**.

1. Ensure that the activity you just withdrew from does not show up on the list any longer.

    > You should only have one Activity Permission Task left to choose from. The message shown would be as follows:

    ![relative](images/finalactivity.png)

Congratulations you have completed the entire lab!

# Bonus Exercise: Enhance NLU accuracy by adding vocabulary

## Goal

Increase the accuracy of the NLU model that we built by using vocabulary to provide synonyms to the keywords in our topic.

1. Click the **Profile avatar** on the top right of the screen.

1. Click **End impersonation**

    ![relative](images/endimpersonation.png)

1. Navigate to **All > NLU Workbench > Models**.

    ![relative](images/nlunav.png)

1. Open your *Student activities* model by clicking **English** next to **Student activities**.

    ![relative](images/addvocab1.png)

1. On the *Student activities* model page, click the **Vocabulary** box title to be brough to the *Vocabulary* screen.

    ![relative](images/clickvocab.png)

1. Click **Add synonym**.

    ![relative](images/addsyno.png)

1. In the **Add Synonym** pop up box, fill in the details as follows:

    Field | Value
    -------------- | --------------
    Type |   Regular
    Vocabulary | Student
    Synonym |  (press *enter* after each synonym to add) *child, ward, pupil, son, daughter, kid*

    Once completed, it should look like this:

    ![relative](images/studentsyno.png)

1. Click **Save**.

    Let's add another synonym for *activity*.

1. Click **Add synonym**.

1. In the **Add Synonym** pop up box, fill in the details as follows:

    Field | Value
    -------------- | --------------
    Type |   Regular
    Vocabulary | Activity
    Synonym |  (press *enter* after each synonym to add) *course, afterschool, program, extra-curricular, event, field trip, visit*

    Once completed, it should look like this:

    ![relative](images/activitysyno.png)

1. Click **Save**.

    Before re-training the model, let's first test an utterance to see the current intent prediction percentage.

1. Click **Try model** on the top right, and in the search field, enter the phrase:

        Withdraw my daughter from upcoming field trip

1. Click **Go**.

    ![relative](images/beforetest.gif)

    > This utternace is predicting the topic correctly, but only at a 68% confidence level.

1. Take note of the percentage value for the intent.

1. Click **Train model**.

    ![relative](images/trainmodeltab.png)

1. Click **Train**.

    Once training has been complete, you should see a success message:

    ![relative](images/trainingsuccessnew.png)

    Repeat the previous steps.

1. Click **Try model** on the top right, and in the search field, enter the phrase:

        Withdraw my daughter from upcoming field trip

1. Click **Go**.

1. Take note of the prediction percentage now, it should be higher than your first try.

    ![relative](images/testutterance2.png)

    > The confidence level is now 77% versus 68% before.

Well done! We have now made the Virtual Agent more accurate in topic identification, giving users and even better experience in getting their requests and queries resolved. Feel free to add even more synonyms to improve accuracy of your NLU model, or try out other phrases.
