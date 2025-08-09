# Clarence Services API Reference

## Overview

Say hello to Clarence the Dog – Pleo’s favorite furry companion and now your programmable pup via API. With the Clarence Services API, you can interact with Clarence just like we do at Pleo:
- Ask him to sit
- Offer his paw
- Heel at your command

It’s our way of bringing a bit of joyful, tail-wagging energy to your tools.
Built for developers who love dogs (and good documentation), this REST API helps you bring Clarence’s charm into your workflows.

**Typical use case:**
When Clarence sits, offers his paw, or heel — like wagging his tail — he gets a little reward such as a treat or maybe even a toy. It’s a fun way to encourage him!

## Authentication setup

To keep Clarence safe and your requests secure, the API supports two authentication methods:

**Just a heads-up:**
When you're setting things up, please use one authentication method only—either an API key or OAuth2, but not both.

1. OAuth2.0 (Recommended)

    Pleo recommends OAuth2.0 for applications that require secure, user-based access — especially when interacting across multiple Pleo services.

    You will need to:
	
      a. Register your app via Pleo’s Developer Portal.
      b. Obtain an OAuth2 access token using client credentials or authorization code flow.
      c. Send the token in the Authorization header with each request.
 
**Example:**
Authorization: Bearer YOUR_ACCESS_TOKEN

2. API Key (Simpler setup)

    For lightweight use cases, when integrating Pleo with apps that don't support OAuth (like on-premise apps). This method works for custom integrations or temporary data pulls.
 
    You will need to:
	
      a. Generate an API key via your developer dashboard.
      b. Include it in the x-api-key header of every request.

**Example:**
x-api-key: YOUR_API_KEY

**Note:**
If we can't find valid credentials, we'll send back an error message.

```
{
  "status": 401,
  "error": "Unauthorized",
  "hint": "Please enter a valid access token or API key."
}
```

