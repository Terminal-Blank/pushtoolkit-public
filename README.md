# PushToolkit

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Node.js](https://img.shields.io/badge/node-%3E%3D18.0.0-brightgreen)](https://nodejs.org/)
[![PostgreSQL](https://img.shields.io/badge/postgresql-15-blue)](https://www.postgresql.org/)
[![Docker](https://img.shields.io/badge/docker-ready-success)](https://www.docker.com/)

**Stop paying monthly fees for push notifications.** PushToolkit is an open-source, self-hosted browser push notification platform built for small businesses, e-commerce stores, and content creators who want to own their subscriber data and send unlimited notifications without per-message pricing. Deploy it on your own server in minutes with Docker, connect any website, and start converting visitors into subscribers — no third-party SaaS required.

## 🚀 Features

- ✅ **Browser Push Notifications** - Web Push API with VAPID authentication
- 📊 **Analytics Dashboard** - Track impressions, clicks, and engagement
- 🎯 **Audience Segmentation** - Target specific user groups
- ⏰ **Scheduled Campaigns** - Send notifications at optimal times
- 🔔 **Automated Workflows** - Welcome series, drip campaigns, RSS feeds
- 🌐 **Multi-Domain Support** - Manage multiple websites from one dashboard
- 🔐 **Self-Hosted** - Complete control over your data
- 📈 **Real-Time Stats** - Live campaign performance metrics
- 🎨 **Customizable UI** - React-based dashboard you can brand
- 🐳 **Docker Ready** - One-command deployment

## 🛠️ Tech Stack

### Backend
- **Node.js 18+** with Express.js
- **PostgreSQL 15** with Prisma ORM
- **Redis 7+** with BullMQ for job queues
- **web-push** library for VAPID-based push delivery

### Frontend
- **React 18** with Vite
- **TailwindCSS** for styling
- **React Query** + Zustand for state management
- **Recharts** for analytics visualization

### Infrastructure
- **Docker** + Docker Compose for containerization
- **Self-hosted** push service
- **CloudFlare** CDN support for service workers

## 📋 Quick Start

### Prerequisites

- Node.js 18+
- PostgreSQL 15
- Redis 7+
- Docker (optional, but recommended)

### Docker Installation (Recommended)

```bash
# Clone the repository
git clone https://github.com/Terminal-Blank/pushtoolkit-public.git
cd pushtoolkit

# Copy environment file
cp .env.docker.example .env

# Generate VAPID keys
npm run generate-keys

# Start all services
docker-compose up -d
```

Visit **http://localhost:3000** for the dashboard and **http://localhost:4000** for the API.

### Manual Installation

```bash
# Clone the repository
git clone https://github.com/Terminal-Blank/pushtoolkit-public.git
cd pushtoolkit

# Install backend dependencies
cd backend
npm install

# Install frontend dependencies
cd ../frontend
npm install

# Set up environment variables
cd ../backend
cp .env.example .env
# Edit .env with your database credentials

# Run database migrations
npx prisma migrate dev

# Generate VAPID keys
npm run generate-vapid

# Start Redis (in separate terminal)
redis-server

# Start backend (in separate terminal)
cd backend
npm run dev

# Start frontend (in separate terminal)
cd frontend
npm run dev
```

## 🔧 Configuration

### Environment Variables

**Backend (.env)**:
```env
DATABASE_URL="postgresql://user:password@localhost:5432/pushtoolkit"
REDIS_URL="redis://localhost:6379"
VAPID_PUBLIC_KEY="<generated-public-key>"
VAPID_PRIVATE_KEY="<generated-private-key>"
VAPID_SUBJECT="mailto:your@email.com"
JWT_SECRET="<random-secret>"
PORT=4000
```

**Frontend (.env)**:
```env
VITE_API_URL=http://localhost:4000
```

### Generating VAPID Keys

VAPID keys are required for browser push notifications:

```bash
cd backend
npm run generate-vapid
```

This will output keys you can add to your `.env` file.

## 📚 Usage

### 1. Create a Project

Navigate to the dashboard and create your first project:
- Enter your website URL
- Upload your icon/logo
- Configure notification preferences

### 2. Install Service Worker

Add the service worker to your website:

```html
<script>
  if ('serviceWorker' in navigator && 'PushManager' in window) {
    navigator.serviceWorker.register('/sw.js')
      .then(registration => {
        console.log('Service Worker registered');
      });
  }
</script>
```

Copy `service-worker/sw.js` to your website's public folder.

### 3. Subscribe Users

Use the subscription endpoint to subscribe users:

```javascript
async function subscribeUser() {
  const registration = await navigator.serviceWorker.ready;
  const subscription = await registration.pushManager.subscribe({
    userVisibleOnly: true,
    applicationServerKey: '<YOUR_VAPID_PUBLIC_KEY>'
  });

  // Send subscription to your backend
  await fetch('http://localhost:4000/api/subscriptions', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      subscription,
      projectId: 'your-project-id'
    })
  });
}
```

### 4. Send Your First Notification

Use the dashboard to compose and send:
1. Go to **Campaigns** > **New Campaign**
2. Write your notification title and body
3. Select target audience
4. Schedule or send immediately

Or use the API:

```bash
curl -X POST http://localhost:4000/api/campaigns \
  -H "Content-Type: application/json" \
  -d '{
    "projectId": "your-project-id",
    "title": "Hello World!",
    "body": "Your first push notification",
    "url": "https://yoursite.com"
  }'
```

## 📖 API Documentation

### Authentication

All API requests require a JWT token:

```bash
curl -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  http://localhost:4000/api/campaigns
```

### Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/register` | Register new user |
| POST | `/api/auth/login` | Login user |
| GET | `/api/projects` | List projects |
| POST | `/api/projects` | Create project |
| GET | `/api/subscriptions` | List subscriptions |
| POST | `/api/subscriptions` | Add subscription |
| POST | `/api/campaigns` | Send campaign |
| GET | `/api/analytics` | Get analytics |

See [ARCHITECTURE.md](./ARCHITECTURE.md) for full system details.

## 🎯 Use Cases

### E-Commerce
- **Cart Abandonment**: Re-engage users who left items in cart
- **Flash Sales**: Instantly notify subscribers of limited offers
- **Order Updates**: Keep customers informed of shipping status

### Content Creators
- **New Posts**: Alert readers when new content is published
- **RSS Integration**: Auto-notify on RSS feed updates
- **Live Streams**: Notify followers when you go live

### SaaS Products
- **Feature Launches**: Announce new features to users
- **Maintenance Alerts**: Warn about scheduled downtime
- **Usage Milestones**: Celebrate user achievements

### News & Media
- **Breaking News**: Instant alerts for urgent stories
- **Daily Digests**: Scheduled news roundups
- **Personalized Content**: Topic-based notifications

## 🔒 Security

- **VAPID Keys**: Secure authentication for push services
- **JWT Tokens**: Stateless authentication for API
- **Rate Limiting**: Protection against abuse
- **CORS**: Configured to prevent unauthorized access
- **Input Validation**: All inputs sanitized and validated

## 📊 Analytics

Track key metrics:
- **Delivery Rate**: Successfully delivered notifications
- **Click-Through Rate (CTR)**: Engagement measurement
- **Conversion Tracking**: Goal completions
- **Time-Based Analysis**: Best times to send
- **Audience Insights**: User behavior patterns

## 🚀 Deployment

### Deploy with Docker Compose

```bash
docker-compose up -d
```

See [DEPLOYMENT_GUIDE.md](./DEPLOYMENT_GUIDE.md) for production deployment.

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Built by [Terminal Blank](https://terminalblank.com)
- Powered by the [Web Push API](https://developer.mozilla.org/en-US/docs/Web/API/Push_API)
- Icons from [Heroicons](https://heroicons.com)

## 📞 Support

- 🐛 [Report Issues](https://github.com/Terminal-Blank/pushtoolkit-public/issues)
- 💬 [Discussions](https://github.com/Terminal-Blank/pushtoolkit-public/discussions)
- 📧 Email: hello@terminalblank.com
- 🌐 Website: [terminalblank.com](https://terminalblank.com)

## ⭐ Show Your Support

Give a ⭐️ if this project helped you!

---

**Made with ❤️ by [Terminal Blank](https://terminalblank.com)**
