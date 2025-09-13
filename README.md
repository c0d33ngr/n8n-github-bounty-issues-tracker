# GitHub Bounty Issue Tracker with Alert System

An automated n8n workflow that monitors GitHub for bounty-labeled issues, tracks them in Google Sheets, and sends notifications via email and WhatsApp for new opportunities.

## Features

- **Automated GitHub Monitoring**: Searches for issues with "💎 Bounty" label every hour
- **Dual Notification System**: Sends alerts via WhatsApp and HTML-formatted emails
- **Smart Filtering**: Only notifies about issues from the last 5 days that haven't been sent before
- **Comprehensive Tracking**: Maintains two Google Sheets for different data purposes
- **Real-time Updates**: Automatically updates issue states and comments every 6 hours
- **Data Cleanup**: Keeps spreadsheets synchronized with current GitHub issue status

## Workflow Architecture

### Main Components

1. **GitHub API Integration**: Fetches bounty issues using GitHub Search API
2. **Google Sheets Management**: Two-sheet system for comprehensive tracking
3. **Notification System**: WhatsApp + Email alerts with rich formatting
4. **Data Synchronization**: Periodic updates for issue status and comments
5. **Smart Filtering**: Prevents duplicate notifications and focuses on recent issues

## Google Sheets Structure

### Sheet 1: `public-bounty` (Master Database)
- **Issue Number**: GitHub issue number
- **Issue Name**: Issue title
- **Issue Repo URL**: Repository URL
- **Issue URL**: Direct link to the issue
- **Issue Creation Date**: When the issue was created
- **Issue State**: Current status (open/closed)
- **Issue Updated Date**: Last update timestamp

### Sheet 2: `sent-issue` (Notification Tracker)
- **Issue Number**: GitHub issue number
- **Issue Name**: Issue title
- **Issue Repo URL**: Repository URL
- **Issue URL**: Direct link to the issue
- **Issue Creation Date**: When the issue was created
- **Number of Issue Comments**: Comment count
- **Issue State**: Current status (open/closed)
- **Bounty Amount**: Extracted from issue labels
- **Issue Bounty Creation Date**: Tracking date

## Workflow Triggers

### Primary Trigger (Hourly)
- **Schedule**: Every 1 hour
- **Purpose**: Search for new bounty issues and send notifications

### Secondary Trigger (6-hourly)
- **Schedule**: Every 6 hours
- **Purpose**: Update existing records with current status and comments

## Setup Requirements

### Prerequisites
1. **n8n Instance**: Self-hosted or cloud instance
2. **Google Sheets**: Two connected spreadsheets
3. **GitHub Token**: Personal access token for API access
4. **WhatsApp Business API**: For WhatsApp notifications
5. **Gmail Account**: For email notifications

### Required Credentials
- `googleSheetsOAuth2Api`: Google Sheets access
- `httpBearerAuth`: GitHub personal access token
- `whatsAppApi`: WhatsApp Business API credentials
- `gmailOAuth2`: Gmail OAuth2 credentials

## Installation

1. **Import Workflow**
   ```bash
   # Import the workflow JSON file into your n8n instance
   ```

2. **Configure Credentials**
   - Set up Google Sheets OAuth2 connection
   - Add GitHub personal access token
   - Configure WhatsApp Business API
   - Set up Gmail OAuth2 credentials

3. **Update Configuration**
   - Replace Google Sheets document ID with your spreadsheet ID
   - Update phone number in WhatsApp node
   - Modify email address in Gmail node
   - Adjust GitHub search query if needed

4. **Test the Workflow**
   - Run manually to verify all connections
   - Check that data appears in both spreadsheets
   - Verify notifications are received

## Key Workflow Nodes

### Data Collection
- **GitHub Search HTTP Request**: Fetches bounty issues with pagination
- **Split Out**: Processes individual issues from the search results

### Data Management
- **Get row(s) in sheet**: Retrieves existing data from spreadsheets
- **Filter**: Prevents duplicate processing and focuses on recent issues
- **Merge**: Combines GitHub data with existing spreadsheet data

### Notifications
- **HTML Node**: Formats email with GitHub-style styling
- **Send message (WhatsApp)**: Quick mobile notifications
- **Send a message (Gmail)**: Detailed HTML email alerts

### Data Synchronization
- **Append/Update Operations**: Maintains spreadsheet data integrity
- **Filter2/Filter3**: Identifies changes requiring updates

## Customization Options

### Search Parameters
Modify the GitHub search query in the HTTP Request node:
```
is:issue label:"💎 Bounty" state:open
```

### Notification Frequency
Adjust the Schedule Trigger intervals:
- Primary: Currently set to 1 hour
- Secondary: Currently set to 6 hours

### Email Template
Customize the HTML node for different email styling or content structure.

### Filter Criteria
Modify the time range filter (currently 5 days):
```javascript
new Date().getTime() - (5 * 24 * 60 * 60 * 1000)
```

