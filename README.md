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