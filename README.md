# ApplicationControlAPI
Step-By-Step Guides on how to interact with the Deep Security / Workload Security API


## Creating a Shared Ruleset via API

Perhaps you're looking to create a list of allowed executables or applications for a group of machines. The Application Control module of Deep Security / Cloud One Workload Security fits that bill nicely. In this guide, we'll walk step-by-step through creating API Keys, configuring an API request, and how we'll utilize the API to create a Shared Ruleset which we can use with Application Control. 

This step-by-step guide will take you through API setup to enabling the ruleset on your determined machines. 

### Before we start
It's important that we understand a few key phrases about Application Control. 
**Shared Rulesets*** are a list of executables that should be allowed by Application Control. These can be referenced in multiple policies and are best used when trying to standardize application control across a wide swath of machines. 
**Global Rulesets** are a list of executables that will be Blocked by Application Control. 
**Software Inventory** is 

### Creating an API Key in Deep Security
  1. Login to the Deep Security Manager
  2. Navigate to Administration section of the console
  3. On left side there will be an API section to create the key
  4. Give the API key a name and copy/save the output after hitting next
  
  ![image](https://user-images.githubusercontent.com/12953672/214129836-9318e618-38c2-4395-b763-03dc12c46fd9.png)
  
--Workload placeholder

### Creating an API request using Postman

#### Downloading Postman
There are many API tools out there. For the purposes of this demonstration and guide we'll be using Postman. 
You can download postman here: https://www.postman.com/downloads/

#### Configuring Postman
After downloading and installing Postman you should be met with an empty API call with a few "Headers" prepopulated:
<img width="1268" alt="image" src="https://user-images.githubusercontent.com/12953672/214153459-8b25b278-d3e1-4cbc-9db9-ba8778400997.png">

This is where we'll be adding two lines to define the API Version as well as our authentication.
 
- In the "Key" tab under "Headers" add a line for `api-version`, for the "Value" tab fill in `v1`
- **For Deep Security Users** the "Key" tab under "Headers" add a line called `api-secret-key`, for the "Value" tab fill in `YOUR SECRET KEY`
The "Headers" section of your request should look like the below:
<img width="1253" alt="image" src="https://user-images.githubusercontent.com/12953672/214164751-b03f8433-5c17-4d93-9e21-85f14abef10b.png">

- **For Cloud One Workload Security Users** the "Key" tab under "Headers" create a line called `Authorization`, for the "Value" tab fill in `ApiKey YOUR-API-KEY`
The "Headers" section of your request should look like the below: 
<img width="961" alt="image" src="https://user-images.githubusercontent.com/12953672/214155346-5fc82b4d-8323-4bdf-8f9d-fbe0c8bd2523.png">

The URL we call will depend on whether you're using Deep Security or Cloud One Workload Security. 

For Deep Security, we'll be using the domain name of your Deep Security Manager with the Port, and api in the URL.
Example: `https://dsm.example.com:4119/api`

For Cloud One Workload Security, you'll need to ensure you're using the right URL for your accounts region. 
Example" `https://workload.{{region}}.cloudone.trendmicro.com/api`

For the purposes of this guide, i'll be using the Workload Security API. The requests are nearly identical and are documented below:
- Deep Security API Reference: https://automation.deepsecurity.trendmicro.com/article/20_0/api-reference/
- Cloud One Workload Security Reference: https://cloudone.trendmicro.com/docs/workload-security/api-reference/

### Getting Computers

Before we create a software inventory and shared ruleset for application control, we need to get the ID of the computer we're going to create the software inventory based off of. Ideally, this will be a machine that will have the applications and files you'd like the approve on the broader range of machines.

With our "Headers" section of the request filled out as outlined above, we're going to add the URL we'll be calling. The URL itself can be split into three pieces.
1. **The Endpoint**, for example https://dsm.example.com:4119/api
2. **The Path**, for example /computers
3. **The Parameters**, are usually used to send a query or refine the return, and aren't needed on all requests.

We're going to input a request to return all of the computers in our management console. Keep in mind that your endpoint may be different, but the paths are generally the same.

1. Add the URL into the request and list computers. You can find this path in the API Reference.
![image](https://user-images.githubusercontent.com/12953672/214172591-5a1b1eb5-b22d-4cc8-bd30-7daff60ca062.png)
This is what my request looks like:
<img width="1270" alt="image" src="https://user-images.githubusercontent.com/12953672/214172806-366c7f05-df81-47b3-ae61-d28303c15ff4.png">
2. Ensure you've selected "Get" to the left of the URL field, and hit Send!

If you're successful, you should see many lines of JSON with every piece of information available about each computer and agent. 
<img width="1200" alt="image" src="https://user-images.githubusercontent.com/12953672/214173983-50ad11e9-4c53-4020-ad22-6fa0a7bdd058.png">
If you're unsuccessful, you'll likely get an error. This is usually because of authentication (check for spaces), or an incorrect entry in the URL. 
<img width="452" alt="image" src="https://user-images.githubusercontent.com/12953672/214173470-9c060694-6830-483d-a514-1d77f9fa1ffa.png">

### Getting Computer IDs
Great! We have a response with lots of data, but too much data for what we need. We're going to need the ID of the computer that we'd like to base our Software Inventory from. Ensure that you have a machine with applications you'd like to be allowed in this software inventory before continuing. 

1. We're going to add the URL again, but this time we'll append parameters to limit the data we get back. Add the following parameters to your request:
`?expand?=none` your entire URL should look something like `https://workload.us-1.cloudone.trendmicro.com/api/computers?expand=none`. 
You can also enter `expand` and `none` as a Key/Value pair under the "Params" tab in Postman. This will automatically format and add the paramaeter to your request.
<img width="1252" alt="image" src="https://user-images.githubusercontent.com/12953672/214175302-7d33700d-7ba8-4ffb-b966-dcb357476f0f.png">

2. Hit Send! You should now see much less data. 
3. We'll need to parse this list for the computer we're looking to create a Software Inventory of. You can search by host name, display name, IP, etc. You can search for this using the "Search" feature in the top right of the response pane.
<img width="1213" alt="image" src="https://user-images.githubusercontent.com/12953672/214176110-16d37891-17fd-47dd-9d69-f39cc3d063f0.png">
4. Once you see the ID, take note of this value. We'll use it later when creating the Software Inventory. 

### Creating a Software Inventory
As you can see, the API is a very powerful tool when interacting with the management server. So far we've just performed "Get" requests to pull results of queries. Next, we'll be telling the management server to perform some actions. 

1. We're going to change the path of our API request to create a Software Inventory of the machine we just identified. Check the API reference for "Application Control > Create a Software Inventory"
![image](https://user-images.githubusercontent.com/12953672/214176999-f8a0b7e4-a640-40a1-9062-2e34e1a83ea6.png)
2. Input the new path `/softwareinventories` and change the method to "POST" it should look something like this:
<img width="583" alt="image" src="https://user-images.githubusercontent.com/12953672/214177333-344a935c-d476-4af4-8311-ecfb138227ce.png">
3. This request needs a body! Luckily the API reference has the example JSON we need. Copy & Paste the below into the "Body" tab of the request. 

![image](https://user-images.githubusercontent.com/12953672/214177465-82935d44-61ff-49a6-9edd-c2889cee4776.png)

``` yaml
{
  "computerID": 0,
  "name": "string",
  "description": "string"
} 
```
4. Change the value of computerID to the value we found earlier when pulling the list of computers. 
Your request should look something like this:
<img width="1257" alt="image" src="https://user-images.githubusercontent.com/12953672/214180297-533d0733-85f9-413d-9e3c-0ccb7987fea2.png">

5. You should get a response that your Software Inventory is building. Give it a few minutes and then send the following as a "GET" request:
`YOUR-API-ENDPOINT/api/softwareinventories`
It should look something like this, body doesn't matter in a GET request:
<img width="1251" alt="image" src="https://user-images.githubusercontent.com/12953672/214181404-f143326b-60fb-4af7-b166-1ba7010ea8e4.png">
The response should look something like this:
<img width="454" alt="image" src="https://user-images.githubusercontent.com/12953672/214181578-0122bef6-ed33-44b9-86d8-35a2b4da8cda.png">
Note the "ID" of the Software Inventory. We'll be using it in our last step.

### Create a Shared Ruleset

Wow! You've become quite the API guru in just a few simple steps. Now we're going to create a shared ruleset.

To create a shared ruleset, we'll need to have a Software Inventory ID to reference. 


1. We're going to change the method to "POST" and the URL path to `/api/rulesets`.
2. This request is going to need some parameters to reference! We're going to add the following parameters to the URL: `/?softwareinventoryid=YOUR-SOFTWARE-INVENTORY-ID` 
You can also add `softwareinventoryid` and `YOUR-SOFTWARE-INVENTORY-ID` as a Key/Value pair under "Params"
<img width="1246" alt="image" src="https://user-images.githubusercontent.com/12953672/214184908-d30d71a3-35b1-4c8c-a837-56154cf69720.png">
3. This request also needs body! We'll need to create a name and description for this ruleset. This can be anything, but should identify its purpose.

``` yaml
{
  "name": "your-string",
  "description": "your-string"
}
```
<img width="1251" alt="image" src="https://user-images.githubusercontent.com/12953672/214188049-23e37a2a-f015-43c7-b19a-cae5a1da8c02.png">

4. Once you've filled out your POST URL, parameters, and body, hit Send!
5. Your response should look something like this, including the name of the ruleset and ID. 
<img width="404" alt="image" src="https://user-images.githubusercontent.com/12953672/214188277-b2f8ab4c-ccf0-4f41-96e7-0b5c5766467e.png">

Congrats! You've made a shared ruleset that you can reference in a policy using application control. Our work using the API can also now be referenced in the GUI.
![image](https://user-images.githubusercontent.com/12953672/214188836-afe621fb-cfec-4041-aa09-aa4232cbc39c.png)











  



