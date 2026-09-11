# TalentFlow Recruitment Automation V1.0

An AI-powered recruitment automation system built with n8n, Supabase, OpenAI, Gmail, Google Drive, and Slack.

TalentFlow automates candidate intake, CV processing, job-specific AI screening, human review, recruitment requirement management, and recruiter assignment while keeping human intervention available for uncertain screening decisions.

## Overview

TalentFlow was designed to automate the repetitive operational work involved in early-stage recruitment.

Instead of manually processing every application, recruiters can use TalentFlow to receive candidates through email, extract and structure CV information, retrieve the correct requirements for the position, evaluate the applicant using AI, and automatically route the candidate based on the evaluation result.

The system also includes separate workflows for maintaining recruitment requirements and handling human review decisions.

Hiring managers can update job requirements through structured emails without modifying the automation itself. These requirements are stored in a vector database and retrieved during candidate evaluation using RAG.

Candidates who meet the configured screening thresholds can automatically pass the initial screening, while uncertain or lower-confidence evaluations can be escalated to recruiters through Slack.

After screening, the system can also assign candidates to recruitment agents based on specialization, availability, and workload capacity.

## What the System Does

### Candidate Processing

- Receives candidate applications through Gmail
- Extracts CV attachments
- Preserves applicant files in Google Drive
- Extracts and structures candidate information
- Identifies the position being applied for
- Creates candidate and application records
- Retrieves job-specific recruitment requirements
- Evaluates candidates using AI and RAG
- Generates match and confidence scores
- Identifies missing recruitment criteria
- Produces structured AI recommendations

### Automated Screening

- Uses AI match and confidence scores to control the screening flow
- Automatically passes candidates who meet the configured high-confidence thresholds
- Routes uncertain or lower-confidence evaluations for human review
- Prevents every application from requiring manual recruiter intervention

### Human Review

- Sends candidates requiring review to Slack
- Provides recruiters with candidate and AI evaluation information
- Supports interactive recruiter actions
- Processes recruiter decisions through a separate webhook workflow
- Updates the candidate's application status based on the decision

### Recruitment Requirements Management

- Allows hiring managers to submit or update job requirements through structured email
- Extracts and processes recruitment requirement documents
- Identifies the corresponding job position
- Replaces outdated requirement information
- Generates embeddings for updated requirements
- Stores recruitment knowledge in a Supabase vector database
- Makes updated requirements immediately available to future candidate evaluations

### Recruitment Agent Assignment

- Maintains recruitment agent information in Supabase
- Tracks recruiter specialization
- Tracks maximum candidate capacity
- Tracks current active candidate workload
- Checks recruiter availability and status
- Assigns screened candidates to suitable recruitment agents
- Updates recruiter workload after assignment

## System Architecture

TalentFlow is composed of several connected automation components that handle different stages of the recruitment process.

```text
                    HIRING MANAGER
                          |
                          | Structured Requirements Email
                          v
                        Gmail
                          |
                          v
              Requirements Management
                     Workflow
                          |
                 Extract / Process
                 Update Requirements
                          |
                          v
                Supabase Vector Store
                          |
                          | RAG Retrieval
                          |
                          +-------------------------+
                                                    |
                                                    v
APPLICANT                                     Job Requirements
   |                                                |
   | Application + CV                               |
   v                                                |
 Gmail                                              |
   |                                                |
   v                                                |
Candidate Intake & Processing <---------------------+
   |
   | Extract CV
   | Structure Candidate Data
   | Identify Position
   v
AI Candidate Evaluation
   |
   | Match Score
   | Confidence Score
   | Recommendation
   | Explanation
   | Missing Criteria
   v
Screening Decision
   |
   +----------------------+----------------------+
   |                                             |
High-Confidence Result                     Review Required
   |                                             |
   v                                             v
Automatic Initial Pass                      Slack Review
                                                 |
                                                 | Recruiter Action
                                                 v
                                        Human Review Workflow
                                                 |
                                       Approve / Reject /
                                         Manual Review
                                                 |
                         +-----------------------+
                         |
                         v
                 Screened Candidate
                         |
                         v
                Agent Assignment Logic
                         |
              +----------+----------+
              |                     |
       Specialization          Capacity /
            Check             Availability
              |                     |
              +----------+----------+
                         |
                         v
               Recruitment Agent
                    Assignment
                         |
                         v
                Update Agent Workload
```

## Core Workflow