For more details, see [Authentication and Authorisation](https://developers.pleo.io/docs/authentication-authorisation-introduction).

## Endpoints

### Tell Clarence to sit

**POST** /v3/clarence/sit

Ask Clarence to sit, optionally with a set duration, repetition count, and reward. // Alternative "Ready to have Clarence sit? Just use this endpoint!"

**Example request**

- Method: POST
- URL: https://external.pleo.io/v3/clarence/sit
- Headers:
  - Authorization: Bearer YOUR_ACCESS_TOKEN
  - Content-Type: application/json
 
```
{
  "count": 2,
  "duration": "60 secs",
  "rewardType": "treat",
  "reward": {
    "treatType": "Multi",
    "variety": [
      {"item": "Cheese", "qty": 1},
      {"item": "Carrot", "qty": 1},
      {"item": "Crunchy treats", "qty": 5}
    ]
  }
}
```

If the rewardType is "toy", structure reward like this:

```
"reward": {
  "type": "Rubber Ball",
  "color": "Blue"
}
```

**Request fields definition**

| Field        | Type    | Required | Description                                                               |
| ------------ | ------- | -------- | ------------------------------------------------------------------------- |
| `count`      | Integer | No       | How many times Clarence should sit. Defaults to 1 if not set. Must be ≥ 1.|
| `duration`   | String  | No       | How long Clarence should sit. Accepts `"60 secs"`, `"1 min"`, etc. |
| `rewardType` | String  | Yes      | What Clarence gets — a `"treat"` or a `"toy"`.|
| `reward`     | Object  | Yes      | Reward details, based on `rewardType`.                        |

**If rewardType = "treat":**

| Subfield    | Type   | Required | Description                                                  |
| ----------- | ------ | -------- | ------------------------------------------------------------ |
| `treatType` | String | Yes      | How you want to give the treats — `"Single"` for one treat or `"Multi"` for a selection. |
| `variety`   | Array  | Yes      | List of treats. At least one required.                |

**Inside variety array:**

| Field  | Type    | Required | Description            |
| ------ | ------- | -------- | ---------------------- |
| `item` | String  | Yes      | Treat name.     |
| `qty`  | Integer | Yes      | How many of that treat Clarence should get. Must be ≥ 1. |

**If rewardType = "toy":**

| Subfield | Type   | Required | Description                                      |
| -------- | ------ | -------- | ------------------------------------------------ |
| `type`   | String | Yes      | Type of toy, e.g., `"Bone"`, `"Rubber Ball"`. |
| `color`  | String | No      | Toy color, e.g., `"Blue"`, `"Red"`. Defaults to "Blue" if not set.|

**Example response**

```
{
  "status": "success",
  "action": "sit",
  "count": 2,
  "requestedDuration": "60 secs",
  "actualDuration": "30 secs",
  "rewardGiven": true,
  "rewardType": "treat",
  "rewardDetails": {
    "treats": ["Cheese", "Carrot", "Crunchy treats"]
  },
  "message": "Clarence was great today! He sat perfectly for 60 seconds, twice, and got lots of yummy treats as a reward."
}
```

**Response fields definition**

| Field           | Type    | Always Present?                   | Description                                                 |
| --------------- | ------- | --------------------------------- | ----------------------------------------------------------- |
| `status`        | String  | Yes                               | "success" tells you everything went to plan.  |
| `action`        | String  | Yes                               | Clarence performed the action "sit".              |
| `count`         | Integer | Yes                               | How many times Clarence sat.                               |
| `requestedDuration`|String|	No (only if duration was specified in request)	|How long the client asked Clarence to sit.|
| `actualDuration`   | String | Yes                               | How long Clarence actually sat.                        |
| `rewardGiven`   | Boolean | Yes                               | Whether Clarence was given a reward.                        |
| `rewardType`    | String  | No (only if `rewardGiven = true`) | Type of reward given (`"treat"` or `"toy"`).                |
| `rewardDetails` | Object  | No (only if `rewardGiven = true`) | Details of the reward — list of treats or toy details.      |
| `message`       | String  | Yes                               | A short, friendly description of what happened. |


### Ask for Clarence’s paw

**GET** /v3/clarence/paw

Ask Clarence to offer his paw —just specify 'left' or 'right'.

**Example request**

- Method: GET
- URL: https://external.pleo.io/v3/clarence/paw?side=left

  or
  
  https://external.pleo.io/v3/clarence/paw?side=right
- Headers:
  - Authorization: Bearer YOUR_ACCESS_TOKEN

**Query parameters**

|Name	|Type	|Description	|Required|
|-------|-------|----------------|-----------------|
|side	|string	|"left" or "right"|	Yes|

**Example response**

```
{
  "status": "success",
  "action": "paw",
  "side": "left",
  "message": "Clarence offers his left paw."
}
```

**Response field definition**

| Field     | Type   | Description                                                                               |
| --------- | ------ | ----------------------------------------------------------------------------------------- |
| `status`  | String | "success" tells you everything went to plan. |
| `action`  | String | Clarence performed the action "paw". He offered his paw.                                 |
| `side`    | String | Which paw Clarence offered—left or right? In this case, it was the left one.        |
| `message` | String | A short, friendly description of what happened.                 |

### Tell Clarence to heel

**POST** /v3/clarence/heel

Ask Clarence to walk nicely by your side.

**Example request**

- Method: POST
- URL: https://external.pleo.io/v3/clarence/heel
- Headers:
  - Authorization: Bearer YOUR_ACCESS_TOKEN
  - Content-Type: application/json

**Example request with toy**

```
{
  "count": 1,
  "duration": "2 min",
  "rewardType": "toy",
  "reward": {
    "type": "Rubber Ball",
    "color": "Blue"
  }
}
```

**Example response with toy**

```
{
  "status": "success",
  "action": "heel",
  "count": 1,
  "requestedDuration": "2 min",
  "actualDuration": "2 min",
  "rewardGiven": true,
  "rewardType": "toy",
  "rewardDetails": {
    "toy": {
      "type": "Rubber Ball",
      "color": "Blue"
    }
  },
  "message": "Clarence walked nicely by your side and received a blue Rubber Ball to play for the whole day."
}
```

**Response fields definition**

| Field           | Type    | Always Present?                   | Description                                                 |
| --------------- | ------- | --------------------------------- | ----------------------------------------------------------- |
| `status`        | String  | Yes                               | "success" tells you everything went to plan.  |
| `action`        | String  | Yes                               | Clarence performed the action "heel".              |
| `count`         | Integer | Yes                               | How many times Clarence sat.                               |
| `requestedDuration`|String|	No (only if duration was specified in request)	|How long the client asked Clarence to sit.|
| `actualDuration`   | String | Yes                               | How long Clarence actually sat.                        |
| `rewardGiven`   | Boolean | Yes                               | Whether Clarence was given a reward.                        |
| `rewardType`    | String  | No (only if `rewardGiven = true`) | Type of reward given (`"treat"` or `"toy"`).                |
| `rewardDetails` | Object  | No (only if `rewardGiven = true`) | Details of the reward — list of treats or toy details.      |
| `message`       | String  | Yes                               | A short, friendly description of what happened. |


## Error handling

400 Bad Request – Clarence is Asleep
Clarence is resting. If you try to interact with him while he’s snoozing, you’ll get a 400 Bad Request.

**Error response**
```
{
  "status": "error",
  "error": "Clarence is asleep.",
  "hint": "Try again later when Clarence is awake."
}
```
Note: Please don't mistake Clarence, he isn’t ignoring you – he’s just recharging. Wait a bit, then send your request again.

## FAQs

Q: Can I skip reward?

    A: Not in this version — every action includes a reward.

Q: What happens if a treat is out of stock?

    A: You’ll get a 409 Conflict with a hint listing the unavailable items.

Q: Is duration exact?

    A: No, it’s a target. The response shows both requested and actual times.

Q: Can I set Clarence’s breed?

    A: Yes — you can set it when you create Clarence’s profile. For example: "breed": "Patterdale Terrier".

Q: Should I set the water level if Clarence wants to drink?

    A: Yes — Clarence enjoys one glass of water a day. You can specify the amount to keep his bowl topped up.

## Notes

- For future proof, `rewardType`, and `rewardDetails` are not always present.
- Color in toy rewards is optional — if omitted, backend assigns a default.

## Best practices

- Do not expose your API key (if you are using one) in frontend code or public repositories.
- Cache responses where possible to reduce API calls.
- Monitor rate limits to avoid disruptions.
- Use HTTPS to ensure secure communication.
- Handle 409 Conflict gracefully by offering alternative rewards.