## Monitoring and Maintenance

### Regular Checks
- Monitor n8n execution logs for errors
- Verify API rate limits aren't exceeded
- Check spreadsheet data integrity
- Test notification delivery

### Troubleshooting
- **No notifications**: Check GitHub API credentials and rate limits
- **Missing data**: Verify Google Sheets permissions
- **Duplicate entries**: Review filter conditions
- **Failed updates**: Check spreadsheet structure matches workflow expectations

## API Rate Limits

### GitHub API
- **Rate Limit**: 5,000 requests/hour for authenticated requests
- **Current Usage**: ~24-30 requests/day (well within limits)
- **Pagination**: Handles up to 100 issues per search

### Google Sheets API
- **Rate Limit**: 300 requests/minute per project
- **Current Usage**: Minimal, periodic updates only

## Security Considerations

- GitHub token has read-only repository access
- Google Sheets credentials limited to specific spreadsheet
- WhatsApp messages sent to designated number only
- No sensitive data stored in workflow variables

## Performance Optimization

- **Pagination**: Efficiently handles large result sets
- **Smart Filtering**: Reduces unnecessary processing
- **Batch Operations**: Minimizes API calls
- **Error Handling**: Graceful failure management

## Future Enhancements

- [ ] Machine learning for bounty value prediction
- [ ] Integration with time-tracking tools
- [ ] Repository reputation scoring
- [ ] Advanced filtering based on programming languages
- [ ] Slack integration for team notifications

## Support

For issues or questions:
1. Check n8n execution logs
2. Verify all credentials are properly configured
3. Test individual nodes to isolate problems
4. Review API documentation for any service changes

<script src="https://cdn.jsdelivr.net/npm/@webcomponents/webcomponentsjs@2.0.0/webcomponents-loader.js"></script>
<script src="https://www.unpkg.com/lit@2.0.0-rc.2/polyfill-support.js"></script>
<script type="module" src="https://cdn.jsdelivr.net/npm/@n8n_io/n8n-demo-component/n8n-demo.bundled.js"></script>

<n8n-demo workflow='{"nodes":[{"name":"Workflow-Created","type":"n8n-nodes-base.webhook","position":[512,369],"parameters":{"path":"webhook","httpMethod":"POST"},"typeVersion":1}],"connections":{}}'></n8n-demo>

<n8n-demo workflow='{"nodes":[{"parameters":{},"type":"n8n-nodes-base.manualTrigger","typeVersion":1,"position":[0,0],"id":"b90c6847-8ff9-4b77-b8f8-c9f26c4cbebd","name":"When clicking ‘Execute workflow’"},{"parameters":{"options":{}},"type":"n8n-nodes-base.httpRequest","typeVersion":4.2,"position":[208,0],"id":"d7b020ae-04cf-4b32-aa52-fd794e0662b5","name":"HTTP Request"},{"parameters":{"options":{}},"type":"n8n-nodes-base.splitOut","typeVersion":1,"position":[416,0],"id":"1bd93c72-18c3-4224-8202-59ca74a7b328","name":"Split Out"}],"connections":{"When clicking ‘Execute workflow’":{"main":[[{"node":"HTTP Request","type":"main","index":0}]]},"HTTP Request":{"main":[[{"node":"Split Out","type":"main","index":0}]]}},"pinData":{},"meta":{"instanceId":"eabd2a392b89a16ded6ab6084d422b15c6d0c99e5111f156426ee2a569509dbb"}}' frame=true></n8n-demo>

