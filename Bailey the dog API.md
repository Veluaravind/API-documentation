# Bailey Services API Reference

## Overview

Say hello to Bailey the Dog – My favorite furry companion and now your programmable pup via API. With the Bailey Services API, you can interact with Bailey just like we do at <<XYZ>>:
- Ask him to sit
- Offer his paw
- Heel at your command

It’s our way of bringing a bit of joyful, tail-wagging energy to your tools.
Built for developers who love dogs (and good documentation), this REST API helps you bring Bailey’s charm into your workflows.

**Typical use case:**
When Bailey sits, offers his paw, or heel — like wagging his tail — he gets a little reward such as a treat or maybe even a toy. It’s a fun way to encourage him!

## Authentication setup

To keep Bailey safe and your requests secure, XYZ supports two authentication methods:

**Just a heads-up:**
When you're setting things up, please use one authentication method only—either an API key or OAuth2, but not both.

1. OAuth2.0 

    Best for secure, user-based access across XYZ services.

    How to use:
	
      a. Register your app via XYZ’s Developer Portal.
   
      b. Obtain an OAuth2 access token using client credentials or authorization code flow.
   
      c. Include the token in the Authorization header for every request.
 
**Example:**
Authorization: Bearer YOUR_ACCESS_TOKEN

2. API Key (Simpler setup)

    For lightweight use cases, when integrating XYZ with apps that don't support OAuth (like on-premise apps). This method works for custom integrations or temporary data pulls.
 
    How to use:
	
      a. Generate an API key via your developer dashboard.
   
      b. Include it in the x-api-key header of every request.

**Example:**
x-api-key: YOUR_API_KEY

