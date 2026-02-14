# BharatSense AI – AI Decision Copilot for Bharat Retail

## 1. Problem Statement

India has over 12 million kirana stores that operate without structured data intelligence systems. Retailers rely on intuition for:

- Inventory planning  
- Pricing decisions  
- Seasonal demand estimation  
- Festival-based stocking  

This results in:
- Overstocking and wastage  
- Missed demand opportunities  
- Reduced profit margins  
- Working capital inefficiencies  

Small retailers lack access to affordable, AI-powered analytics tailored to their needs.

## 2. Objective

Build an AI-powered decision copilot that:

- Enhances retail decision-making  
- Improves operational efficiency  
- Provides explainable demand and pricing insights  
- Works through natural language interaction  
- Is scalable across India's retail ecosystem  

## 3. Target Users

### Primary Users
- Kirana store owners  
- Small and medium retailers  

### Secondary Users (Future Expansion)
- FMCG distributors  
- D2C brands  
- Marketplace sellers  

## 4. Functional Requirements

### 4.1 Conversational AI Copilot
- Users can ask natural language questions such as:
  - "How much milk should I stock tomorrow?"
  - "Why did biscuit sales drop last week?"
- The system generates contextual and explainable responses.

### 4.2 Demand Forecasting Engine
- Use historical sales data
- Incorporate:
  - Seasonality
  - Festival signals
  - Weather signals
- Provide SKU-level forecasts
- Display confidence levels with forecasts

### 4.3 Price Intelligence
- Integrate publicly available government price data
- Detect pricing anomalies
- Suggest margin optimization opportunities

### 4.4 Explainable AI Insights
- Every recommendation must include reasoning
- Outputs must be simple and business-friendly
- Avoid technical jargon in retailer-facing explanations

### 4.5 Data Integration (MVP Scope)
- Synthetic POS data
- Public commodity price datasets
- Festival calendar data
- Weather data signals

## 5. Non-Functional Requirements

### 5.1 Scalability
- Support onboarding of multiple stores
- Handle large SKU datasets

### 5.2 Performance
- Copilot response time under 3 seconds

### 5.3 Reliability
- Serverless, fault-tolerant architecture

### 5.4 Security
- Secure API access
- Role-based access control

### 5.5 Cost Efficiency
- Optimized AWS usage suitable for small retailers

## 6. AI Requirements

The system must:

- Use ML-based time series forecasting
- Use LLM reasoning via Amazon Bedrock
- Provide explainable AI outputs
- Avoid purely rule-based static logic
- Support multilingual expansion capability

AI is necessary because:
- Retail demand is nonlinear and seasonal
- Decision reasoning must adapt dynamically
- Natural language interaction improves accessibility
- Insights must be interpretable and context-aware

## 7. Technology Stack (AWS)

- Amazon Bedrock – Generative AI reasoning
- AWS Lambda – Forecasting logic
- Amazon S3 – Data storage
- Amazon Athena – Analytics layer
- API Gateway – API exposure
- AWS Amplify – Frontend hosting

## 8. MVP Scope

The hackathon MVP will demonstrate:

- Simulated kirana dataset
- Demand forecast visualization
- Conversational AI interaction
- Explainable output reasoning
- Hyperlocal seasonal impact simulation

## 9. Future Roadmap

- Regional language voice support
- WhatsApp-based copilot
- Live POS integration
- Distributor-level intelligence expansion
