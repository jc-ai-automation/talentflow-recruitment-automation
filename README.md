## What the System Does

### Applicant Processing

- Receives job applications through Gmail
- Extracts applicant information and CV details
- Identifies the position being applied for
- Retrieves the relevant job requirements from the vector database using RAG
- Evaluates candidates against the correct job criteria
- Generates an AI match score and confidence score
- Produces approve, reject, or needs review recommendations
- Explains the reasoning behind each evaluation
- Identifies missing candidate requirements
- Stores candidate and application data
- Sends structured candidate evaluations to Slack
- Allows recruiters to approve or reject candidates through Slack actions
- Tracks application status throughout the recruitment process

### Recruitment Requirements Management

TalentFlow also includes a separate management workflow that allows hiring managers to update recruitment requirements without manually editing the automation.

When a hiring manager wants to create or change the criteria for a position, they send an email using a predefined structured format.

The management workflow automatically:

- Receives the manager's requirements through Gmail
- Extracts and validates the submitted job criteria
- Identifies the corresponding job position
- Processes the updated recruitment requirements
- Updates the job requirements stored in the vector database
- Makes the new requirements available to the candidate evaluation workflow

This allows hiring criteria to be maintained independently from the main applicant-processing workflow.

When future applicants are evaluated, the AI retrieves the latest relevant requirements from the vector database rather than relying on hard-coded criteria inside the workflow.

## System Architecture

TalentFlow is composed of three connected automation components:

1. **Recruitment Requirements Management**  
   Allows hiring managers to update job criteria through structured email submissions.

2. **Candidate Processing and AI Evaluation**  
   Processes applications, extracts candidate information, retrieves the correct job requirements using RAG, and evaluates candidates.

3. **Human Review and Decision Processing**  
   Sends candidate evaluations to Slack and processes recruiter decisions such as approve, reject, or needs review.

```text
HIRING MANAGER
      |
      | Structured Requirements Email
      v
Gmail
      |
      v
Recruitment Requirements Workflow
      |
      | Extract / Classify / Update Criteria
      v
Supabase Vector Store
      |
      | Latest Job Requirements
      |
      +-----------------------------+
                                    |
                                    v
APPLICANT                     RAG Retrieval
   |                                |
   | Application + CV               |
   v                                |
Gmail                               |
   |                                |
   v                                |
Candidate Processing Workflow <-----+
   |
   | Extract CV / Classify Position
   | Normalize Candidate Data
   v
AI Candidate Evaluation
   |
   | Match Score
   | Confidence Score
   | Recommendation
   | Explanation
   | Missing Criteria
   v
Slack Human Review
   |
   | Recruiter Action
   v
Human Review Webhook Workflow
   |
   +-------------+-------------+
   |             |             |
 Approve       Reject     Needs Review
   |             |             |
   +-------------+-------------+
                 |
                 v
        Application Status Update
```

## Core Workflow

1. A hiring manager sends recruitment requirements through a predefined structured email format.
2. The requirements workflow extracts and processes the submitted criteria.
3. Existing requirements for the relevant position are updated in the Supabase vector database.
4. A candidate submits an application and CV through Gmail.
5. TalentFlow extracts the CV and candidate information.
6. The system identifies the position being applied for.
7. RAG retrieves the latest relevant recruitment requirements from the vector store.
8. The AI evaluates the candidate against those requirements.
9. The system generates a match score, confidence score, recommendation, explanation, and missing criteria.
10. The candidate evaluation is sent to Slack for human review.
11. A recruiter approves, rejects, or flags the application for further review.
12. The Slack action is processed by the human-review webhook workflow.
13. The candidate's application status is updated based on the recruiter's decision.

## Tech Stack

| Technology | Purpose |
|---|---|
| n8n | Workflow orchestration, branching logic, data transformation, and integrations |
| Gmail | Applicant intake and hiring-manager requirement submissions |
| Supabase | Candidate data storage and vector database |
| Supabase Vector Store | Storage and retrieval of recruitment requirements for RAG |
| OpenAI | Candidate evaluation, classification, and structured AI outputs |
| Slack | Human review, recruiter notifications, and approval/rejection actions |
| Google Drive | CV and recruitment requirement file handling |
| PostgreSQL | Supporting structured recruitment data |
| JavaScript | Custom transformation and workflow logic |
| Webhooks / HTTP Requests | Communication between workflow components and interactive actions |
