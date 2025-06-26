# Recruit8N Setup Guide

## Overview

Recruit8N is an automated recruitment workflow system built with n8n that processes CVs, matches candidates to jobs, and manages your recruitment pipeline using Google Drive and Google Sheets.

## Prerequisites

### Required Accounts & Services
- n8n instance (cloud or self-hosted)
- Google Account with Google Drive and Sheets access
- OpenAI API account
- SMTP email service (Gmail, Outlook, or dedicated service)

### Required APIs
- Google Drive API
- Google Sheets API  
- OpenAI API
- SMTP credentials

## Step 1: Google Drive Setup

### 1.1 Create Folder Structure

Create the following folder structure in your Google Drive:

```
📁 Recruitment Agency/
├── 📁 01-Incoming CVs/
│   ├── 📁 New-Unprocessed/
│   ├── 📁 Processing/
│   └── 📁 Processed/
├── 📁 02-Job Vacancies/
│   ├── 📁 Active-Jobs/
│   └── 📁 Archived-Jobs/
├── 📁 03-Candidates/
│   ├── 📁 Suitable-Matches/
│   ├── 📁 Alternative-Matches/
│   └── 📁 Rejected/
├── 📁 04-Client-Files/
│   └── 📁 [Client-Name]/
└── 📁 05-Reports-Analytics/
```

### 1.2 Get Folder IDs

1. Navigate to each folder in Google Drive
2. Copy the folder ID from the URL (after `/folders/`)
3. Save these IDs - you'll need them for workflow configuration

## Step 2: Google Sheets Setup

### 2.1 Create Spreadsheets

Create the following Google Sheets in your Google Drive:

1. **Candidates Database**
2. **Job Vacancies**  
3. **Match Results**
4. **Alternative Matches**
5. **Reverse Matches**

### 2.2 Configure Sheet Columns

Use the schema templates in `/schemas/` to set up your sheet columns:

#### Candidates Sheet
- id, name, email, phone, address, occupation, skills, experience_years, education, cv_path, date_added, status, notes

#### Active Jobs Sheet  
- job_id, title, company, location, requirements, preferred_skills, description, experience_required, education_required, salary_range, job_type, remote_option, industry, client_contact, status, date_posted, deadline, file_link, notes

#### Match Results Sheet
- match_id, candidate_email, job_id, suitability_percentage, reasoning, key_matches, gaps, recommendation, confidence_level, date_processed, processed_by, status, recruiter_notes

#### Alternative Matches Sheet
- alt_match_id, candidate_email, original_job_id, alternative_jobs, top_recommendation_job_id, confidence_score, reasoning, transferable_skills, skill_gaps, analysis_date, status, recruiter_action, notes

#### Reverse Matches Sheet
- reverse_match_id, job_id, job_title, company, suitable_candidates, top_candidate_email, top_candidate_score, total_matches_found, total_evaluated, match_summary, recommended_actions, analysis_date, notification_sent, recruiter_reviewed, status, notes

### 2.3 Get Sheet IDs

1. Open each Google Sheet
2. Copy the sheet ID from the URL (between `/d/` and `/edit`)
3. Save these IDs for workflow configuration

## Step 3: n8n Configuration

### 3.1 Install n8n

Choose one of the following:

