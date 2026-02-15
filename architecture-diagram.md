# AWS Architecture Diagram
## AI-Based Scholarship & Financial Aid Eligibility Platform

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              CLIENT LAYER                                    │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐                      │
│  │   Students   │  │  Mobile App  │  │  Voice UI    │                      │
│  │   (Web)      │  │              │  │              │                      │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘                      │
└─────────┼──────────────────┼──────────────────┼────────────────────────────┘
          │                  │                  │
          └──────────────────┴──────────────────┘
                             │
┌────────────────────────────▼─────────────────────────────────────────────────┐
│                    CONTENT DELIVERY & API LAYER                              │
│  ┌─────────────────────────────────────────────────────────────────┐        │
│  │  Amazon CloudFront (CDN)                                         │        │
│  │  - Global edge locations                                         │        │
│  │  - Static content caching                                        │        │
│  │  - Low latency for Indian users                                 │        │
│  └─────────────────────────┬───────────────────────────────────────┘        │
│                            │                                                 │
│  ┌─────────────────────────▼───────────────────────────────────────┐        │
│  │  AWS Amplify (Frontend Hosting)                                 │        │
│  │  - React/React Native app                                       │        │
│  │  - CI/CD pipeline                                               │        │
│  └─────────────────────────┬───────────────────────────────────────┘        │
│                            │                                                 │
│  ┌─────────────────────────▼───────────────────────────────────────┐        │
│  │  Amazon API Gateway                                             │        │
│  │  - RESTful API endpoints                                        │        │
│  │  - Request throttling & caching                                 │        │
│  │  - CORS configuration                                           │        │
│  └─────────────────────────┬───────────────────────────────────────┘        │
│                            │                                                 │
│  ┌─────────────────────────▼───────────────────────────────────────┐        │
│  │  Amazon Cognito (Authentication)                                │        │
│  │  - User pools & JWT tokens                                      │        │
│  │  - Email/phone verification                                     │        │
│  └─────────────────────────┬───────────────────────────────────────┘        │
└────────────────────────────┼──────────────────────────────────────────────────┘
                             │
┌────────────────────────────▼─────────────────────────────────────────────────┐
│                      SERVERLESS COMPUTE LAYER                                │
│                         (AWS Lambda Functions)                               │
│                                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐   │
│  │   Profile    │  │  Eligibility │  │    Search    │  │ Translation  │   │
│  │  Management  │  │   Matching   │  │   Service    │  │   Service    │   │
│  │              │  │              │  │              │  │              │   │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘   │
│         │                 │                 │                 │            │
│  ┌──────▼───────┐  ┌──────▼───────┐  ┌──────▼───────┐  ┌──────▼───────┐   │
│  │    Voice     │  │ Notification │  │    Admin     │  │  Explanation │   │
│  │  Interface   │  │   Service    │  │   Service    │  │  Generator   │   │
│  │              │  │              │  │              │  │              │   │
│  └──────────────┘  └──────────────┘  └──────────────┘  └──────────────┘   │
│                                                                              │
└────────────────────────────┬─────────────────────────────────────────────────┘
                             │
        ┌────────────────────┼────────────────────┐
        │                    │                    │
