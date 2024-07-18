# Miner Minder
A not so gentle reminder to help remind people to submit their backblasts.

## Prerequisites
You must already have PAXminer, Weaslebot, and QSignups installed for this to work.

## Process
The app will take a list of Slack Workspaces and iterate through them. It will query PAXminer and QSignups during a specified time period and check to see if any backblasts are missing. The app will group by Q (if available from QSignups) and send a message detailing which backblasts are missing in a direct message. Then it will check the day of the week. If the current day of the week is the one configured for that workspace, it will group missing backblasts by SiteQ if available from the PAXminer `aos` table and send an alert detailing the missing backblasts to the Site Q in a direct message. It will then group missing backblasts by AO and message the AO channel detailing which ones are missing.

## Inputs
Due to limited storage options, inputs are sent to the app via 2 environmental variables. Both are strings. The first input is common inputs. Semicolons separate variables for different workspaces and commas separate inputs for each workout into individual variables. The other input is slack tokens, which are separated for security reasons.

### Common Inputs
`"Denver,TCT7SH4JC,C04R36F5YGJ,1,75,2;Denver2,TCT7SH4JC,C04R36F5YGJ,1,75,3"`

This is an example of an input. It represents variables for 2 Slack Workspaces (separated by a comma). Variables for a given workspace are separated using a comma and are defined by the order they are written. Here is which each workspace variable is, by location.
1. PAXminer database
    - The name of the workspace's PAXminer database (e.g. f3denver, f3alliance)
    - This is also used to designate operations in the logs
1. Workspace ID
    - The Slack-defined ID for your workspace (see https://docs.louply.io/how-tos/how_to_get_slack_workspace_id/ for instructions on finding it.)
1. Log Channel ID
    - The Slack-defined ID of a channel you want this app to write logs to. This is useful for troubleshooting. It will write 1 message to this channel every time it runs and will indicate how many missing backblasts it found.
    - You can use the #paxminer_logs channel or another channel. Generally, it should be a channel not commonly used by users. You will most likely end up muting this channel.
    - See https://help.socialintents.com/article/148-how-to-find-your-slack-team-id-and-slack-channel-id for instructions on finding a Channel ID
    - If this is set left empty (e.g. ",,") then no logs will be written
1. Minimum Notification Days (grace period)
    - How many days do you want to give a Q to post before messaging them?
    - Example: If this is set to 0, they will be notified right away. For example, if they Q at 5:30 AM and they do not post the backblast by the time the app runs at 9 AM, then they will be notified.
    - Example: If this is set to 1, they have 1 day to post their backblast. If they Q at 5:30 AM on Monday and they haven't posted their backblast by the time the app runs at 9 AM, they won't be notified. But if they still haven't posted by the time the app runs at 9 AM on Tuesday, they will be notified.
    - This can be any positive integer.
1. Maximum Notification Days
    - How long should a Q be notified before giving up?
    - Example: If this is set to 75, the app will notify the Q of a missing backblast every time the app runs until more than 75 days have passed since the Q.
1. Site Notification Day
    - A Q will be notified every time the app runs if they don't post. To prevent alarm fatigue on the Site Q and AO, they will only be notified one day a week.
    - The number for the variable represents the day of the week Site Q and AO should be notified. 0 is Monday and 6 is Sunday.

### Secret Inputs
`xoxb-alsdhgfliucbe1234978yhsdf;xoxb-sjdhfn9812oeuwkjdhf`

Because you can do a lot of damage with a Slack Bot token, these are separated from the other inputs and should be treated carefully. Bot tokens for different Slack Workspaces should be separated by semicolons. The order of the bot tokens in this input should match the order of the common inputs.
1. Bot User OAuth Token
    - For this app to post messages to Q's, Site Q's, and AOs, it will need certain permissions. You will have to hhave a custom app installed in your workspace and grant that app the required permissions. You can use an existing app (like PAXminer), or create a new one. https://medium.com/applied-data-science/how-to-build-you-own-slack-bot-714283fd16e5 has a basic run-through of creating an app. When you get to the part about assigning bot token scopes, select "chat:write" and "chat:write.public". You can get the Bot User OAuth Token from the top of the page where you assign scopes.

## Logging
Basic logs are written to the backend system using Python's Print() command. If a channel ID is specified in the common inputs, a message will also be posted there for each run.