**Option A: n8n Cloud**
1. Sign up at [n8n.cloud](https://n8n.cloud)
2. Create a new instance

**Option B: Self-Hosted**
```bash
npm install -g n8n
n8n start
```

### 3.2 Configure Credentials

In n8n, set up the following credentials:

#### Google API Credentials
1. Go to [Google Cloud Console](https://console.cloud.google.com)
2. Create a new project or select existing
3. Enable Google Drive API and Google Sheets API
4. Create OAuth 2.0 credentials
5. Add credentials in n8n: Credentials → Add → Google → OAuth2

#### OpenAI API Credentials
1. Get API key from [OpenAI](https://platform.openai.com/api-keys)
2. Add in n8n: Credentials → Add → OpenAI → API Key

#### SMTP Credentials
1. Configure your email service (Gmail, Outlook, etc.)
2. Add in n8n: Credentials → Add → SMTP

### 3.3 Import Workflows

1. In n8n, go to Workflows → Import from File
2. Import the following workflow files:
   - `workflows/main-cv-processing.json`
   - `workflows/alternative-job-matching.json`
   - `workflows/reverse-candidate-matching.json`

### 3.4 Configure Workflow Parameters

For each imported workflow, update the following:

#### Folder IDs
Replace placeholder folder IDs with your actual Google Drive folder IDs:
- `1YourFolderIdHere` → Your actual folder ID
- `1ProcessingFolderIdHere` → Your Processing folder ID
- etc.

#### Sheet IDs
Replace placeholder sheet IDs with your actual Google Sheets IDs:
- `1YourCandidatesSheetIdHere` → Your Candidates sheet ID
- `1YourJobVacanciesSheetIdHere` → Your Job Vacancies sheet ID
- etc.

#### Credential IDs
Replace placeholder credential IDs with your actual n8n credential IDs:
- `your-google-api-credential-id`
- `your-google-sheets-credential-id`
- `your-openai-credential-id`
- `your-smtp-credential-id`

## Step 4: Testing & Validation

### 4.1 Test CV Processing

1. Upload a test CV to the "New-Unprocessed" folder
2. Monitor the workflow execution in n8n
3. Verify data appears in Google Sheets
4. Check that CV is moved to appropriate folder

### 4.2 Test Job Matching

1. Add a job description to the "Active-Jobs" folder
2. Verify reverse matching workflow executes
3. Check match results in Google Sheets
4. Confirm notification emails are sent

### 4.3 Test Alternative Matching

1. Manually trigger alternative matching workflow
2. Verify alternative job suggestions are generated
3. Check results in Alternative Matches sheet

## Step 5: Customization

### 5.1 Adjust AI Prompts

- Modify prompts in `/templates/cv-parsing-prompts.json`
- Update matching criteria and scoring thresholds
- Customize output formats

### 5.2 Email Templates

- Customize email templates in `/templates/email-templates.json`
- Update branding and styling
- Modify notification triggers

### 5.3 Workflow Scheduling

- Set up scheduled triggers for batch processing
- Configure alternative matching frequency
- Adjust notification timing

## Step 6: Monitoring & Maintenance

### 6.1 Monitor Workflow Executions

- Check n8n execution history regularly
- Set up error notifications
- Monitor API usage and limits

### 6.2 Data Quality

- Review AI matching accuracy
- Validate extracted CV data
- Maintain clean candidate database

### 6.3 Performance Optimization

- Monitor workflow execution times
- Optimize prompts for accuracy
- Adjust batch processing sizes

## Troubleshooting

### Common Issues

#### Workflow Not Triggering
- Check Google Drive trigger permissions
- Verify folder IDs are correct
- Ensure credentials are valid

#### AI Parsing Errors
- Verify OpenAI API key and credits
- Check prompt formatting
- Review input data quality

#### Google Sheets Errors
- Confirm sheet IDs and permissions
- Check column names match exactly
- Verify data types and formats

#### Email Not Sending
- Test SMTP credentials
- Check email templates for syntax errors
- Verify recipient addresses

### Support Resources

- n8n Documentation: [docs.n8n.io](https://docs.n8n.io)
- n8n Community: [community.n8n.io](https://community.n8n.io)
- OpenAI API Docs: [platform.openai.com/docs](https://platform.openai.com/docs)
- Google APIs: [developers.google.com](https://developers.google.com)

## Next Steps

1. **Production Deployment**: Move from testing to production environment
2. **Team Training**: Train your recruitment team on the new system
3. **Process Integration**: Integrate with existing recruitment processes
4. **Performance Monitoring**: Set up monitoring and analytics
5. **Continuous Improvement**: Regular review and optimization

---

**Need Help?** 
Refer to the configuration files in `/config/` for detailed parameter settings and troubleshooting guides.