# ApplicationControlAPI
Step-By-Step Guides on how to interact with the Deep Security / Workload Security API


## Creating a Shared Ruleset via API

Perhaps you're looking to create a list of allowed executables or applications for a group of machines. The Application Control module of Deep Security / Cloud One Workload Security fits that bill nicely.

This step-by-step guide will take you through API setup to enabling the ruleset on your determined machines. 

#### Creating an API Key in Deep Security
  1. Login to the Deep Security Manager
  2. Navigate to Administration section of the console
  3. On left side there will be an API section to create the key
  4. Give the API key a name and copy/save the output after hitting next
  
  ![image](https://user-images.githubusercontent.com/12953672/214129836-9318e618-38c2-4395-b763-03dc12c46fd9.png)
  
--Workload placeholder

#### Creating an API request using Postman

There are many API tools out there. For the purposes of this demonstration and guide we'll be using Postman. 
You can download postman here: https://www.postman.com/downloads/

After downloading and installing Postman you should be met with an empty API call with a few "Headers" prepopulated:
<img width="1268" alt="image" src="https://user-images.githubusercontent.com/12953672/214153459-8b25b278-d3e1-4cbc-9db9-ba8778400997.png">

This is where we'll be adding two lines to define the `api-version` and `Authorization`.
- In the "Key" tab under "Headers" add a line for `api-version`. For the "Value" tab fill in `v1`
- Again, in the "Key" tab under "Headers" create a line called `Authorization`. The value will be `ApiKey YOUR-API-KEY`
The "Headers" section of your request should look like the below: 
<img width="961" alt="image" src="https://user-images.githubusercontent.com/12953672/214155346-5fc82b4d-8323-4bdf-8f9d-fbe0c8bd2523.png">




  



