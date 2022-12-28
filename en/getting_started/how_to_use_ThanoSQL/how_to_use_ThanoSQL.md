---
title: ThanoSQL Web Manual
---

# __ThanoSQL Web Manual__

## __1. Connect to [ThanoSQL Web](https://www.thanosql.ai/en/){style="color:#604bcc" target="_blank"}__

Go to ThanoSQL and click on the **Login Button (or the start button at the homepage)** to go to the login page.

[![IMAGE](/en/img/getting_started/img0.png)](/en/img/getting_started/img0.png)

!!! note ""
      To use the ThanoSQL console, please sign up first.

## __2. Sign Up__

Click on the **Sign up Button** in the login page.

[![IMAGE](/en/img/getting_started/img1.png)](/en/img/getting_started/img1.png)

- Fill out the signup form and click on the **Create Account** button.
- Verify your email address and agree to our Terms of Service and Privacy Policy to create an account.

[![IMAGE](/en/img/getting_started/img2.png)](/en/img/getting_started/img2.png)

## __3. Use the ThanoSQL Console__

- Log in and click on the **Start Button** to create a new workspace.

[![IMAGE](/en/img/getting_started/img3.png)](/en/img/getting_started/img3.png)

## __3-1. Plan Settings__

[![IMAGE](/en/img/getting_started/img4.png)](/en/img/getting_started/img4.png)

① **Plan**

- Choose a plan.
- Click the Start Button to go to the next step.

② **Promotion Code**

- Enter a promotion code.
- Click the Start Button after entering the code to go to the next step.
!!! warning
      - Promotion codes cannot be reused once they create a workspace.

## __3-2. Application Form__

[![IMAGE](/en/img/getting_started/img5.png)](/en/img/getting_started/img5.png)

① Create a workspace name

- Workspace names must be unique.
- Workspace names must start with a letter and can only be composed of lowercase letters and numbers(example: myworkspace123)

② Selected Plan

1. Check the Selected Plan
      - Confirm the selected Plan Tier, Plan Name, and Price.
2. Email
      - Please input the email address that will recieve your invoice.
      - **This field is mandatory.**
3. Customer Name
      - Please input the customer's full name.
4. mobile phone number
      - Please input your mobile phone number in case we cannot reach you via email.
5. Inquiry
      - Please input any inquiries you may have.
!!! tip ""
      workspace end-date changes, contact information change, etc...

③ Complete Request

- Click on the **Complete Button** to request / complete creating a new workspace.

## __4. Create Workspace__

① Workspace

- Click on the **Open Button** to open the selected workspace.
- Click on the More Button to download the SSH Key or update your workspace plan.

② Create additional workspaces

- Click on the **Create New Workspace** card to create additional workspaces.

[![IMAGE](/en/img/getting_started/img6.png)](/en/img/getting_started/img6.png)

## __5. ThanoSQL Workspace__

Query and create AI models with both structured and unstructured data with SQL.

① Get API Token

- **Click on the "GET API_TOKEN" Button to get your API token (copied into your clipboard). Use the query below to connect to and start using ThanoSQL.**
- You must use the given API token to use our services. Though, you can request for new API tokens, be aware that older tokens will automatically expire.
```sql
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```
- The query above must be executed first to use the ThanoSQL's syntax.

② Switching Workspaces

- Click on the workspace drop down list to switch between your workspaces.

[![IMAGE](/en/img/getting_started/img7.png)](/en/img/getting_started/img7.png)