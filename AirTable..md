# Airtable Base Configuration: Meeting Insights

## Table 1: Meetings
Primary table storing all meeting data

### Fields Configuration:
1. **Project** (Single line text)
   - Required: Yes
   - Description: Project name or identifier
   - Example: "Website Redesign"

2. **Client** (Single line text)
   - Required: No
   - Description: Client name if applicable
   - Example: "Acme Corp"

3. **Description** (Long text)
   - Required: Yes
   - Description: Meeting summary or agenda
   - Example: "Weekly progress review for Q1 deliverables"

4. **Task** (Single line text)
   - Required: No
   - Description: Specific task or subproject
   - Example: "Frontend Development"

5. **User** (Single line text)
   - Required: Yes
   - Description: Primary user/owner of the meeting
   - Example: "John Smith"

6. **Group** (Single line text)
   - Required: No
   - Description: Team or department
   - Example: "Engineering"

7. **Email** (Email)
   - Required: No
   - Description: Contact email for the primary user
   - Example: "john.smith@company.com"

8. **Start Date** (Date)
   - Required: Yes
   - Description: Meeting start date
   - Format: YYYY-MM-DD

9. **Start Time** (Time)
   - Required: Yes
   - Description: Meeting start time
   - Format: HH:mm

10. **End Date** (Date)
    - Required: Yes
    - Description: Meeting end date
    - Format: YYYY-MM-DD

11. **End Time** (Time)
    - Required: Yes
    - Description: Meeting end time
    - Format: HH:mm

12. **Start** (Date & Time)
    - Required: Yes
    - Description: Combined start date and time
    - Formula: `DATETIME_PARSE(CONCATENATE({Start Date}, " ", {Start Time}))`
    - Format: ISO 8601

13. **End** (Date & Time)
    - Required: Yes
    - Description: Combined end date and time
    - Formula: `DATETIME_PARSE(CONCATENATE({End Date}, " ", {End Time}))`
    - Format: ISO 8601

14. **Duration (h)** (Single line text)
    - Required: Yes
    - Description: Duration in hours (formatted)
    - Example: "1:30"

15. **Duration (decimal)** (Number)
    - Required: Yes
    - Description: Duration in decimal hours
    - Precision: 2 decimal places
    - Example: 1.50

16. **Tags** (Multiple select)
    - Required: No
    - Description: Meeting categories or labels
    - Allow custom values: Yes
    - Suggested options:
      - Planning
      - Review
      - Client Meeting
      - Internal Sync
      - Workshop
      - Decision Making

17. **Billable** (Checkbox)
    - Required: No
    - Description: Whether the meeting is billable
    - Default: False

18. **Billable Rate (USD)** (Currency)
    - Required: No
    - Description: Hourly rate for billable meetings
    - Format: USD
    - Show if: {Billable} is checked

19. **Billable Amount (USD)** (Currency)
    - Required: No
    - Description: Total billable amount
    - Format: USD

20. **Participants** (Multiple select)
    - Required: Yes
    - Description: List of meeting attendees
    - Allow custom values: Yes
    - Options: Auto-populated from User field

## Table 2: Meeting Insights Cache
Cache table for storing generated insights to improve performance

[Previous cache table configuration remains the same...]

## Views Configuration

### Meetings Table Views:

1. **All Meetings** (Grid view)
   - Sort: Start Date (Descending)
   - Fields: All

2. **Recent Meetings** (Grid view)
   - Filter: Start Date is after (TODAY - 30)
   - Sort: Start Date (Descending)
   - Fields: Project, Description, Start, Duration (decimal), User, Participants

3. **Calendar View**
   - Field Mapping:
     - Event Name: Project
     - Start Field: Start
     - End Field: End
   - Color By: Project

4. **Billable Summary** (Grid view)
   - Filter: Billable is checked
   - Group By: Project
   - Totals:
     - Duration (decimal): SUM
     - Billable Amount: SUM

5. **Time Tracking** (Grid view)
   - Group By: User
   - Sort: Start Date (Descending)
   - Fields: Project, Task, Start, End, Duration (decimal), Billable

### Automations:

1. **Date-Time Sync**
   - Trigger: When Start Date, Start Time, End Date, or End Time is modified
   - Action: Update Start and End DateTime fields

2. **Participant Sync**
   - Trigger: When a record is created or modified in Meetings
   - Action: Update Participants select field options

3. **Cache Cleanup**
   - Trigger: Every day at midnight
   - Action: Delete expired cache entries

### Recommended Indexes:
1. Start Date (for date range queries)
2. Project (for project filtering)
3. User (for user filtering)
4. Billable (for billable reports)