# Frontline Voicemail

This serverless function will allow you to add voicemail to your existing Twilio Frontline application, this assumes that you already have a Frontline instance configured, if not please see the [quickstart](https://www.twilio.com/docs/frontline/nodejs-demo-quickstart) to get started with Frontline.

## Setup Requirements

Before we begin, we need to collect the environment variables that we need to run the function:

- ACCOUNT_SID & AUTH_TOKEN: You can find both of these in the console [here](https://console.twilio.com/?frameUrl=/console).
- CONVERSATION_SID: Your Frontline conversation sid, you can find this [here](https://console.twilio.com/us1/develop/conversations/manage/services?frameUrl=%2Fconsole%2Fconversations%2Fservices)
- WORKER: The worker the voicemail will be routed to, it should be a frontline agent, this variable is expecting an email address
- FRONTLINE_DOMAIN: This will be available after you have deployed the application (notes below)

## Clone Repo

Clone this repo to your local environment:

```
git clone https://github.com/benjohnstone1/frontline-voicemail
cd frontline-voicemail
npm install
```

## Create .env File

Make a copy of the .env.example file to your local environment and update with your environment variables from the setup requirements above (note, we will come back to the FRONTLINE_DOMAIN after you have deployed the twilio function)

```
cp .env.example .env

```

## Deploy Twilio Functions

We will use the [twilio serverless toolkit](https://www.twilio.com/docs/labs/serverless-toolkit) to deploy the twilio function to your account.
Run the following command from the root folder in order to deploy to your account:

```
twilio serverless:deploy
```

By default, Twilio serverless functions are read-only when deployed using the serverless toolkit, to make the function editable within the UI run the following command replacing the sid with your function service sid, find your service sid in the console [here](https://console.twilio.com/us1/develop/functions/services?frameUrl=/console/functions/overview/services)

```
twilio api:serverless:v1:services:update \
    --sid ZSXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX \
    --ui-editable
```

## Update Frontline Domain

Now that we have deployed the function we can update the FRONTLINE_DOMAIN, you can either choose to update the .env file and redeploy using `twilio:serverless:deploy` or you can update within the UI. See below to see where to find the domain within the function editor and where to update assuming you've ran the command to make the function ui-editable.

Copy the domain from the bottom left of the function editor and set it as the FRONTLINE_DOMAIN. The format of this parameter should be “https://frontline-voicemail-XXXX-dev.twil.io” and click update and Deploy All

![alt Frontline Domain](https://github.com/benjohnstone1/frontline-voicemail/blob/main/public/Frontline%20Domain.png)

## Configure Twilio Number

Update your frontline number by changing the voice configuration to accept your Twilio function.

![alt Configure Number](https://github.com/benjohnstone1/frontline-voicemail/blob/main/public/Configure%20Number.png)

## Test!

You can now test out this functionality by calling your Twilio phone number. If the call is answered by the Frontline worker then it will go through as a normal voicecall otherwise the Twilio function will create a Voicemail conversation and post the recording as a new message within the conversation thread.

## Routing

This function will simply route new voicemails to the worker you have set in your environment variable, please feel free to use this as a starting point and update as needed