1. A hiring manager defines or updates recruitment requirements through a structured email.
2. The requirements workflow processes the submitted document and identifies the corresponding job position.
3. Previous requirement information is replaced or updated in the vector database.
4. Embeddings are generated so the latest requirements can be retrieved using RAG.
5. A candidate submits an application and CV through Gmail.
6. TalentFlow extracts the CV and structures the applicant information.
7. The system identifies the position the candidate applied for.
8. The relevant job requirements are retrieved from the Supabase vector store.
9. The AI evaluates the candidate against those requirements.
10. TalentFlow generates a match score, confidence score, recommendation, explanation, and missing criteria.
11. Candidates meeting the configured high-confidence screening thresholds automatically pass the initial screening.
12. Candidates requiring additional review are escalated to Slack.
13. Recruiter actions are processed by the separate human-review workflow.
14. Screened candidates continue to the recruitment agent assignment process.
15. TalentFlow checks recruiter specialization, status, availability, and workload capacity.
16. A suitable recruitment agent is selected and the corresponding workload information is updated.

## Tech Stack

| Technology | Purpose |
|---|---|
| n8n | Workflow orchestration, branching, integrations, and automation logic |
| Gmail | Applicant intake and hiring-manager requirement submissions |
| Supabase | Recruitment database and operational data storage |
| Supabase Vector Store | Recruitment requirement storage and RAG retrieval |
| OpenAI | Candidate classification, evaluation, embeddings, and structured AI outputs |
| Slack | Human review and recruiter decision actions |
| Google Drive | CV and recruitment requirement file management |
| PostgreSQL | Structured recruitment data management |
| JavaScript | Custom data transformation and workflow logic |
| REST APIs / HTTP Requests | Communication with external services |
| Webhooks | Processing interactive actions and workflow events |

## Technical Implementation

### 1. Recruitment Requirements Management

TalentFlow includes a dedicated workflow that allows hiring managers to maintain recruitment requirements without modifying the main candidate automation.

When requirements for a position need to be created or changed, the hiring manager sends a structured email containing the updated recruitment criteria.

The workflow receives the email through Gmail, extracts the attached requirements document, identifies the corresponding position, processes the information, and updates the recruitment knowledge stored in Supabase.

Outdated requirement information for the position is removed or replaced before the latest criteria are stored.

Embeddings are generated for the updated information and stored in the Supabase vector store.

As a result, future applicants can automatically be evaluated against the latest requirements without requiring changes to the candidate evaluation workflow.

### 2. Candidate Intake and CV Processing

The main workflow begins when an applicant submits their application and CV through Gmail.

TalentFlow extracts the CV attachment and preserves the original applicant file in Google Drive.

The workflow then processes the CV and converts the unstructured applicant information into structured candidate data.

Candidate information can include:

- Full name
- Email
- Phone number
- Location
- Years of experience
- Skills
- Education
- Certifications
- Previous positions
- CV summary
- Position applied for
- Candidate ID
- Application ID
- Original CV reference

The system also identifies the specific position being applied for so the applicant can be evaluated against the correct recruitment criteria.

### 3. Job-Specific RAG Retrieval

TalentFlow uses Retrieval-Augmented Generation to provide the AI evaluator with the correct recruitment requirements.

After identifying the candidate's position, the workflow queries the Supabase vector store and retrieves the relevant job requirements.

This separates recruitment knowledge from the AI evaluation prompt.

Instead of permanently hard-coding job criteria into the automation, the evaluator receives the latest requirements stored for the relevant position.

This also allows multiple positions to use the same overall evaluation architecture while maintaining different recruitment criteria.

### 4. AI Candidate Evaluation

The structured candidate information and retrieved job requirements are provided to the AI evaluation stage.

The evaluator compares the candidate's qualifications with the requirements for the selected position.

The result is returned using a structured output containing:

- `ai_match_score`
- `ai_confidence_score`
- `ai_recommendation`

The **AI match score** represents how closely the candidate matches the recruitment requirements for the position.

The **AI confidence score** represents how confident the AI is in its evaluation based on the available candidate information and retrieved job requirements.

The **AI recommendation** provides the suggested screening outcome based on the candidate's qualifications and the relevant recruitment criteria.

These structured results are then used by the workflow to determine whether the candidate automatically passes the initial screening or is routed for human review.

### 5. Automated Screening Decision

TalentFlow uses both the AI match score and AI confidence score to determine how an application proceeds.

Candidates with:

- AI match score `> 80`
- AI confidence score `> 80`

automatically pass the initial screening stage and continue through the automated recruitment process.

Candidates with:

- AI match score `<= 80`
- AI confidence score `<= 80`

are routed to Slack for human review.

This allows high-confidence, strongly matched candidates to move forward automatically while lower-scoring and lower-confidence evaluations are escalated to a recruiter for manual assessment.

### 6. Conditional Human Review

Candidates requiring additional review are sent to Slack.

