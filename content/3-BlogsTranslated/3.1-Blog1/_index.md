---
title: "Blog 1"
date: ""
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

AWS DevOps & Developer Productivity Blog  
# Accelerating Development with Secure Access to Amazon Q Developer Using PingIdentity

*by Sid Vantair on 19 JUN 2025 in Amazon Q, AWS IAM Identity Center, Generative AI, Technical How-to — Permalink*

Customers using [Amazon Q Developer](https://aws.amazon.com/q/developer/),  
a programming assistant enhanced by **Generative AI**, often need to authenticate through existing  
Identity Providers (**IdPs**) such as **PingIdentity**. By leveraging  
[AWS IAM Identity Center](https://aws.amazon.com/iam/identity-center/),  
organizations can allow their developers to access **Amazon Q Developer** using their existing  
**PingIdentity** credentials, simplifying the authentication workflow and removing the need for separate logins.

**Amazon Q Developer** can chat about your code, provide inline code completions, and generate new code.  
It also scans your code for security vulnerabilities and performs improvements, including  
language upgrades, debugging, and optimization.

**Amazon Q Developer** comes in two tiers:  
- **Free Tier** – free for personal use  
- **Pro Tier** – enterprise-grade version with advanced access controls, analytics dashboard,  
  customization capabilities, and higher usage limits

Organizations enabling the **Pro Tier** for their developers typically authenticate through  
**AWS IAM Identity Center** due to its ability to federate with external identity providers.

In this article, we will guide you through how to configure **PingIdentity** as an external IdP  
for **IAM Identity Center**, allowing developers to access **Amazon Q Developer**  
using their existing PingIdentity credentials.

<span style="font-weight: bold; font-size: 24px;">Activities</span>

![docflow1](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-1.png)  
**Figure 1 – Solution Overview**

## The business challenge

## The authentication process works as follows:

1. The developer initiates a request to access **Amazon Q Developer**.
2. **IAM Identity Center** checks the authentication status.
3. If the user is not authenticated, they are redirected to the **PingIdentity** login page.
4. The developer enters their **PingIdentity** credentials.
5. **PingIdentity** validates the credentials and returns a **SAML** response.
6. **IAM Identity Center** validates the SAML response.
7. Once validated, access to **Amazon Q Developer** is granted.
8. The developer can now start using **Amazon Q Developer**.

---

## Prerequisites

- An **AWS account**
- A **PingIdentity environment** with users and groups configured for Amazon Q Developer access
- **IAM Identity Center**
- **Amazon Q Developer Pro Tier subscription**

---

## Step-by-step guide

In this section, we will walk through how to create a SAML-based connection between  
**PingIdentity** and **IAM Identity Center**, enabling seamless access to  
**Amazon Q Developer** using PingIdentity credentials.

**Note:** You will need to switch between the PingIdentity admin console and IAM Identity Center  
during this setup. It is recommended to keep each console open in separate tabs.

---

## Step 1: Enable AWS Single Sign-On in PingIdentity

This step enables the AWS Single Sign-On application inside PingIdentity.

1. In the PingIdentity console, navigate to **Applications tab → Application Catalog**.
2. Search for **“AWS Single Sign-On”** and click the **+** icon to start Quick Setup.

![docflow1](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-2.png)  
**Figure 2 – PingIdentity Application Catalog**

*Alt Text:* Screenshot of the PingIdentity Application Catalog.  
The term “aws” is entered in the search bar, showing three results:  
Amazon Web Services – AWS, AWS Gov-Cloud, and AWS Single Sign-On.  
The **"AWS Single Sign-On"** option is highlighted with a red box  
and includes a plus icon to add the application.

---
### 3. Provide the Name, SSO Region, and SSO **Tenant ID**, then select **Next**

- **Name** – Enter an appropriate name for the connection  
- **SSO Region** – Enter the correct AWS region  
- **Tenant ID** – This is the Identity Store ID  

You can run the following CLI command to retrieve this value.  
It is a **10-character alphanumeric string** starting with the prefix **“d-”**:

aws sso-admin list-instances --query 'Instances[0].IdentityStoreId'

Output example: “d-XXXXXXXXXX”

### 4. Navigate to PingOne Mappings and select **Email Address** from the dropdown list.

![docflow1](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-3.png)  
**Figure 3 – AWS Single Sign-On Attribute Mapping**

*Alt Text:* Screenshot of the **AWS Single Sign-On** configuration in **PingIdentity**.  
The screen displays *Step 2* of the setup process, where the **SAML_SUBJECT** attribute  
is mapped to the **PingOne “Email Address”** attribute.  
A red box highlights the mapping section in **PingOne Mappings**.

### 5. Search for and select the group that you previously created  
to grant access to **Amazon Q Developer**, then click the “+” icon to add the group.

### 6. Select **Save**.

![docflow1](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/12/devops-16054-image-4.png)  
**Figure 4 – Selecting PingIdentity Directory Groups for Amazon Q Developer Access**

*Alt Text:* Screenshot of **Step 3** in the **AWS Single Sign-On** setup process  
in **PingIdentity**. The interface displays a group selection screen,  
where the **“Amazon Q”** group is listed. A plus icon (+) appears next to the group  
to add it, and the blue **“Save”** button is highlighted in the bottom-right corner.

---

## Step 2: Connect PingIdentity with IAM Identity Center

This step involves configuring **PingIdentity** using credentials from the  
**AWS IAM Identity Center** to complete the authentication setup.

1. In the **PingIdentity console**, navigate to  
   **Applications Tab → Applications**, and select the application you created in **Step 1**.

2. Select **Enable Advanced Configuration** and click **Enable**.

![docflow1](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-5.png)  
**Figure 5 – Enabling Advanced Configuration for the AWS Single Sign-On Application**

*Alt Text:* Screenshot of the **PingIdentity Applications** console showing  
the selected **AWS Single Sign-On** application.  
The overview displays key configuration sections including **protocol (SAML)**,  
**mapped attributes**, **selected policy**, and **access groups (Amazon Q)**.  
The **“Enable Advanced Configuration”** option is highlighted near the bottom.

---

3. Scroll down and select **Download Metadata**.  
   This saves the metadata file to your computer; it will be used later in the configuration process.

4. In another browser tab, log in to your  
   [AWS IAM Identity Center console](https://console.aws.amazon.com/)  
   and select **Choose your identity source**.

5. In the **Identity source** section, select **Change identity source**  
   from the **Actions** dropdown.

![docflow1](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-6.png)  
**Figure 6 – Changing the Identity Source in the IAM Identity Center Console**

*Alt Text:* Screenshot of the **IAM Identity Center** settings page,  
focusing on the **Identity source** tab.  
The page displays details such as identity source, authentication method,  
AWS access portal URL, issuer URL, and Identity Store ID.  
An expanded **Actions** dropdown in the top-right corner highlights the options  
**“Customize AWS access portal URL”** and **“Change identity source”** in a red box.

6. On the next page, select **External identity provider** and click **Next**.

7. In the **Service provider metadata** section, copy the  
   **IAM Identity Center Assertion Consumer Service (ACS) URL**.

![docflow1](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-7.png)

**Figure 7 – Copying the IAM Identity Center ACS URL**

*Alt Text:* Screenshot of the **“Configure external identity provider”** step  
in the IAM Identity Center setup process.  
The screen shows service provider metadata, including  
**AWS access portal sign-in URL**, **IAM Identity Center ACS URL** (highlighted in a red box),  
and the **IAM Identity Center issuer URL**.  
A **“Download metadata file”** button is displayed in the upper-right corner.

8. Now, return to the **PingIdentity** browser tab, navigate to  
   the **Configuration** tab, and click the **pencil icon** to edit the details.

9. Paste the **ACS URL** you copied from the IAM Identity Center console and click **Save**.

![docflow8](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/12/devops-16054-image-26.png)
**Figure 8 – Configuring SAML Settings for AWS Single Sign-On in PingIdentity**

*Alt Text:* Two screenshots showing the configuration and editing process  
for **SAML** settings in **PingIdentity**.  
The first screenshot displays static configuration details including  
**ACS URL**, **signing key** (“PingOne SSO Certificate for Administrators environment”),  
**signing method** (“Response”), and **signing algorithm**.  
The second screenshot shows the editable interface, with the **ACS URL** field  
highlighted in red and dropdown menus for selecting signing key, signing method  
(Assertion, Response, or both), and the **RSA_SHA256** algorithm.  
These screens guide users through securing a proper SAML setup with AWS SSO.

---

## Step 3: Configure PingIdentity as an External Identity Provider (IdP)  
in IAM Identity Center

This step configures **PingIdentity** as an **external IdP** in  
**IAM Identity Center**, enabling **federated access**.

1. Return to the IAM Identity Center tab opened earlier.

2. Upload the **PingIdentity IdP SAML metadata** file downloaded in  
   **Step 3 of the previous section**, then click **Next**.

![docflow9](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-10.png)  
**Figure 9 – AWS IAM Identity Center Metadata**

*Alt Text:* Screenshot of the **AWS Identity Center** configuration interface  
where the user uploads the **IdP SAML metadata XML** file.  
The metadata file is successfully selected.  
Below it are empty fields for manually entering the **IdP sign-in URL**,  
**IdP issuer URL**, and **IdP certificate**.  
The orange **“Next”** button is highlighted in the bottom-right corner.

3. Review the list of changes. When ready, type **ACCEPT**, then select  
   **Change identity source**.

---

## Step 4: Enable Provisioning and Identity-Aware Sessions  
in IAM Identity Center

This step involves enabling **user provisioning** and  
**identity-aware sessions** in **AWS IAM Identity Center**  
to support dynamic access control.

1. In the  
   [IAM Identity Center Console](https://console.aws.amazon.com/),  
   select **Settings** from the left navigation panel.

2. On the **Settings** page, find and enable **Automatic provisioning**.  
   This immediately activates automatic provisioning in IAM Identity Center  
   and displays the required information:

- **SCIM endpoint**  
- **Access token**

3. In the **Inbound automatic provisioning** dialog, copy each value.  
   You will need these for configuring provisioning in PingIdentity.

4. Select **Close**.

5. Next, enable both **identity-aware sessions** and **automatic provisioning**.

![docflow10](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-11.png)

**Figure 10 – IAM Identity Center Settings for Identity-Aware Sessions and Automatic Provisioning**

*Alt Text:* Two configuration options are shown: **“Enable identity-aware sessions”**  
and **“Automatic provisioning.”**  
Both options display an **Enable** button on the right side highlighted in red.

---

## Step 5: Configure Provisioning Connections in PingIdentity

This step explains how to configure **Provisioning Connections** in PingIdentity  
to enable automatic user and group management.

1. In the **PingIdentity** console, navigate to  
   **Integrations → Provisioning**.

2. Select the **+** icon → **New Connection**.

3. Under **Connection Type**, select **Identity Store**.

![docflow11](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-12.png)

**Figure 11 – Provisioning Connection Setup in PingIdentity**

*Alt Text:* Screenshot of the **Provisioning** configuration in **PingIdentity**.  
The left sidebar highlights the Provisioning tab, and the main panel shows  
the **Create a New Connection** dialog with options for **Identity Store**  
and **Gateway**.  
The Identity Store option is selected, and a plus icon at the top enables  
adding new connections.

4. Select **SCIM outbound** from the list and click **Next**.

5. Enter a name for the connection and click **Next**.

6. Paste the **SCIM endpoint URL** into the **SCIM BASE URL** field.

7. Under **Authentication Method**, select **OAuth 2 Bearer Token**.

8. Paste the **Access Token** into the **OAuth Access Token** field.  
9. Click **Test Connection** to verify the setup, then select **Next**.

![docflow12](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-13.png)

**Figure 12 – Authentication Details Configuration**

*Alt Text:* Screenshot of the PingIdentity interface showing the authentication  
setup fields for **SCIM Base URL**, **SCIM Version (2.0)**, **OAuth 2 Bearer Token**,  
and **OAuth Access Token**.  
The **Test Connection** and **Next** buttons appear below.

---10. Navigate to **User Filter Expression** and change the value to:

userName Eq "%s".

11. Select **Save**. (By default, the connection is created in a **Disabled** state.)

![docflow13](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-14.png)

**Figure 13 – Editing the User Filter Expression for the Connection**

*Alt Text:* The final step of the **Create a New Connection** wizard in PingIdentity,  
displaying the configuration options for **User Filter Expression**, **User Identifier**,  
and group options.  
The **Save** button is highlighted in the bottom-right corner.

---

12. Select the connection you just created and **toggle the switch ON** to activate it.

![docflow14](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-15.png)

**Figure 14 – Activating the Connection**

*Alt Text:* Screenshot of the PingIdentity configuration console showing integration  
with the **IAM Identity Store**, with a highlighted on/off toggle in the upper-right corner  
indicating that the connection is now activated.

---

## Step 6: Configure Provisioning Rules in PingIdentity

This step covers defining provisioning rules in **PingIdentity**  
to determine how users and groups are synchronized.

1. In the **PingIdentity** console, navigate to **Integrations → Provisioning**.  
2. Select the **plus icon (+)** → **New Rule**.

3. Enter a **Name** and **Description** for the rule.

4. Select **Create**.

5. Select the **plus icon (+)** to choose the **Connection** created earlier.

6. Select **Save**.

![docflow15](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/12/devops-16054-image-27.png)

**Figure 15 – Adding the IAM Identity Center Connection to the Rule**

*Alt Text:* Screenshot showing the final steps of linking IAM Identity Center  
to IAM Identity Store using PingIdentity.  
The first image shows the IAM Identity Store connection listed under **Available Connections**  
with a **plus (+)** icon to link it.  
The second image shows the connection selected with **PingOne Directory (P1)** as the source  
and **IAM Identity Store (SCIM)** as the destination, with a highlighted **Save** button.

7. If you want to synchronize users from the PingIdentity directory, create a **User Filter**.  
   To do this, navigate to **User Filter** and select the **pencil icon** to edit.

8. Choose the appropriate filter from the dropdown depending on your use case,  
   then select **Save**.  
   In this example, **Group Name** assigned for **Amazon Q Developer** access is selected.

![docflow16](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-18.png)

**Figure 16 – User Filter in PingIdentity**

*Alt Text:* Screenshot of the **Edit User Filter** interface in IAM Identity Center.  
The user filter is configured to provision users belonging to groups  
with names containing **“Amazon Q Developer”**.  
The condition logic is set to match if **“Any”** of the conditions are true.

9. If you want to synchronize a group from PingIdentity, configure **Group Provisioning**.  
   Navigate to **Group Provisioning** and select the **pencil icon** to edit.

10. Select the appropriate group designated for **Amazon Q Developer** access and choose **Save**.

![docflow17](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-19.png)

**Figure 17 – Configuring Group Provisioning in PingIdentity**

*Alt Text:* Screenshot of the **Edit Group Provisioning** interface in  
IAM Identity Center.  
The **“Amazon Q Developer”** group is selected for outbound provisioning.  
The **Save** button is highlighted at the bottom-left.

11. Navigate to **Attribute Mapping** and select the **pencil icon** to edit the settings.

12. Remove the **Primary Phone** attribute from the **PingOne Directory**.  
13. Add a new attribute mapping where **Username** (PingOne Directory)  
    maps to **displayName** (IAM Identity Store).

14. Select **Save**.

![docflow18](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/12/devops-16054-image-28.png)

**Figure 18 – Attribute Mapping Configuration in PingIdentity**

*Alt Text:* Screenshot of the **Edit Attribute Mapping** interface in PingIdentity.  
The list shows mappings between **PingOne Directory** and **IAM Identity Store** attributes,  
such as *Family Name*, *Given Name*, *Username*, *Email Address*, and *Primary Phone*.  
A newly added mapping **Username → displayName** appears.  
The **Add** and **Save** buttons are highlighted for guidance.

15. Select the rule you created and toggle the switch to **activate the rule**.

This will automatically provision users/groups from PingIdentity to IAM Identity Center via **SCIM**.

![docflow19](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/12/devops-16054-image-29.png)

**Figure 19 – Synchronization Status of Users and Groups via SCIM**

*Alt Text:* IAM Identity Center sync summary showing successful provisioning  
of users and groups.  
The first screenshot highlights two impacted users successfully synchronized.  
The second screenshot highlights a group successfully synchronized.  
The sync status is shown as **“ACTIVE”**, confirming that the integration  
between PingOne and AWS IAM Identity Center is working properly.

---

## Step 7: Grant Access to Amazon Q Developer

This step includes identifying and subscribing the groups that need  
access to **Amazon Q Developer**.

1. In the  
   [Amazon Q Developer console](https://console.aws.amazon.com/amazonq/),  
   under **Subscriptions**, add the IAM Identity Center groups  
   that require access.

2. Select **Subscribe**, then search for the group name.

3. Choose **Assign**.

![docflow20](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-24.png)

**Figure 20 – Amazon Q Developer Subscription Page**

*Alt Text:* Screenshot of the “Subscriptions” page in the Amazon Q Developer console.  
The “Groups” tab is selected, showing “Amazon Q Developer” with subscription status **Subscribed**.  
The “Amazon Q Developer” group is highlighted with a red box.

---

## Setting Up Amazon Q Developer with IAM Identity Center

This section guides you through installing the Amazon Q Developer extension  
and configuring authentication through IAM Identity Center.

1. To install the **Amazon Q Developer extension** in your IDE,  
   follow the steps outlined in the  
   [AWS documentation](https://docs.aws.amazon.com).

2. After installation, select the **Amazon Q** icon in your IDE.  
3. Choose a **Sign-in option**.  
4. Select **Use with Pro license**, then choose **Continue**.  
5. Select **Continue** again.  
6. Enter your **Start URL** — available from the **IAM Identity Center Console**.

![docflow21](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-25.png)

**Figure 21 – IAM Identity Center Access Portal URL**

*Alt Text:* Screenshot of the IAM Identity Center settings page,  
showing the identity source configuration.  
The **AWS access portal URL** and **Identity Store ID** are highlighted in red.  
The **Settings** tab is selected in the navigation panel.

7. Enter the **Region** where your identity directory is hosted, then choose **Continue**.  
8. Select **Open** in the dialog to redirect to your browser.  
9. The browser will redirect to **PingOne**, where you enter your  
   **PingIdentity** credentials, then select **Sign On**.  
10. Once authentication succeeds, choose **Allow access** to complete the login.

![docflow22](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-26-3.gif)

**Figure 22 – Setting Up Amazon Q Developer Extension in Visual Studio Code**

*Alt Text:* Screen recording in Visual Studio Code showing the user selecting  
the Amazon Q icon in the sidebar.  
A login window appears, explaining that the user must authenticate  
with PingIdentity through IAM Identity Center before accessing  
Amazon Q Developer features.

---

## Validate Configuration

Once the previous steps are completed successfully,  
you can begin using Amazon Q Developer’s code suggestions.

![docflow23](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-27.gif)

**Figure 23 – Amazon Q Developer Example**

*Alt Text:* Screen recording from Visual Studio Code showing  
Amazon Q Developer generating sample code in the editor.

---

## Cleanup

To avoid incurring charges after testing, complete the following cleanup steps:

---

### 1. Remove the PingIdentity Application

- In the **PingIdentity** console, navigate to **Applications**.  
- Find and delete the **AWS Single Sign-On** application configured for IAM Identity Center.

---

### 2. Reset IAM Identity Center Configuration

- In the **AWS IAM Identity Center** console:
  - Navigate to **Settings → Identity source**.  
  - Change the **identity source** back to the default  
    **IAM Identity Center directory** if PingIdentity is no longer used.

---

### 3. Revoke Subscriptions and Access

- In the **Amazon Q Developer** console:
  - Go to **Subscriptions** and remove assigned groups  
    such as **Amazon Q Developer** or **CodeWhisperer trial**.

This action **deactivates access** and prevents future costs.

---

### 4. Uninstall Amazon Q Developer Extension

- If desired, you can uninstall the Amazon Q Developer extension  
  from Visual Studio Code to fully revert your environment.

---

## Conclusion

In this article, we demonstrated how to use existing **PingIdentity** credentials  
to access **Amazon Q Developer** through integration with **IAM Identity Center**.

We provided a **step-by-step guide** to configure  
**PingIdentity as an external identity provider (IdP)** for IAM Identity Center.

Finally, we described how to connect the  
**Amazon Q Developer extension** in your IDE to AWS using PingIdentity credentials,  
enabling seamless authentication and usage.

If you have any questions or feedback, feel free to leave a comment below.

---

## Learn More About AWS Services

- [Amazon Q Developer](https://aws.amazon.com/q/)  
- [IAM Identity Center](https://aws.amazon.com/iam/identity-center/)  
- [AWS Toolkit for Visual Studio Code](https://docs.aws.amazon.com/toolkit-for-vscode/)

## About the Author

<div style="display: flex; align-items: flex-start; gap: 16px;">

<img src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-28.jpg" alt="Sid Vantair" style="width:120px; border-radius:8px;">

<div>

Sid Vantair is a **Solutions Architect** at AWS supporting strategic accounts.  
He is passionate about solving complex technical challenges  
to help customers overcome barriers.

Outside of work, he values time with his family  
and encourages a love of learning in his children.

</div>

</div>

