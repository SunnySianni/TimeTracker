# Make.com Meeting Insights Scenario Setup Guide

## Prerequisites
- Make.com account
- Airtable account with a base set up
- OpenAI API key
- Your application's API endpoint

## Step 1: Create New Scenario

1. Log into Make.com
2. Click "Create a new scenario"
3. Name it "Meeting Insights Generator"
4. Click "Add new trigger"

## Step 2: Configure Webhook Trigger

1. Search for and select "Webhooks" in the modules list
2. Select "Custom webhook" as the trigger
3. Configure webhook settings:
```json
{
  "method": "POST",
  "headers": {
    "x-api-key": "{{config.api_key}}"
  },
  "bodyType": "raw",
  "expectedType": "application/json",
  "schema": {
    "type": "object",
    "required": ["query", "dateRange"],
    "properties": {
      "query": { "type": "string" },
      "dateRange": {
        "type": "object",
        "properties": {
          "start": { "type": "string", "format": "date-time" },
          "end": { "type": "string", "format": "date-time" }
        }
      },
      "participants": {
        "type": "array",
        "items": { "type": "string" }
      }
    }
  }
}
```
4. Save the webhook configuration and copy the generated webhook URL

## Step 3: Add Airtable Search Module

1. Click (+) after the webhook module
2. Search for and select "Airtable"
3. Choose "Search records" operation
4. Configure Airtable connection:
   - Select/create connection
   - Choose your base
   - Select "Meetings" table
5. Set up the filter formula:
```javascript
// Click "Add a formula" and paste this code
let startDate = formatDate(webhookData.dateRange.start);
let endDate = formatDate(webhookData.dateRange.end);

return `AND(
  IS_AFTER({Start}, '${startDate}'),
  IS_BEFORE({Start}, '${endDate}')
  ${webhookData.participants.length ? 
    `, OR(${webhookData.participants.map(p => 
      `FIND('${p}', CONCATENATE(Participants))`
    ).join(',')})` 
    : ''
  }
)`;
```
6. Set sorting: Add "Start" field with "desc" direction

## Step 4: Add Data Transformation Module

1. Click (+) after Airtable module
2. Select "Tools" > "Set Variable"
3. Configure data aggregation:
```javascript
// Variable name: meetingData
// Type: Object
// Value (click "Add a formula"):
{
  summary: {
    totalMeetings: records.length,
    dateRange: `${startDate} to ${endDate}`,
    uniqueParticipants: [...new Set(records.flatMap(r => r.Participants))],
    totalHours: records.reduce((sum, r) => sum + r.Duration, 0)
  },
  meetings: records.map(record => ({
    project: record.Project,
    description: record.Description,
    duration: record.Duration,
    date: record.Start,
    participants: record.Participants,
    tags: record.Tags
  }))
}
```

## Step 5: Add ChatGPT Integration

1. Click (+) after transformation module
2. Search for and select "HTTP" module
3. Configure HTTP request:
   - Method: POST
   - URL: https://api.openai.com/v1/chat/completions
   - Headers:
     ```json
     {
       "Authorization": "Bearer {{openai_api_key}}",
       "Content-Type": "application/json"
     }
     ```
   - Body (JSON):
     ```json
     {
       "model": "gpt-4",
       "messages": [
         {
           "role": "system",
           "content": "You are an expert meeting analyst with deep experience in business intelligence and organizational dynamics. Your role is to analyze meeting data and provide actionable insights."
         },
         {
           "role": "user",
           "content": "Analyze the following meeting data:\n{{meetingData}}\n\nUser Query: {{webhookData.query}}"
         }
       ]
     }
     ```

## Step 6: Add Response Formatting

1. Click (+) after ChatGPT module
2. Add another "Tools" > "Set Variable" module
3. Configure response formatting:
```javascript
// Variable name: formattedResponse
// Type: Object
// Value (click "Add a formula"):
{
  insight: gptResponse.choices[0].message.content,
  metadata: {
    analyzedMeetings: records.length,
    dateRange: meetingData.summary.dateRange,
    generatedAt: new Date().toISOString()
  }
}
```

## Step 7: Configure Error Handling

1. Add error handlers to each module:
   - Click on each module
   - Go to "Error handler" tab
   - Select "Continue to next route"
2. Add parallel error route:
   - Right-click main route
   - Select "Add parallel route"
3. Configure error response:
```javascript
{
  error: {
    code: "ERROR_" + error.type,
    message: "Failed to generate insight",
    details: error.message
  }
}
```

## Step 8: Add Rate Limiting (Optional)

1. Add "Tools" > "Set Variable" before ChatGPT module
2. Configure rate limiting:
```javascript
// Variable name: rateLimitKey
// Value: `insights_${new Date().toISOString().slice(0, 13)}`

// Add another Set Variable for counter
// Use built-in storage to track request count
// Add IF router to check count < 100
```

## Final Steps

1. Configure scenario settings:
   - Schedule: Real-time
   - Execution mode: Sequential
   - Data retention: 30 days
2. Set up error notifications:
   - Go to scenario settings
   - Configure email notifications for errors
3. Test the scenario:
   - Click "Run once"
   - Use sample data to test
4. Activate the scenario

## Important Notes

- Keep your API keys secure in Make.com variables
- Monitor usage to adjust rate limits
- Set up appropriate error notifications
- Consider adding retry logic for API calls
- Test thoroughly with various input combinations
- Monitor execution history for debugging

Remember to update your frontend application's environment variables with the Make.com webhook URL generated in Step 2.