┌───────▼────────┐  ┌────────▼────────┐  ┌───────▼────────┐
│   AI/ML        │  │   DATA          │  │  MONITORING    │
│   SERVICES     │  │   STORAGE       │  │  & ALERTS      │
└────────────────┘  └─────────────────┘  └────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                          AI/ML SERVICES LAYER                                │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────┐        │
│  │  Amazon Bedrock (Foundation Models)                             │        │
│  │  - Claude/Titan for eligibility matching                        │        │
│  │  - Natural language explanation generation                      │        │
│  │  - Complex rule interpretation                                  │        │
│  └─────────────────────────────────────────────────────────────────┘        │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────┐        │
│  │  Amazon Translate                                               │        │
│  │  - 7+ Indian languages (Hindi, Tamil, Telugu, Bengali, etc.)   │        │
│  │  - Real-time translation                                        │        │
│  └─────────────────────────────────────────────────────────────────┘        │
│                                                                              │
│  ┌──────────────────────────┐  ┌──────────────────────────┐                │
│  │  Amazon Polly            │  │  Amazon Transcribe       │                │
│  │  - Text-to-speech        │  │  - Speech-to-text        │                │
│  │  - Indian voices         │  │  - Voice accessibility   │                │
│  └──────────────────────────┘  └──────────────────────────┘                │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                        DATA STORAGE LAYER                                    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────┐        │
│  │  Amazon DynamoDB (NoSQL Database)                               │        │
│  │                                                                  │        │
│  │  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────┐ │        │
│  │  │ Student Profiles │  │  Scholarships    │  │ Translation  │ │        │
│  │  │ Table            │  │  Table           │  │ Cache Table  │ │        │
│  │  │                  │  │                  │  │              │ │        │
│  │  │ - userId (PK)    │  │ - scholarshipId  │  │ - textHash   │ │        │
│  │  │ - profile data   │  │ - eligibility    │  │ - language   │ │        │
│  │  │                  │  │ - GSI indexes    │  │ - TTL 30d    │ │        │
│  │  └──────────────────┘  └──────────────────┘  └──────────────┘ │        │
│  │                                                                  │        │
│  │  ┌──────────────────────────────────────────────────────────┐  │        │
│  │  │ DynamoDB Streams → Lambda → OpenSearch Sync              │  │        │
│  │  └──────────────────────────────────────────────────────────┘  │        │
│  └─────────────────────────────────────────────────────────────────┘        │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────┐        │
│  │  Amazon OpenSearch                                              │        │
│  │  - Full-text search across scholarships                        │        │
│  │  - Advanced filtering (amount, deadline, category)             │        │
│  │  - Real-time indexing via DynamoDB Streams                     │        │
│  └─────────────────────────────────────────────────────────────────┘        │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────┐        │
│  │  Amazon S3 (Object Storage)                                     │        │
│  │  - Document templates & guidance materials                      │        │
│  │  - Audio files (voice input/output)                            │        │
│  │  - Bulk import files (CSV/JSON)                                │        │
│  │  - Intelligent-Tiering for cost optimization                   │        │
│  └─────────────────────────────────────────────────────────────────┘        │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                    MONITORING & NOTIFICATION LAYER                           │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────┐        │
│  │  Amazon CloudWatch                                              │        │
│  │  - Centralized logging (JSON format)                           │        │
│  │  - Custom metrics (users, matches, searches)                   │        │
│  │  - Dashboards & alarms                                         │        │
│  │  - 30-day log retention                                        │        │
│  └─────────────────────────────────────────────────────────────────┘        │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────┐        │
│  │  Amazon EventBridge                                             │        │
│  │  - Scheduled deadline reminders (7-day, 1-day)                 │        │
│  │  - Event-driven architecture                                   │        │
│  │  - Triggers notification Lambda                                │        │
│  └─────────────────────────────────────────────────────────────────┘        │
│                                                                              │
│  ┌──────────────────────────┐  ┌──────────────────────────┐                │
│  │  Amazon SNS              │  │  Amazon SES              │                │
│  │  - Push notifications    │  │  - Email notifications   │                │
│  │  - System alerts         │  │  - Deadline reminders    │                │
│  └──────────────────────────┘  └──────────────────────────┘                │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                         SECURITY & IAM LAYER                                 │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────┐        │
│  │  AWS IAM (Identity & Access Management)                         │        │
│  │  - Least-privilege policies for Lambda functions               │        │
│  │  - Service-to-service authentication                           │        │
│  │  - Role-based access control                                   │        │
│  └─────────────────────────────────────────────────────────────────┘        │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────┐        │
│  │  AWS KMS (Key Management Service)                               │        │
│  │  - Encryption at rest for DynamoDB                             │        │
│  │  - S3 bucket encryption                                        │        │
│  └─────────────────────────────────────────────────────────────────┘        │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘
```

## Key Data Flows

### 1. Student Registration & Profile Creation
```
Student → CloudFront → Amplify → API Gateway → Cognito (Auth)
→ Profile Lambda → DynamoDB (StudentProfiles) → Response
```

### 2. Eligibility Matching
```
Student → API Gateway → Cognito (Auth) → Matching Lambda
→ DynamoDB (Profiles + Scholarships) → Bedrock (AI Evaluation)
→ Ranked Results → Response
```

### 3. Multilingual Content
```
Student selects language → Translation Lambda
→ Check DynamoDB Cache → (if miss) Amazon Translate
→ Store in Cache (TTL 30d) → Translated Response
```

### 4. Voice Interaction
```
Student speaks → Voice Lambda → Upload to S3
→ Amazon Transcribe → Text → Process Command
→ Amazon Polly → Audio Response → S3 → Student
```

### 5. Deadline Reminders
```
Admin creates scholarship → EventBridge schedules reminders
→ (7 days before) EventBridge triggers Notification Lambda
→ Query DynamoDB (NotificationPreferences)
→ Send via SNS (push) + SES (email)
```

### 6. Search with Eligibility
```
Student searches → Search Lambda → OpenSearch (full-text)
→ Results → Evaluate eligibility per result
→ Mark eligible/not eligible → Sorted Response
```

## AWS Services Summary

| Service | Purpose | Key Features |
|---------|---------|--------------|
| **Amazon Bedrock** | AI matching & explanations | Foundation models, no training required |
| **Amazon Translate** | Multilingual support | 7+ Indian languages |
| **Amazon Polly** | Text-to-speech | Indian voices |
| **Amazon Transcribe** | Speech-to-text | Voice accessibility |
| **AWS Lambda** | Serverless compute | Auto-scaling, pay-per-use |
| **API Gateway** | API management | Throttling, caching, auth |
| **Amazon Cognito** | User authentication | JWT tokens, social login |
| **Amazon DynamoDB** | NoSQL database | Single-digit ms latency |
| **Amazon OpenSearch** | Full-text search | Advanced filtering |
| **Amazon S3** | Object storage | Documents, audio files |
| **Amazon CloudFront** | CDN | Global edge locations |
| **AWS Amplify** | Frontend hosting | CI/CD pipeline |
| **Amazon CloudWatch** | Monitoring | Logs, metrics, alarms |
| **Amazon EventBridge** | Event scheduling | Deadline reminders |
| **Amazon SNS** | Push notifications | Mobile alerts |
| **Amazon SES** | Email service | Deadline emails |
| **AWS IAM** | Access control | Least-privilege policies |
| **AWS KMS** | Encryption | Data security |

## Cost Optimization Strategies

1. **DynamoDB On-Demand**: Pay only for actual reads/writes
2. **Lambda Reserved Concurrency**: For predictable workloads
3. **CloudFront Caching**: Reduce origin requests
4. **Translation Caching**: Store in DynamoDB (30-day TTL)
5. **S3 Intelligent-Tiering**: Automatic cost optimization

## Scalability Features

- **Lambda**: Auto-scales to 1000 concurrent executions
- **DynamoDB**: Auto-scaling based on traffic
- **API Gateway**: 10,000 req/s per account
- **CloudFront**: Global CDN with Indian edge locations
- **OpenSearch**: Horizontal scaling by adding nodes
