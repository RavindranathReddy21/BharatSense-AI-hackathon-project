# BharatSense AI – System Design Document

## 1. System Architecture Overview

BharatSense AI follows a serverless, event-driven architecture on AWS to ensure scalability, cost-efficiency, and reliability for small and medium enterprise retailers.

### 1.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         Frontend Layer                          │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  React Web App (AWS Amplify)                             │  │
│  │  - Conversational UI                                     │  │
│  │  - Forecast Visualization                                │  │
│  │  - Dashboard                                             │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │ HTTPS/REST
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                         API Gateway Layer                       │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Amazon API Gateway                                      │  │
│  │  - Authentication & Authorization                        │  │
│  │  - Rate Limiting                                         │  │
│  │  - Request Validation                                    │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Application Layer (Lambda)                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐ │
│  │   Copilot    │  │  Forecasting │  │  Price Intelligence  │ │
│  │   Service    │  │   Service    │  │      Service         │ │
│  │              │  │              │  │                      │ │
│  │  - NL Query  │  │  - ML Models │  │  - Price Analysis    │ │
│  │  - LLM Call  │  │  - Demand    │  │  - Anomaly Detection │ │
│  │  - Response  │  │    Forecast  │  │  - Margin Optimizer  │ │
│  └──────────────┘  └──────────────┘  └──────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                         AI/ML Layer                             │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Amazon Bedrock                                          │  │
│  │  - Claude/Llama for NLU                                  │  │
│  │  - Prompt Engineering                                    │  │
│  │  - Explainable Reasoning                                 │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Data Layer                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐   │
│  │  Amazon S3   │  │  DynamoDB    │  │  Amazon Athena       │   │
│  │              │  │              │  │                      │   │
│  │  - Sales     │  │  - Store     │  │  - Analytics Queries │   │
│  │    History   │  │    Metadata  │  │  - Historical        │   │
│  │  - Forecasts │  │  - User Data │  │    Analysis          │   │
│  └──────────────┘  └──────────────┘  └──────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                   External Data Sources                         │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐   │
│  │  Government  │  │   Festival   │  │  Weather APIs        │   │
│  │  Price APIs  │  │   Calendar   │  │  (Optional)          │   │
│  └──────────────┘  └──────────────┘  └──────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

## 2. Component Design

### 2.1 Frontend Layer

**Technology**: React.js hosted on AWS Amplify

**Components**:
- Conversational Chat Interface
- Forecast Dashboard
- Product Catalog View
- Price Intelligence Panel
- Settings & Configuration

**Key Features**:
- Mobile-first responsive design
- Progressive Web App (PWA) capabilities
- Optimistic UI updates
- Offline-ready (future enhancement)

**State Management**: React Context API or Redux for global state

**API Communication**: Axios/Fetch with retry logic and error handling

### 2.2 API Gateway Layer

**Service**: Amazon API Gateway (REST API)

**Endpoints**:

```
POST   /api/v1/chat              - Submit conversational query
GET    /api/v1/forecast/{sku}    - Get demand forecast for product
GET    /api/v1/forecast/store    - Get all forecasts for store
POST   /api/v1/forecast/generate - Trigger forecast generation
GET    /api/v1/prices/{sku}      - Get price intelligence
GET    /api/v1/insights          - Get business insights
POST   /api/v1/store/data        - Upload sales data
GET    /api/v1/store/profile     - Get store information
```

**Security**:
- AWS IAM authentication
- API keys for store identification
- Request throttling (100 requests/minute per store)
- Input validation and sanitization

### 2.3 Application Services (AWS Lambda)

#### 2.3.1 Copilot Service

**Purpose**: Handle natural language queries and generate conversational responses

**Runtime**: Python 3.11

**Key Functions**:
```python
def handle_chat_query(query, store_id, context):
    """
    Process natural language query and return response
    
    Steps:
    1. Parse user intent
    2. Retrieve relevant store data (anonymized)
    3. Call forecasting/price services if needed
    4. Generate LLM prompt with context
    5. Call Amazon Bedrock
    6. Format and return response
    """
    pass

def anonymize_data(raw_data):
    """
    Mask sensitive information before LLM processing
    - Remove store names, owner details
    - Aggregate transaction-level data
    - Keep only statistical summaries
    """
    pass
```

