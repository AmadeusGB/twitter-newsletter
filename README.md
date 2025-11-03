# Twitter Newsletter Service

A Next.js-based automated newsletter service that delivers personalized Twitter content digests to users via email. Users can configure custom schedules to receive curated newsletters based on specific Twitter accounts, keywords, or industry topics.

## Features

- **Flexible Content Curation**: Subscribe to newsletters based on:
  - Specific Twitter users
  - Keywords and hashtags
  - Industry topics and trends

- **Smart Scheduling**: Configure delivery times in your local timezone (internally managed via Unix timestamps)

- **AI-Powered Summarization**: Leverages Grok AI model to generate intelligent content summaries and insights

- **User-Friendly Dashboard**: Intuitive frontend for managing subscriptions and schedules

- **Automated Delivery**: Backend cron jobs handle scheduled email dispatch

## Tech Stack

### Frontend
- **Next.js 14+** - React framework with App Router
- **TypeScript** - Type-safe development
- **Tailwind CSS** - Utility-first styling
- **shadcn/ui** - Component library

### Backend
- **Next.js API Routes** - Serverless API endpoints
- **Prisma** - Database ORM
- **PostgreSQL** - Primary database
- **Node-cron** - Task scheduling

### External Services
- **Twitter API v2** - Tweet fetching and user data
- **Grok API** - AI content generation and summarization
- **Resend/SendGrid** - Email delivery service

## Architecture

```
twitter-newsletter/
├── app/
│   ├── api/              # API routes
│   │   ├── auth/         # Authentication endpoints
│   │   ├── newsletters/  # Newsletter CRUD
│   │   ├── cron/         # Scheduled job triggers
│   │   └── webhooks/     # External service webhooks
│   ├── dashboard/        # User dashboard pages
│   └── (auth)/           # Auth pages (login/signup)
├── components/           # React components
├── lib/
│   ├── db/              # Database utilities
│   ├── twitter/         # Twitter API integration
│   ├── grok/            # Grok API integration
│   ├── email/           # Email service
│   └── scheduler/       # Cron job management
├── prisma/
│   └── schema.prisma    # Database schema
└── types/               # TypeScript definitions
```

## Getting Started

### Prerequisites

- Node.js 18+
- PostgreSQL database
- Twitter API credentials
- Grok API key
- Email service API key (Resend/SendGrid)

### Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/twitter-newsletter.git
cd twitter-newsletter

# Install dependencies
npm install

# Set up environment variables
cp .env.example .env.local
# Edit .env.local with your credentials

# Initialize database
npx prisma migrate dev

# Run development server
npm run dev
```

Visit `http://localhost:3000` to see the application.

### Environment Variables

```env
# Database
DATABASE_URL="postgresql://..."

# Authentication
NEXTAUTH_SECRET="your-secret"
NEXTAUTH_URL="http://localhost:3000"

# Twitter API
TWITTER_API_KEY="..."
TWITTER_API_SECRET="..."
TWITTER_BEARER_TOKEN="..."

# Grok AI
GROK_API_KEY="..."
GROK_API_URL="..."

# Email Service
RESEND_API_KEY="..."
FROM_EMAIL="noreply@yourdomain.com"

# Cron Job Secret (for securing cron endpoints)
CRON_SECRET="..."
```

## Core Features

### 1. Newsletter Configuration

Users can create multiple newsletter subscriptions with:
- **Name & Description**: Identify your newsletter
- **Content Sources**:
  - Twitter usernames to follow
  - Keywords/hashtags to track
  - Industry categories
- **Schedule**: Set delivery time in local timezone
- **Frequency**: Daily, weekly, or custom intervals

### 2. AI Content Processing

- Fetches relevant tweets based on configuration
- Uses Grok AI to:
  - Summarize tweet threads
  - Extract key insights
  - Generate engaging newsletter content
  - Filter noise and identify trends

### 3. Automated Scheduling

- Converts user's local time to Unix timestamps
- Manages timezone conversions automatically
- Triggers scheduled jobs via cron
- Handles retry logic for failed deliveries

### 4. Email Delivery

- Responsive email templates
- Personalized content for each user
- Unsubscribe management
- Delivery tracking and analytics

## API Endpoints

### Newsletters

- `POST /api/newsletters` - Create new newsletter
- `GET /api/newsletters` - List user's newsletters
- `PUT /api/newsletters/[id]` - Update newsletter
- `DELETE /api/newsletters/[id]` - Delete newsletter

### Cron Jobs

- `POST /api/cron/process-newsletters` - Process all due newsletters
- `POST /api/cron/cleanup` - Clean up old data

## Database Schema

```prisma
model User {
  id            String       @id @default(cuid())
  email         String       @unique
  timezone      String       @default("UTC")
  newsletters   Newsletter[]
  createdAt     DateTime     @default(now())
}

model Newsletter {
  id              String   @id @default(cuid())
  userId          String
  user            User     @relation(fields: [userId], references: [id])
  name            String
  description     String?

  // Content sources
  twitterUsers    String[] // Array of Twitter usernames
  keywords        String[] // Array of keywords/hashtags
  industries      String[] // Array of industry tags

  // Scheduling
  scheduleTime    Int      // Unix timestamp (user's local time)
  frequency       String   // "daily", "weekly", "custom"
  timezone        String   // User's timezone

  // Settings
  isActive        Boolean  @default(true)
  lastSentAt      DateTime?
  nextSendAt      DateTime?

  createdAt       DateTime @default(now())
  updatedAt       DateTime @updatedAt

  deliveries      Delivery[]
}

model Delivery {
  id            String      @id @default(cuid())
  newsletterId  String
  newsletter    Newsletter  @relation(fields: [newsletterId], references: [id])
  status        String      // "pending", "sent", "failed"
  sentAt        DateTime?
  error         String?
  createdAt     DateTime    @default(now())
}
```

## Deployment

### Vercel (Recommended)

```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
vercel --prod
```

Configure environment variables in Vercel dashboard.

### Cron Jobs Setup

For production, use Vercel Cron or external services:

**vercel.json**:
```json
{
  "crons": [{
    "path": "/api/cron/process-newsletters",
    "schedule": "*/15 * * * *"
  }]
}
```

## Development Roadmap

- [ ] User authentication (NextAuth.js)
- [ ] Newsletter CRUD operations
- [ ] Twitter API integration
- [ ] Grok AI integration
- [ ] Email template system
- [ ] Cron job scheduler
- [ ] User dashboard UI
- [ ] Analytics and tracking
- [ ] Payment integration (premium features)
- [ ] Mobile app (React Native)

## Contributing

Contributions are welcome! Please read our contributing guidelines and code of conduct.

## License

MIT License - see LICENSE file for details.

## Support

For issues and questions:
- GitHub Issues: [Report a bug](https://github.com/yourusername/twitter-newsletter/issues)
- Email: support@yourdomain.com

---

Built with ❤️ using Next.js and Grok AI
