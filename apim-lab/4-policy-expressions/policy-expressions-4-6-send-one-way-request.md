## Task 6: Send One Way policy

APIM integrates well with [external services](https://docs.microsoft.com/en-us/azure/api-management/api-management-sample-send-request) via HTTP-based interaction.

This example shows a fire-and-forget [send-one-way-request](https://docs.microsoft.com/en-us/azure/api-management/api-management-sample-send-request#send-one-way-request) policy, which does not await a response. Alternatively, you can use a [send-request](https://docs.microsoft.com/en-us/azure/api-management/api-management-sample-send-request#send-request) policy to send a request and await a return. Some complex in-flight processing logic may also be better handled by using Logic Apps.

### Setup the Send-One-Way-Request policy

The following policy and payload applies for both examples in this section (webhook and teams). **Please ensure that you replace the value in `<set-url>` with your webhook target URL.** You will identify the URL in either example below.

- From **Basic Calculator API (1)** ,select the **Add two integers (2)** operation .
- In **Outbound Processing (3)** section ,open the **Code View (4)**.
- Add the `send-one-way-request` policy to **Outbound processing** and replace the webhook and payload as required. For demo purposes, we are going to use the payload for a Teams message (even for Webhook.site) and also send the message on every successful request.
  
  ![](media/Pg15-1.png)

>**Note:** Make Sure that **Add two integers** operation is selected.

  ```xml
  <outbound>
      <base />
      <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
      <set-header name="x-aspnet-version" exists-action="delete" />
      <set-header name="x-powered-by" exists-action="delete" />
      <choose>
          <when condition="@(context.Response.StatusCode == 200)">
              <send-one-way-request mode="new">
                  <set-url>https://enter-your-webhook-url</set-url>
                  <set-method>POST</set-method>
                  <set-body>@{
        return new JObject(
          new JProperty("@type","MessageCard"),
          new JProperty("@context", "http://schema.org/extensions"),
          new JProperty("summary","Summary"),
          new JProperty("themeColor", "0075FF"),
          new JProperty("sections",
            new JArray (
              new JObject (
                new JProperty("text", "Hello!")
              )
            )
          )
        ).ToString();
      }</set-body>
              </send-one-way-request>
          </when>
      </choose>
  </outbound>
  ```

### Send a message to Webhook.site

[Webhook.site](https://webhook.site) is a simple recipient to test webhook messages and requires no setup overhead, making this an ideal component in this lab, especially as we are not sending any sensitive information in our payload from our test Azure API Management instance.

- Go to [Webhook.site](https://webhook.site) and copy the value for **Your unique URL**.

    ![Webhook Site Setup](media/34.png)

- Use this URL as the value in the `<set-url>` property in the `send-one-way-request` policy previously defined.

    ![Webhook Site Setup](media/adding-web-hook.png)

- Invoke the API from the Azure API Management **Test** tab by clicking the **Trace** button and observe the `200` success response.

- Check the **Trace** for the **Outbound** one-way message.

    ![Webhook Site APIM Trace](media/35.png)

- Observe the success in the Webhook site.

    ![Webhook Site Success](media/36.png)

### Send a message to the Microsoft Teams channel (Optional)

An optional lab to connect to Microsoft Teams:

- Follow the official steps from the [documentation to create an Incoming Webhook](https://docs.microsoft.com/en-us/microsoftteams/platform/webhooks-and-connectors/how-to/add-incoming-webhook#create-an-incoming-webhook-1) and when you have your webhook URL ready, update the previous `send-one-way-request` policy.

- If you want, you can format the required payload. The payload sent to a Teams channel uses the [MessageCard](https://docs.microsoft.com/en-us/microsoftteams/platform/task-modules-and-cards/cards/cards-reference) JSON schema. You can experiment with different cards in the [MessageCard Playground](https://messagecardplayground.azurewebsites.net/).

- When you are ready, invoke the API from the **Test** tab and observe the `200` success response.

- Look for a received message in your Teams channel:

  ![Teams APIM Message](media/37.png)
---
### Summary 
In this task, a "Send One-Way" policy is set up in Azure API Management (APIM) to send a webhook message without waiting for a response. The policy is applied to the "Add two integers" operation in the Calculator API.
- Now, click on Next from the lower right corner to move on to the next page.
