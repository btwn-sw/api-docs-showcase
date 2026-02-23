# Step-by-Step Tutorial

This tutorial walks you through retrieving event details using the Eventbrite API. By the end of this guide, you will have successfully, authenticating an API request, calling an Eventbrite API endpoint, inspecting the API response, and importing and testing the request in Postman.

### Table of Contents

- [Prerequisites](#prerequisites)
- [Step-by-Step Guide](#step-by-step-guide)

<br>

## Prerequisites

Eventbrite API requires the following:

- An active Eventbrite account
- A valid **Private (OAuth) Token**
- A Postman account installed or accessible via browser

<br>

## Step-by-Step Guide

**Step 1. Get a Private Token**

To authenticate API requests, you need a private token. If you have not yet created the token, follow the instructions in the [Authentication Guide](../guides/authentication.md).

<br>

**Step 2. Select an API Endpoint**

You will retrieve details for a single event.

1. Navigate to the Eventbrite [API Reference](https://www.eventbrite.com/platform/api/).
2. In the sidebar, select a desired endpoint through **Reference > Event > Retrieve an Event**.

***Note**: Reference represents all available Eventbrite endpoints.*

<br>

**Step 3. Enable Try-it-out**

Eventbrite provides an interactive console powered by Apiary.

1. In the right-hand pane, click the **Try** button.
2. This enables you to call a live API request.

<br>

**Step 4. Configure Request Parameters**

Next, configure the required request details.

1. **Query Parameters**
    
    
    | Key | Value |
    | --- | --- |
    | `event_id`  | `12345` |
2. **Headers**: Add your private token in place of “*Bearer PERSONAL_OAUTH_TOKEN.*”
    
    ```graphql
    Authorization: Bearer PERSONAL_OAUTH_TOKEN
    Content-Type: application/json
    ```
    
3. **Request Body**: Not required for this endpoint.
4. Click **Call Resource**.

<br>

**Step 5. Inspect the API Response**

After the request completes, scroll to the **Response Body** section. You can see a JSON object containing event details such as :

- Event name
- Description
- Start and end time
- Status

<br>

**Step 6. Generate a Code Example**

To reuse the request outside the console, generate a code snippet.

1. Click **Show Code Example**, right below the parameters section **i**n the Console pane.
    
    ``![image.png](attachment:f9dce0b9-7502-4c22-bf2f-cd2e52a4571a:image.png)``
    
2. Select the **cURL**.
You can change the language you preferred from the language dropdown.
3. Copy the generated command.
    
    ```bash
    curl --include \
         --header "Authorization: ************" \
         --header "Content-Type: application/json" \
      'https://www.eventbriteapi.com/v3/events/12345/'
    ```

<br>

**Step 7. Test the Request in Postman**

To further inspect the request and response, import the cURL command into Postman.

1. Open Postman and sign in.
2. In the menu bar, click **File → Import**.
3. Select the **Paste Tab** (named “*Paste cURL, gRPCurl, Raw text or URL…*”) and paste the cURL command you copied from Eventbrite.
4. Change into the collection name (optional).
Collection lets you group related requests.
    
``![image.png](attachment:6f2579e4-a5f9-4749-9fa2-fcde933148da:image.png)``

5. Click the `Import Into Collection` button.
Then, Postman automatically populates a new tab with the OAuth information under the **Headers** tab.
    
``![image.png](attachment:9a272c44-687c-4ae6-be77-5b4c966be425:image.png)``
    
6. Click the `Send` button to execute the request.
7. Confirm that a `200 OK` response is successfully returned with the request’s details. 
You can switch the response format to dropdown **‘{ } JSON’** if needed.
    
``![image.png](attachment:262c44c8-6006-4216-9e37-def9808f12f4:image.png)``

<br>

## Next steps

- [API Reference](../api/api-reference.md)
- [Response Handling Guide](../guides/response_handling.md)
- [Code Examples](../examples/code-examples.md)
- [SDKs](../sdks/sdks.md)

<br>

---