**LLM Integration**:
- Model: Claude 3 Sonnet or Llama 3 via Bedrock
- Prompt template with retail context
- Temperature: 0.3 (for consistent, factual responses)
- Max tokens: 500

**Data Privacy**:
- Only aggregated metrics sent to LLM
- No raw POS data exposed
- Store identifiers masked

#### 2.3.2 Forecasting Service

**Purpose**: Generate demand forecasts using ML models

**Runtime**: Python 3.11 with scikit-learn, statsmodels

**Key Functions**:
```python
def generate_forecast(store_id, sku, horizon_days=7):
    """
    Generate demand forecast for specific product
    
    Steps:
    1. Load historical sales data from S3
    2. Apply time series preprocessing
    3. Run forecasting model (SARIMA/Prophet)
    4. Incorporate seasonal/festival factors
    5. Calculate confidence intervals
    6. Store results in S3
    7. Return forecast with explanation
    """
    pass

def explain_forecast(forecast_data, influencing_factors):
    """
    Generate human-readable explanation of forecast
    - Identify key drivers (seasonality, trends, festivals)
    - Highlight confidence level
    - Provide actionable recommendations
    """
    pass
```

**ML Models**:
- SARIMA (Seasonal AutoRegressive Integrated Moving Average)
- Prophet (Facebook's time series forecasting)
- Model selection based on data characteristics

**Features Used**:
- Historical sales (last 90-365 days)
- Day of week
- Month/season
- Festival indicators
- Price changes
- Weather (optional)

**Output Format**:
```json
{
  "sku": "MILK-001",
  "forecast": [
    {"date": "2026-02-15", "quantity": 45, "confidence": "high"},
    {"date": "2026-02-16", "quantity": 42, "confidence": "high"}
  ],
  "confidence_level": "high",
  "explanation": "Demand expected to be stable. Weekend pattern shows slight decrease on Sundays.",
  "factors": ["day_of_week", "historical_trend"],
  "recommendation": "Stock 45 units for tomorrow"
}
```

#### 2.3.3 Price Intelligence Service

**Purpose**: Analyze pricing and provide optimization recommendations

**Runtime**: Python 3.11

**Key Functions**:
```python
def analyze_price(store_id, sku):
    """
    Analyze product pricing
    
    Steps:
    1. Get current store price
    2. Fetch government/market prices
    3. Calculate price position
    4. Detect anomalies
    5. Suggest margin optimization
    """
    pass

def detect_price_anomalies(sku, current_price, market_prices):
    """
    Identify unusual pricing situations
    - Significantly above/below market
    - Sudden price changes
    - Margin compression
    """
    pass
```

**Data Sources**:
- Government APIs (Ministry of Consumer Affairs, AGMARKNET)
- Historical store pricing
- Category benchmarks

**Output Format**:
```json
{
  "sku": "RICE-001",
  "current_price": 65,
  "market_avg": 62,
  "position": "slightly_above",
  "margin": 15,
  "recommendation": "Price is competitive. Consider maintaining current level.",
  "sources": ["Ministry of Consumer Affairs", "AGMARKNET"]
}
```

### 2.4 Data Layer

#### 2.4.1 Amazon S3 Storage

**Buckets**:

1. `bharatsense-sales-data-{env}`
   - Historical POS data (CSV/Parquet)
   - Partitioned by: store_id/year/month/
   - Lifecycle: Archive to Glacier after 2 years

2. `bharatsense-forecasts-{env}`
   - Generated forecasts (JSON)
   - Partitioned by: store_id/date/
   - Lifecycle: Delete after 90 days

3. `bharatsense-external-data-{env}`
   - Government price data
   - Festival calendar
   - Weather data (if used)

**Data Format**:
- Sales data: Parquet (columnar, compressed)
- Forecasts: JSON
- Metadata: JSON

#### 2.4.2 Amazon DynamoDB

**Tables**:

1. `Stores`
   - Partition Key: store_id
   - Attributes: name, location, category, owner_contact (encrypted), created_at
   - GSI: location-index

2. `Users`
   - Partition Key: user_id
   - Attributes: email (encrypted), role, store_id, last_login
   - GSI: store_id-index

3. `Products`
   - Partition Key: store_id
   - Sort Key: sku
   - Attributes: name, category, unit, current_price, last_updated

4. `QueryHistory`
   - Partition Key: store_id
   - Sort Key: timestamp
   - Attributes: query, response, latency
   - TTL: 30 days

#### 2.4.3 Amazon Athena

**Purpose**: Ad-hoc analytics and historical analysis

**Use Cases**:
- Aggregate sales trends across time periods
- Category-level performance analysis
- Forecast accuracy measurement
- Data quality checks

**Sample Queries**:
```sql
-- Calculate forecast accuracy
SELECT 
  sku,
  AVG(ABS(actual - forecast) / actual) as mape
FROM forecast_accuracy
WHERE date >= DATE_ADD('day', -30, CURRENT_DATE)
GROUP BY sku;
```

### 2.5 AI/ML Layer

#### 2.5.1 Amazon Bedrock Integration

**Model Selection**:
- Primary: Claude 3 Sonnet (balanced performance/cost)
- Fallback: Llama 3 70B

**Prompt Engineering**:

```python
SYSTEM_PROMPT = """
You are BharatSense AI, an expert retail advisor for small businesses in India.
Your role is to help store owners make better inventory and pricing decisions.

Guidelines:
- Provide clear, actionable recommendations
- Explain your reasoning in simple terms
- Use Indian retail context and terminology
- Be concise and business-focused
- Cite data sources when making claims
- Never expose sensitive business data

Current Context:
- Store Type: {store_type}
- Location: {location}
- Product Categories: {categories}
"""

USER_PROMPT_TEMPLATE = """
Store Owner Question: {user_query}

Available Data (Aggregated):
- Recent Sales Trend: {sales_summary}
- Forecast: {forecast_summary}
- Market Prices: {price_summary}

Provide a helpful response with:
1. Direct answer to the question
2. Supporting data/reasoning
3. Actionable recommendation
"""
```

**Data Anonymization Before LLM**:
```python
def prepare_llm_context(store_data):
    """
    Anonymize data before sending to LLM
    """
    return {
        "sales_summary": {
            "trend": "increasing",  # Not raw numbers
            "top_products": ["category_a", "category_b"],  # Not SKUs
            "avg_daily_transactions": "50-100"  # Range, not exact
        },
        "forecast_summary": {
            "outlook": "stable",
            "confidence": "high"
        }
    }
```

**Response Post-Processing**:
- Validate response format
- Add citations/sources
- Format for UI display
- Log for quality monitoring

## 3. Data Flow Diagrams

### 3.1 Conversational Query Flow

```
User → Frontend → API Gateway → Copilot Lambda
                                      ↓
                                Parse Intent
                                      ↓
                          ┌───────────┴───────────┐
                          ↓                       ↓
                  Forecasting Service      Price Service
                          ↓                       ↓
                    Retrieve Data          Retrieve Data
                          ↓                       ↓
                          └───────────┬───────────┘
                                      ↓
                              Anonymize Data
                                      ↓
                              Amazon Bedrock
                                      ↓
                              Format Response
                                      ↓
                          API Gateway → Frontend → User
```

### 3.2 Forecast Generation Flow

```
Scheduled Event / API Trigger
            ↓
    Forecasting Lambda
            ↓
    Load Historical Data (S3)
            ↓
    Preprocess & Feature Engineering
            ↓
    Run ML Model (SARIMA/Prophet)
            ↓
    Apply Seasonal/Festival Adjustments
            ↓
    Calculate Confidence Intervals
            ↓
    Generate Explanation
            ↓
    Store Forecast (S3)
            ↓
    Update DynamoDB Metadata
            ↓
    Return Results
```

### 3.3 Price Intelligence Flow

```
API Request
      ↓
Price Intelligence Lambda
      ↓
Get Store Price (DynamoDB)
      ↓
Fetch Government Data (External API)
      ↓
Calculate Price Position
      ↓
Detect Anomalies
      ↓
Generate Recommendations
      ↓
Return Response with Sources
```

## 4. Security Design

### 4.1 Authentication & Authorization

**Authentication**:
- AWS Cognito for user authentication
- JWT tokens for API access
- API keys for store identification

**Authorization**:
- Role-Based Access Control (RBAC)
  - Store Owner: Full access to own store data
  - Admin: System management
- IAM policies for service-to-service communication

### 4.2 Data Protection

**Encryption**:
- In Transit: TLS 1.3 for all API calls
- At Rest: 
  - S3: AES-256 encryption
  - DynamoDB: AWS managed encryption
  - Sensitive fields: Application-level encryption (KMS)

**Data Masking**:
- PII (owner contact, email) encrypted in DynamoDB
- Store names/identifiers masked before LLM calls
- Only aggregated metrics exposed to AI models

**Access Control**:
- S3 bucket policies: Deny public access
- DynamoDB: Fine-grained access control
- Lambda: Least privilege IAM roles

### 4.3 Privacy Compliance

**Data Handling**:
- Store data isolated per store_id
- No cross-store data sharing
- Data retention policies enforced
- User consent for data usage

**LLM Data Policy**:
- No raw POS data sent to LLMs
- Only statistical summaries used
- No personally identifiable information
- Audit logs for all LLM calls

## 5. Scalability Design

### 5.1 Horizontal Scaling

**Lambda Auto-Scaling**:
- Concurrent execution limit: 1000 per function
- Reserved concurrency for critical functions
- Provisioned concurrency for low-latency requirements

**API Gateway**:
- Handles 10,000 requests/second by default
- Regional deployment for low latency

### 5.2 Data Partitioning

**S3 Partitioning Strategy**:
```
s3://bharatsense-sales-data/
  ├── store_id=STORE001/
  │   ├── year=2026/
  │   │   ├── month=01/
  │   │   │   └── sales.parquet
  │   │   └── month=02/
  │   │       └── sales.parquet
```

**Benefits**:
- Efficient data retrieval
- Parallel processing
- Cost-effective queries

### 5.3 Caching Strategy

**CloudFront CDN**:
- Cache static frontend assets
- Edge locations for low latency

**Application-Level Caching**:
- Forecast results cached for 24 hours
- Government price data cached for 6 hours
- DynamoDB DAX for hot data (future)

## 6. Performance Optimization

### 6.1 Response Time Targets

| Operation | Target | Strategy |
|-----------|--------|----------|
| Chat Query | < 3s | Async LLM calls, cached data |
| Forecast Generation | < 5s | Optimized ML models, parallel processing |
| Dashboard Load | < 2s | CDN, lazy loading, pagination |
| Price Check | < 1s | Cached government data |

### 6.2 Optimization Techniques

**Lambda Optimization**:
- Warm start: Provisioned concurrency for critical functions
- Memory allocation: 1024MB (balanced CPU/memory)
- Code optimization: Minimize cold start time

**Data Access**:
- Parquet format for columnar access
- Athena query optimization
- DynamoDB query patterns optimized

**Frontend**:
- Code splitting
- Lazy loading components
- Image optimization
- Service worker for caching

## 7. Monitoring & Observability

### 7.1 Logging

**CloudWatch Logs**:
- Lambda execution logs
- API Gateway access logs
- Application-level structured logging

**Log Levels**:
- ERROR: System failures, exceptions
- WARN: Degraded performance, retries
- INFO: Key business events
- DEBUG: Detailed troubleshooting (dev only)

### 7.2 Metrics

**Key Metrics**:
- API latency (p50, p95, p99)
- Lambda duration and errors
- Forecast accuracy (MAPE)
- LLM response quality
- User engagement (queries per day)

**CloudWatch Dashboards**:
- System health overview
- Per-store analytics
- Cost tracking
- Error rates

### 7.3 Alerting

**Critical Alerts**:
- API error rate > 5%
- Lambda timeout rate > 1%
- Forecast generation failures
- External API failures

**Notification**: SNS → Email/Slack

### 7.4 Tracing

**AWS X-Ray**:
- End-to-end request tracing
- Service map visualization
- Performance bottleneck identification

## 8. Cost Optimization

### 8.1 Serverless Benefits

**Pay-per-use Model**:
- No idle infrastructure costs
- Automatic scaling
- No over-provisioning

### 8.2 Cost Estimates (Per Store/Month)

| Service | Usage | Estimated Cost |
|---------|-------|----------------|
| Lambda | 10,000 requests | $0.20 |
| API Gateway | 10,000 requests | $0.04 |
| Bedrock | 100 LLM calls | $2.00 |
| S3 | 1GB storage | $0.02 |
| DynamoDB | On-demand | $0.50 |
| **Total** | | **~$3-5/month** |

### 8.3 Cost Control Strategies

- S3 lifecycle policies (archive old data)
- DynamoDB on-demand pricing (MVP)
- Lambda memory optimization
- Bedrock prompt optimization (reduce tokens)
- CloudWatch log retention (30 days)

## 9. Disaster Recovery

### 9.1 Backup Strategy

**Data Backups**:
- S3: Versioning enabled, cross-region replication (production)
- DynamoDB: Point-in-time recovery enabled
- Backup retention: 30 days

### 9.2 Recovery Objectives

- RTO (Recovery Time Objective): 4 hours
- RPO (Recovery Point Objective): 1 hour

### 9.3 Failure Scenarios

| Scenario | Impact | Mitigation |
|----------|--------|------------|
| Lambda failure | Service degradation | Automatic retry, fallback logic |
| S3 outage | Data access failure | Cross-region replication |
| Bedrock unavailable | Chat disabled | Cached responses, fallback model |
| External API down | Price data stale | Cached data, graceful degradation |

## 10. Deployment Strategy

### 10.1 Environments

1. **Development**: Feature development and testing
2. **Staging**: Pre-production validation
3. **Production**: Live system

### 10.2 CI/CD Pipeline

```
Code Commit → GitHub
      ↓
GitHub Actions
      ↓
  ┌───┴───┐
  ↓       ↓
Lint    Test
  ↓       ↓
  └───┬───┘
      ↓
Build & Package
      ↓
Deploy to Dev
      ↓
Integration Tests
      ↓
Deploy to Staging
      ↓
Manual Approval
      ↓
Deploy to Production
      ↓
Smoke Tests
```

### 10.3 Infrastructure as Code

**AWS CDK** (Cloud Development Kit) in Python:
- Define all infrastructure as code
- Version controlled
- Repeatable deployments
- Easy rollback

### 10.4 Deployment Process

**Lambda Deployment**:
- Blue/green deployment
- Gradual traffic shifting (10% → 50% → 100%)
- Automatic rollback on errors

**Frontend Deployment**:
- Amplify automatic deployment from Git
- Preview environments for PRs

## 11. Testing Strategy

### 11.1 Unit Tests

- Lambda function logic
- Data processing functions
- ML model components
- Coverage target: 80%

### 11.2 Integration Tests

- API endpoint testing
- Service-to-service communication
- External API mocking
- Database operations

### 11.3 End-to-End Tests

- User journey testing
- Chat flow validation
- Forecast generation pipeline
- Price intelligence workflow

### 11.4 Performance Tests

**Load Testing**:
- Simulate 100 concurrent users
- Sustained load for 10 minutes
- Measure response times and error rates

**Stress Testing**:
- Push system beyond normal capacity
- Identify breaking points
- Validate auto-scaling behavior

**Regression Testing**:
- Automated test suite
- Run on every deployment
- Ensure no functionality breaks

### 11.5 AI/ML Testing

- Forecast accuracy validation
- LLM response quality checks
- Prompt effectiveness testing
- Model drift detection

## 12. MVP Implementation Plan

### Phase 1: Foundation (Week 1)
- Set up AWS infrastructure (CDK)
- Create S3 buckets, DynamoDB tables
- Deploy API Gateway
- Basic Lambda functions

### Phase 2: Core Services (Week 2)
- Implement forecasting service
- Integrate Amazon Bedrock
- Build copilot service
- Price intelligence service

### Phase 3: Frontend (Week 3)
- React app development
- Chat interface
- Forecast visualization
- Dashboard

### Phase 4: Integration & Testing (Week 4)
- End-to-end integration
- Load synthetic data
- Testing and bug fixes
- Documentation

### Phase 5: Demo Preparation (Week 5)
- Demo scenarios
- Performance optimization
- Presentation materials
- Final testing

## 13. Future Enhancements

### 13.1 Phase 2 Features
- WhatsApp bot integration
- Voice interface (regional languages)
- Live POS integration
- Mobile app (React Native)

### 13.2 Advanced Analytics
- Cross-store benchmarking
- Supplier relationship management
- Dynamic pricing automation
- Predictive restocking

### 13.3 Scale Improvements
- Multi-region deployment
- GraphQL API
- Real-time data streaming (Kinesis)
- Advanced caching (ElastiCache)