For more details, see [Authentication and Authorisation](https://developers.xyz.io/docs/authentication-authorisation-introduction).

## Endpoints

### Tell Bailey to sit

**POST** /v3/Bailey/sit

Ask Bailey to sit. You can specify how many times, for how long, and what reward he gets.


**Example request**

- Method: POST
- URL: https://external.xyz.io/v3/Bailey/sit
- Headers:
  - Authorization: Bearer YOUR_ACCESS_TOKEN
  - Content-Type: application/json
 
**Body parameters**

| Field        | Type    | Required | Description                                                               |
| ------------ | ------- | -------- | ------------------------------------------------------------------------- |
| `count`      | Integer | No       | How many times Bailey should sit. Defaults to 1 if not set. Must be ≥ 1.|
| `duration`   | String  | No       | How long Bailey should sit. Accepts `"60 secs"`, `"1 min"`, etc. Keep it short and sweet — up to 10 characters|
| `rewardType` | String  | Yes      | What Bailey gets — a `"treat"` or a `"toy"`. Keep it short and sweet — up to 5 characters|
| `reward`     | Object  | Yes      | Reward details, based on `rewardType`.                        |

**If rewardType = "treat":**

| Subfield    | Type   | Required | Description                                                  |
| ----------- | ------ | -------- | ------------------------------------------------------------ |
| `treatType` | String | Yes      | How you want to give the treats — `"Single"` for one treat or `"Multi"` for a selection. |
| `variety`   | Array  | Yes      | List of treats. At least one required.                |

**Inside variety array:**

| Field  | Type    | Required | Description            |
| ------ | ------- | -------- | ---------------------- |
| `item` | String  | Yes      | Treat name. Keep it short and sweet — up to 20 characters    |
| `qty`  | Integer | Yes      | How many of that treat Bailey should get. Must be ≥ 1. |

**If rewardType = "toy":**

| Subfield | Type   | Required | Description                                      |
| -------- | ------ | -------- | ------------------------------------------------ |
| `type`   | String | Yes      | Type of toy, e.g., `"Bone"`, `"Rubber Ball"`. Keep it short and sweet — up to 15 characters |
| `color`  | String | No      | Toy color, e.g., `"Blue"`, `"Red"`. It defaults to "Blue" if it is not set. Keep it short and sweet — up to 10 characters.|
 
**Example request body**
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

If the rewardType is "toy", structure the reward like this:

```
"reward": {
  "type": "Rubber Ball",
  "color": "Blue"
}
```

**Example successful response**

```
{
  "action": "sit",
  "count": 2,
  "requestedDuration": "60 secs",
  "actualDuration": "30 secs",
  "rewardGiven": true,
  "rewardType": "treat",
  "rewardDetails": {
    "treats": ["Cheese", "Carrot", "Crunchy treats"]
  },
  "message": "Bailey was great today! He sat perfectly for 60 seconds, twice, and got lots of yummy treats as a reward."
}
```

**Response fields definition**

| Field           | Type    |  Description                                                 |
| --------------- | ------- | ----------------------------------------------------------- |
| `action`        | String  | Bailey performed the action "sit".                        |
| `count`         | Integer | How many times Bailey sat.                               |
| `requestedDuration`|String| Displays how long the client asked Bailey to sit, only if a duration is specified in the request.|
| `actualDuration`   | String |   How long Bailey actually sat.                        |
| `rewardGiven`   | Boolean |  Whether Bailey was given a reward.                        |
| `rewardType`    | String  |  Type of reward given (`"treat"` or `"toy"`).                |
| `rewardDetails` | Object  |  Details of the reward — list of treats or toy details.      |
| `message`       | String  |  A short, friendly description of what happened. |


### Ask for Bailey’s paw

**GET** /v3/Bailey/paw

Ask Bailey to offer his paw —specify 'left' or 'right' via the query parameter.

**Request**

**Query parameters**

|Name	|Type	|Required  |Description     |
|-------|-------|----------|-----------------|
|side	|string	|Yes       |"left" or "right" (Keep it short and sweet — up to 5 characters.)|	

**Example request**

- Method: GET
- URL: https://external.xyz.io/v3/Bailey/paw?side=left
- Headers:
  - Authorization: Bearer YOUR_ACCESS_TOKEN

**Example successful response**

```
{
  "action": "paw",
  "side": "left",
  "message": "Bailey offers his left paw."
}
```

**Response field definition**

| Field     | Type   | Description                                                                               |
| --------- | ------ | ----------------------------------------------------------------------------------------- |
| `action`  | String | Bailey performed the action "paw". He offered his paw.                                 |
| `side`    | String | Which paw Bailey offered—left or right? In this case, it was the left one.        |
| `message` | String | A short, friendly description of what happened.                 |

### Tell Bailey to heel

**POST** /v3/Bailey/heel

Ask Bailey to walk nicely by your side.

**Example request**

- Method: POST
- URL: https://external.xyz.io/v3/Bailey/heel
- Headers:
  - Authorization: Bearer YOUR_ACCESS_TOKEN
  - Content-Type: application/json

**Body parameters**

| Field        | Type    | Required | Description                                                               |
| ------------ | ------- | -------- | ------------------------------------------------------------------------- |
| `count`      | Integer | No       | How many times Bailey should heel. Defaults to 1 if not set. Must be ≥ 1.|
| `duration`   | String  | No       | How long Bailey should heel. Accepts `"60 secs"`, `"1 min"`, etc. Keep it short and sweet — up to 10 characters|
| `rewardType` | String  | Yes      | What Bailey gets — a `"treat"` or a `"toy"`. Keep it short and sweet — up to 5 characters|
| `reward`     | Object  | Yes      | Reward details, based on `rewardType`. For example, "toy", specify type and optional color.                      |

**Example request body**

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

**Example successful response**

```
{
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
  "message": "Bailey walked nicely by your side and received a blue Rubber Ball to play for the whole day."
}
```

**Response fields definition**

| Field           | Type    |  Description                                                 |
| --------------- | ------- | ----------------------------------------------------------- |
| `action`        | String  | Bailey performed the action "heel".              |
| `count`         | Integer | How many times Bailey sat.                               |
| `requestedDuration`|String| Displays how long the client asked Bailey to sit, only if a duration is specified in the request.|
| `actualDuration`   | String |How long Bailey actually sat.                        |
| `rewardGiven`   | Boolean |  Whether Bailey was given a reward.                        |
| `rewardType`    | String  | Type of reward given (`"treat"` or `"toy"`).                |
| `rewardDetails` | Object  |  Details of the reward — list of treats or toy details.      |
| `message`       | String  |  A short, friendly description of what happened. |


## Error handling

**Error response**
```
{
  "status": 400,
  "meaning": "Bad request",
  "message": "Bailey is asleep. Please try again later when Bailey is awake."
}
```

| Status code | Meaning      |Description                                 | Cause                                                | Suggested fix                            |
|-------------|--------------|------------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| 200         | OK           |      `Success`                             |       N/A                                            |              N/A                         |
| 400         | Bad request  |`Bailey is asleep.`                       | He is tired.                                         | Please try again later when Bailey is awake.  |
| 400         | Bad request  |`Duration value is invalid.`                | Value is not one of: `"30 secs"`, `"1 min"`          | Use an accepted value.                   |
| 400         | Bad request  |`Treat quantity must be at least 1.`        | A treat has `qty` < 1                                | Ensure all `qty` values are 1 or more.   |
| 400         | Bad request  |`Treat type is missing in reward.`          | `treatType` key is absent                            | Add `treatType` field inside `reward`. |
| 400         | Bad request  |`Reward variety must include at least one item.` | `variety` is missing or empty                   | Add at least one treat in `variety`.     |

## FAQs

Q: Can I skip reward?

    A: Not in this version — every action includes a reward.

Q: What happens if a treat is out of stock?

    A: You’ll get a 409 Conflict with a hint listing the unavailable items.

Q: Is duration exact?

    A: No, it’s a target. The response shows both requested and actual times.

Q: Can I set Bailey’s breed?

    A: Yes — you can set it when you create Bailey’s profile. For example: "breed": "Patterdale Terrier".

Q: Should I set the water level if Bailey wants to drink?

    A: Yes — Bailey enjoys one glass of water a day. You can specify the amount to keep his bowl topped up.

## Notes

- For future proof, `rewardType`, and `rewardDetails` are not always present.
- Color in toy rewards is optional — if omitted, backend assigns a default.
- If Bailey is asleep, any action endpoint (/sit, /paw, /heel) will return this error: HTTP Status: 400 Bad Request

## Best practices

- Do not expose your API key (if you are using one) in frontend code or public repositories.
- Cache responses where possible to reduce API calls.
- Monitor rate limits to avoid disruptions.
- Use HTTPS to ensure secure communication.