<n8n-demo workflow='{"nodes":[{"parameters":{"url":"https://internal.users.n8n.cloud/webhook/custom-erp","authentication":"genericCredentialType","genericAuthType":"httpHeaderAuth","sendHeaders":true,"headerParameters":{"parameters":[{"name":"unique_id","value":"af70a1474fb4cb3501d4c54bdbee2e58"}]},"options":{}},"type":"n8n-nodes-base.httpRequest","typeVersion":4.2,"position":[208,0],"id":"0d2379dc-0ed1-44ea-bfca-eacc7fbb3c69","name":"HTTP Request","credentials":{"httpHeaderAuth":{"id":"Kka1a999ueKvn5Wn","name":"Header Auth account"}}},{"parameters":{"operation":"create","base":{"__rl":true,"value":"appLp39iVlExwWjoN","mode":"list","cachedResultName":"beginner course","cachedResultUrl":"https://airtable.com/appLp39iVlExwWjoN"},"table":{"__rl":true,"value":"tblgRzOu7oa3nsW1u","mode":"list","cachedResultName":"processingOrders","cachedResultUrl":"https://airtable.com/appLp39iVlExwWjoN/tblgRzOu7oa3nsW1u"},"columns":{"mappingMode":"autoMapInputData","value":{},"matchingColumns":[],"schema":[{"id":"orderID","displayName":"orderID","required":false,"defaultMatch":false,"canBeUsedToMatch":true,"display":true,"type":"number","readOnly":false,"removed":false},{"id":"employeeName","displayName":"employeeName","required":false,"defaultMatch":false,"canBeUsedToMatch":true,"display":true,"type":"string","readOnly":false,"removed":false}],"attemptToConvertTypes":false,"convertFieldsToString":false},"options":{}},"type":"n8n-nodes-base.airtable","typeVersion":2.1,"position":[848,-96],"id":"aebc6142-f5ae-4f32-ac4b-1c972ae23d01","name":"Create a record","credentials":{"airtableTokenApi":{"id":"uUhiGToqs55KFJi4","name":"Airtable Personal Access Token account"}}},{"parameters":{"conditions":{"options":{"caseSensitive":true,"leftValue":"","typeValidation":"strict","version":2},"conditions":[{"id":"a649a5e6-88bc-42de-aa1b-8f883895d7c6","leftValue":"={{ $json.orderStatus }}","rightValue":"processing","operator":{"type":"string","operation":"equals","name":"filter.operator.equals"}},{"id":"eab0f518-5b4d-41bb-bc19-5f2039c0781b","leftValue":"={{ $json.employeeName }}","rightValue":"Mario","operator":{"type":"string","operation":"equals","name":"filter.operator.equals"}}],"combinator":"and"},"options":{}},"type":"n8n-nodes-base.if","typeVersion":2.2,"position":[416,0],"id":"252986c0-8482-4656-87bd-98a334ce1f6d","name":"If"},{"parameters":{"assignments":{"assignments":[{"id":"8e6b5695-80eb-4215-bcee-0d8c2e836400","name":"orderID","value":"={{ $json.orderID }}","type":"number"},{"id":"801ab2b6-89a9-4ae2-80e3-6429628097e0","name":"employeeName","value":"={{ $json.employeeName }}","type":"string"}]},"options":{}},"type":"n8n-nodes-base.set","typeVersion":3.4,"position":[624,-96],"id":"c6af99bd-7ff9-48a3-8a6e-5ffd9a9aa257","name":"Edit Fields"},{"parameters":{"jsCode":"let items = $input.all();\nlet totalBooked = items.length;\nlet bookedSum = 0;\n\nfor (let i=0; i < items.length; i++) {\n  bookedSum = bookedSum + items[i].json.orderPrice;\n}\n\nreturn [{ json: {totalBooked, bookedSum} }];"},"type":"n8n-nodes-base.code","typeVersion":2,"position":[624,96],"id":"c788602c-d291-4d38-be17-0ce2fd4a7767","name":"Code"},{"parameters":{"authentication":"webhook","content":"=This week we've {{$json[\"totalBooked\"]}} booked orders with a total value of {{$json[\"bookedSum\"]}}. My Unique ID: {{ $('HTTP Request').params[\"headerParameters\"][\"parameters\"][0][\"value\"] }}","options":{}},"type":"n8n-nodes-base.discord","typeVersion":2,"position":[832,96],"id":"db1cadcf-f740-4f97-8611-c4ba24f98e2f","name":"Discord","webhookId":"48647c85-0b88-42e1-b35d-39630afe61ac","credentials":{"discordWebhookApi":{"id":"4oltlC6sJ86qOz1M","name":"Discord Webhook account"}}},{"parameters":{"rule":{"interval":[{"field":"weeks","triggerAtHour":9}]}},"type":"n8n-nodes-base.scheduleTrigger","typeVersion":1.2,"position":[0,0],"id":"207e6811-0030-4b1f-b481-7275f3681544","name":"Schedule Trigger"},{"parameters":{"jsCode":"let items = $input.all();\nlet totalBooked = items.length;\nlet bookedSum = 0;\n\nfor (let i=0; i < items.length; i++) {\n  bookedSum = bookedSum + items[i].json.orderPrice;\n}\n\nreturn [{ json: {totalBooked, bookedSum} }];"},"type":"n8n-nodes-base.code","typeVersion":2,"position":[1088,-32],"id":"721bb785-f2a1-4298-9812-37bd197e3aff","name":"Code1"}],"connections":{"HTTP Request":{"main":[[{"node":"If","type":"main","index":0}]]},"Create a record":{"main":[[{"node":"Code1","type":"main","index":0}]]},"If":{"main":[[{"node":"Edit Fields","type":"main","index":0}],[{"node":"Code","type":"main","index":0}]]},"Edit Fields":{"main":[[{"node":"Create a record","type":"main","index":0}]]},"Code":{"main":[[{"node":"Discord","type":"main","index":0}]]},"Schedule Trigger":{"main":[[{"node":"HTTP Request","type":"main","index":0}]]}},"pinData":{},"meta":{"templateCredsSetupCompleted":true,"instanceId":"eabd2a392b89a16ded6ab6084d422b15c6d0c99e5111f156426ee2a569509dbb"}}'></n8n-demo>