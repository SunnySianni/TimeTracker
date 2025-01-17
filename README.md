# Meeting Insights Generator

An AI-powered application that analyzes meeting data to provide actionable insights using ChatGPT, Make.com, and Airtable.

## Features

- Upload and analyze meeting data from CSV files
- Filter meetings by date range and participants
- Generate AI-powered insights using ChatGPT
- Interactive web interface for querying meeting data
- Real-time data processing through Make.com integration

## Prerequisites

- Node.js (v18 or higher)
- npm or yarn
- Make.com account
- Airtable account
- OpenAI API key

## Project Structure

```
meeting-insights/
├── components/         # React components
├── pages/             # Next.js pages and API routes
├── utils/             # Utility functions
├── config/            # Configuration files
├── styles/            # CSS and Tailwind styles
└── public/            # Static assets
```

## Installation

1. Clone the repository:
```bash
git clone [repository-url]
cd TimeTracker
```

2. Install dependencies:
```bash
npm install
```

3. Install required UI components:
```bash
npx shadcn-ui@latest init
npx shadcn-ui@latest add card alert
```

## Configuration

1. Create a `.env.local` file in the root directory:
```env
# Make.com Configuration
MAKE_API_KEY=your_make_api_key
MAKE_INSIGHTS_WEBHOOK=your_make_webhook_url
MAKE_PARTICIPANTS_WEBHOOK=your_make_webhook_url

# OpenAI Configuration
OPENAI_API_KEY=your_openai_api_key

# Airtable Configuration
AIRTABLE_API_KEY=your_airtable_api_key
AIRTABLE_BASE_ID=your_base_id
```

2. Set up Airtable:
- Create a new base using the provided schema in `/docs/airtable-schema.md`
- Configure the necessary tables and fields
- Note your Base ID and API key

3. Configure Make.com scenarios:
- Create two scenarios in Make.com:
  1. Data Import Scenario
  2. Insights Generation Scenario
- Use the templates provided in `/docs/make-scenarios/`
- Configure webhooks and add your API keys

## Development

1. Start the development server:
```bash
npm run dev
```

2. Access the application:
```
http://localhost:3000
```

## Make.com Setup

1. Create New Scenarios:
- Navigate to Make.com dashboard
- Create "Data Import" scenario
- Create "Insights Generation" scenario

2. Configure Webhooks:
- Copy webhook URLs from Make.com
- Add URLs to your `.env.local` file
- Test webhook connectivity

3. Add API Keys:
- Add your OpenAI API key to Make.com
- Configure Airtable connection
- Set up error notifications

## Airtable Setup

1. Create Base Structure:
```
Meetings Table:
- Project (Single line text)
- Description (Long text)
- Start (DateTime)
- End (DateTime)
- Duration (Number)
- Participants (Multiple select)
- Tags (Multiple select)
```

2. Import Initial Data:
- Use the provided CSV template
- Map columns correctly
- Verify data integrity

## Usage

1. Data Import:
- Prepare CSV file in the required format
- Use the import functionality in Make.com
- Verify data in Airtable

2. Generate Insights:
- Select date range
- Choose participants (optional)
- Enter your query
- Click "Generate Insight"

## API Endpoints

### Generate Insight
```http
POST /api/insights
Content-Type: application/json

{
  "query": "string",
  "dateRange": {
    "start": "ISO date",
    "end": "ISO date"
  },
  "participants": ["string"]
}
```

### Get Participants
```http
GET /api/participants
```

## Error Handling

The application includes several error handling mechanisms:
- API timeout handling
- Rate limiting
- Input validation
- User-friendly error messages

## Deployment

1. Build the application:
```bash
npm run build
```

2. Deploy to your hosting platform of choice:
- Vercel (recommended)

## Troubleshooting

Common issues and solutions:

1. Make.com Webhook Errors:
- Verify API keys
- Check webhook URLs
- Confirm scenario activation

2. Airtable Issues:
- Verify base permissions
- Check API access
- Confirm schema matching

3. Frontend Issues:
- Clear browser cache
- Check console errors
- Verify environment variables

## Security Considerations

- Keep API keys secure
- Implement rate limiting
- Validate all inputs
- Use secure CORS policies
- Regularly rotate API keys

