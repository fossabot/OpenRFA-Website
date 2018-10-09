## OpenRFA API Documentation
Consider this documentation in a constant state of improvement. We will continue to write-up documentation as we further develop the API.

OpenRFA.org is currently built on Drupal 7.x, therefore the [Services](https://www.drupal.org/project/services) module has been used to implement the REST API.

Note: The API is only currently available on the development server: http://dev.openrfa.org. Please reach out to jmerlan or vdubya for administrator access.

### Endpoint Samples

#### Retrieve CSRF Token
##### Endpoint:
```rest/user/token.json```
##### C# Sample Code:
```csharp
        public static string GetCsrfToken()
        {
            string token = string.Empty;

            // Get CSRF token using RestSharp
            var client = new RestClient("http://dev.openrfa.org");
            var request = new RestRequest("rest/user/token.json", Method.POST);
            request.AddHeader("Content-Type", "application/json");
            IRestResponse response = client.Execute(request);

            // Return token as JSON object
            token = response.Content;

            return token;
        }
```

#### Log in using OpenRFA.org
##### Endpoint:
```rest/user/login.json```
##### C# Sample Code:
```csharp
        public static string LogIn()
        {

            string json = string.Empty;

            // Login using RestSharp
            var client = new RestClient("http://dev.openrfa.org");

            // Set the data format to JSON because default is XML
            var request = new RestRequest("rest/user/login.json", Method.POST) { 
            	RequestFormat = RestSharp.DataFormat.Json
            };

            request.AddHeader("X-CSRF-Token", "k1qBSxDlnCOK8jgMnVbBxTn7HtnBABaxh0Bzu7Rre8Y");
            request.AddHeader("Content-Type", "application/json");
            request.AddBody(new { username = "MartyMcFly", password = "helloooMcFly!" });

            IRestResponse response = client.Execute(request);

            // Returned user session data is in JSON format
            json = response.Content;

            return json;
        }
```

#### Get Shared Parameters
```rest/views/services_parameters?display_id=view_parameters```
##### With Pagination
```rest/views/services_parameters?display_id=view_parameters&limit=5&offset=0```

#### Filter Shared Parameter by GUID
(shared parameter with GUID: 368fac86-6f88-4553-999b-a17e7e822e19)

```rest/views/services_parameters?display_id=view_parameters&filters[guid]=368fac86-6f88-4553-999b-a17e7e822e19```

#### Filter Shared Parameters by Name 
(shared parameters which name contains "code")

```rest/views/services_parameters?display_id=view_parameters&filters[name]=code```

#### Filter Shared Paramters by State
(shared parameters with Current State: "Approved")

```rest/views/services_parameters?display_id=view_parameters&filters[state_id]=3```

##### Shared Parameter State IDs
- (2) Proposed
- (3) Approved
- (4) Rejected

#### Propose a New Share Parameter
```http://dev.openrfa.org/rest/node```

##### Required fields
The following fields are required in your `POST` to propose a new shared parameter. Note that you also must be logged in with a user who has permissions to propose a shared parameter.
- type=shared_parameter
- title={{NameOfSharedParameter}}
- field_data_type={{DataTypeId}}
- field_group={{GroupId}}
- field_description={{Tooltip}}

**For field_data_type and field_group fields, refer to: http://openrfa.org/documentation/api/term-ids**

Not supplying the required fields will return this error message in JSON: 
```json
{
    "form_errors": {
        "field_data_type][und": "Data Type field is required.",
        "field_group][und": "Group field is required.",
        "field_description][und][0][value": "Description field is required."
    }
}
```