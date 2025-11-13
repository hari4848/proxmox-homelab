# n8n

A powerful workflow automation tool that connects your apps and services to automate tasks and processes. n8n provides a visual interface for creating complex workflows without writing code.

## Overview

n8n offers:
- **Visual Workflow Builder:** Drag-and-drop interface for creating automations
- **300+ Integrations:** Connect to popular services and APIs
- **Self-Hosted:** Full control over your data and workflows
- **Flexible Triggers:** Webhooks, schedules, and manual execution
- **Custom Code:** JavaScript/TypeScript support for custom logic
- **Webhook Support:** Receive data from external services

**Official Documentation:** https://docs.n8n.io

## Quick Start

### 1. Environment Setup
Create `.env` file with your configuration:
```bash
SUBDOMAIN=n8n
DOMAIN_NAME=yourdomain.com
SSL_EMAIL=your@email.com
GENERIC_TIMEZONE=Asia/Kolkata
```

### 2. Start n8n
```bash
docker compose up -d
```

### 3. Access n8n
Open `https://n8n.yourdomain.com` in your browser.

## Initial Setup

### First-Time Configuration
1. Create your admin account
2. Set your name and password
3. Choose your workflow preferences
4. Explore the workflow templates

### Creating Your First Workflow

#### Simple Example: Email Notification
1. Click "New Workflow"
2. Add "Manual Trigger" node
3. Add "Send Email" node
4. Connect the nodes
5. Configure email settings
6. Click "Execute Workflow"

#### Webhook Example: GitHub Integration
1. Add "Webhook" trigger
2. Copy webhook URL
3. Add to GitHub repository settings
4. Add processing nodes (filter, transform, etc.)
5. Add action nodes (send notification, create task, etc.)

## Key Features

### Popular Integrations
- **Communication:** Slack, Discord, Telegram, Email
- **Storage:** Google Drive, Dropbox, OneDrive
- **Databases:** PostgreSQL, MySQL, MongoDB
- **CRM:** Salesforce, HubSpot, Airtable
- **Development:** GitHub, GitLab, Jira
- **Productivity:** Notion, Trello, Asana

### Workflow Components
- **Triggers:** Start workflows (webhook, schedule, manual)
- **Nodes:** Individual processing steps
- **Connections:** Data flow between nodes
- **Expressions:** Dynamic data manipulation
- **Error Handling:** Retry logic and error workflows

## Common Use Cases

### Automation Examples
- **File Processing:** Automatically process uploaded files
- **Data Sync:** Sync data between different services
- **Notifications:** Send alerts based on conditions
- **Report Generation:** Create and email regular reports
- **API Integration:** Connect different APIs together
- **Content Management:** Automate content publishing

### Homelab Integrations
- **ARR Stack Notifications:** Get notified when downloads complete
- **System Monitoring:** Create alerts for system issues
- **Backup Automation:** Schedule and monitor backup processes
- **User Management:** Automate user account creation

## Configuration

### Environment Variables
Key settings in `.env`:
- `SUBDOMAIN`: Subdomain for n8n access
- `DOMAIN_NAME`: Your main domain
- `SSL_EMAIL`: Email for SSL certificates
- `GENERIC_TIMEZONE`: Your timezone

### Data Persistence
- Workflows stored in `n8n_data` volume
- Local files accessible in `/files` directory
- Configuration preserved across restarts

## Security

### Built-in Security
- HTTPS with automatic SSL certificates
- User authentication and session management
- API key management for external access
- Secure webhook handling

### Best Practices
- Use strong passwords for admin account
- Regularly update n8n to latest version
- Monitor workflow executions for unusual activity
- Limit webhook access to trusted sources

## Troubleshooting

### Common Issues
- **SSL Certificate Issues:** Check domain and email configuration
- **Workflow Failures:** Review node configurations and connections
- **Webhook Problems:** Verify URL accessibility and firewall settings
- **Performance Issues:** Monitor resource usage and optimize workflows

### Logs
```bash
docker compose logs -f n8n
```

### Reset Admin Password
```bash
docker compose exec n8n n8n user-management:reset --email=your@email.com
```

## Advanced Features

### Custom Code
- JavaScript/TypeScript in Function nodes
- Access to npm packages
- Custom data transformations
- Complex business logic implementation

### Executions
- Manual and automatic execution
- Execution history and logs
- Error handling and retry logic
- Performance monitoring

## Resources

- **Website:** https://n8n.io
- **Documentation:** https://docs.n8n.io
- **GitHub:** https://github.com/n8n-io/n8n
- **Community:** https://community.n8n.io
- **Templates:** https://n8n.io/workflows