The Slack notification provides the recruiter with relevant information about the application and AI evaluation, including candidate identifiers, position, evaluation scores, recommendation, and access to the original CV.

The recruiter can then review the application and select the appropriate action.

The purpose of this branch is to use human attention where it is most valuable instead of requiring manual review for every application.

### 7. Human Review Decision Processing

Slack interactions are handled by a separate TalentFlow human-review workflow.

When a recruiter selects an action, the interaction is sent to an n8n webhook.

The workflow extracts the selected action and candidate information, determines the appropriate branch, and updates the application accordingly.

This creates a separation between AI evaluation and human authorization.

### 8. Recruitment Agent Assignment

TalentFlow also automates the operational process of assigning screened candidates to recruitment agents.

Recruitment agent information is maintained in Supabase, including:

- Agent ID
- Agent name
- Agent email
- Job specialization
- Maximum active candidates
- Current active candidates
- Agent status

When a candidate reaches the agent-assignment stage, TalentFlow can identify recruiters associated with the relevant job specialization.

The system checks whether the recruiter is active and whether they still have capacity based on their configured maximum number of active candidates.

An eligible recruitment agent can then be selected and assigned to the candidate.

The agent's workload information is updated after assignment, allowing future assignments to take current workload into account.

This prevents candidates from being routed to inactive recruiters or recruiters who have already reached their configured capacity.

### 9. Application and Recruitment Data Management

TalentFlow uses structured database tables to separate different parts of the recruitment system.

The database maintains information for areas such as:

- Candidates
- Applications
- Recruitment agents
- Job requirements

Separating these records allows the automation to track candidate information, application progress, recruitment criteria, and recruiter workload independently while still connecting them throughout the workflow.

## AI and Human Review Safeguards

TalentFlow is designed to use AI for initial screening and decision support rather than treating an AI evaluation as a final hiring decision.

The system includes several controls:

- Candidates are evaluated against job-specific requirements retrieved through RAG
- Recruitment requirements can be maintained independently from the AI workflow
- AI evaluations use structured outputs
- Match and confidence scores are evaluated separately
- Missing criteria are explicitly identified
- Lower-confidence or configured review cases can be escalated to recruiters
- Human decisions are handled through a separate review workflow
- Recruiter workload and specialization are checked before candidate assignment

## Business Value

TalentFlow demonstrates how recruitment teams can automate repetitive screening and coordination work while retaining human oversight where necessary.

The system can help a recruitment operation:

- Reduce manual CV screening
- Process incoming applications consistently
- Automatically match candidates against position-specific criteria
- Keep recruitment requirements up to date without editing automation workflows
- Reduce unnecessary recruiter reviews for high-confidence screening results
- Escalate uncertain applications for human judgment
- Distribute candidates according to recruiter specialization and capacity
- Reduce manual recruiter assignment and workload tracking
- Maintain structured candidate and application records throughout the process

## V1.0 Known Limitation

TalentFlow V1.0 has one known missing step at the end of the successful AI evaluation path.

After a candidate passes the automated screening, the workflow currently does not include the final `POST` HTTP Request that creates the candidate record in the `Evaluated CVs` table.

The screening and evaluation logic itself is complete. The missing step only affects the final database persistence of candidates who successfully pass the evaluation.

### Planned Fix

Add the final `POST` HTTP Request:

AI Evaluation → Pass Screening → Create Record in `Evaluated CVs`

## Planned Future Improvements

### TalentFlow V2.0 - Client-Friendly Rework

Rather than simply extending the current V1.0 workflow, a future version of TalentFlow is planned as a usability-focused rework.

The goal is to make the system easier for non-technical recruitment teams to understand, operate, and maintain without requiring them to understand the underlying n8n implementation.

Planned improvements include:

- Simplifying and organizing the workflow architecture
- Using clearer workflow sections and node naming
- Making configurable recruitment settings easier to identify
- Separating client-editable settings from technical automation logic
- Improving error handling and operational visibility
- Completing the missing `POST` operation for successfully evaluated candidates
- Adding automated applicant acknowledgement emails
- Creating an SOP for day-to-day operation and maintenance

### Standard Operating Procedure (SOP)

A future version will include an SOP designed for non-technical users.

The SOP will explain how to:

- Add or update recruitment requirements
- Change screening criteria and thresholds
- Manage recruitment agents
- Update agent capacity and availability
- Review candidates requiring human intervention
- Understand candidate and application statuses
- Handle common workflow errors
- Verify that the automation is operating correctly
- Identify which settings can safely be changed without modifying the core automation

The objective is to make TalentFlow easier to hand over to a client after implementation and reduce their dependency on the automation developer for routine operational changes.
