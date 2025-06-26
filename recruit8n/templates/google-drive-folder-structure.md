# Google Drive Folder Structure for Recruit8N

## Overview
This document provides the complete folder structure needed for the Recruit8N automation system. Follow this structure exactly to ensure proper workflow functionality.

## Complete Folder Structure

```
📁 Recruitment Agency/
├── 📁 01-Incoming CVs/
│   ├── 📁 New-Unprocessed/           # CVs uploaded here trigger automatic processing
│   ├── 📁 Processing/                # Temporary folder during CV processing
│   └── 📁 Processed/                 # Archive of all processed CVs
│       ├── 📁 Suitable-Matches/      # CVs with 70%+ job match
│       ├── 📁 Alternative-Matches/   # CVs with 40-69% match for other roles
│       └── 📁 Rejected/              # CVs with <40% match
├── 📁 02-Job Vacancies/
│   ├── 📁 Active-Jobs/               # New job descriptions uploaded here
│   └── 📁 Archived-Jobs/             # Filled or cancelled positions
├── 📁 03-Candidates/
│   ├── 📁 Suitable-Matches/          # High-priority candidate files
│   ├── 📁 Alternative-Matches/       # Alternative placement candidates
│   ├── 📁 Rejected/                  # Non-suitable candidates
│   └── 📁 Under-Review/              # Candidates being evaluated by recruiters
├── 📁 04-Client-Files/
│   ├── 📁 TechCorp-Inc/              # Individual client folders
│   ├── 📁 Growth-Solutions/
│   ├── 📁 Healthcare-Plus/
│   └── 📁 [Add-More-Clients]/
├── 📁 05-Reports-Analytics/
│   ├── 📁 Monthly-Reports/
│   ├── 📁 Client-Reports/
│   ├── 📁 Performance-Metrics/
│   └── 📁 Backup-Data/
└── 📁 06-Templates-Resources/
    ├── 📁 CV-Templates/
    ├── 📁 Job-Description-Templates/
    ├── 📁 Email-Templates/
    └── 📁 Contract-Templates/
```

## Folder Descriptions & Usage

### 01-Incoming CVs/
**Purpose**: Central processing hub for all candidate CVs

#### New-Unprocessed/
- **Function**: Drop zone for new CVs
- **Trigger**: Files uploaded here automatically start processing workflow
- **Access**: Recruiters upload CVs here
- **File Types**: PDF, DOC, DOCX, RTF, TXT
- **Naming Convention**: `Candidate-Name_Position_Date.pdf`

#### Processing/
- **Function**: Temporary holding during workflow execution
- **Access**: System only (automated)
- **Duration**: Files stay here 1-5 minutes during processing
- **Purpose**: Prevents duplicate processing

#### Processed/
- **Function**: Archive of all processed CVs organized by outcome
- **Structure**: Sub-folders based on matching results
- **Retention**: Keep for historical tracking and re-evaluation

### 02-Job Vacancies/
**Purpose**: Management of job descriptions and requirements

#### Active-Jobs/
- **Function**: New job descriptions trigger candidate matching
- **Trigger**: Files uploaded here start reverse matching workflow
- **File Types**: PDF, DOC, DOCX with job descriptions
- **Naming Convention**: `Company_Position_Date.pdf`

#### Archived-Jobs/
- **Function**: Completed, filled, or cancelled positions
- **Organization**: By month/year for easy retrieval
- **Purpose**: Historical reference and pattern analysis

### 03-Candidates/
**Purpose**: Organized candidate files by status and suitability

#### Suitable-Matches/
- **Criteria**: 70%+ match score candidates
- **Priority**: High priority for immediate contact
- **Action Required**: Interview scheduling

#### Alternative-Matches/
- **Criteria**: 40-69% match for different roles
- **Purpose**: Future opportunities and career development
- **Action Required**: Alternative job presentation

#### Rejected/
- **Criteria**: <40% match score
- **Purpose**: Historical record
- **Action**: No immediate action required

#### Under-Review/
- **Purpose**: Candidates being actively evaluated by recruiters
- **Duration**: Temporary during assessment process
- **Move To**: Appropriate outcome folder after review

### 04-Client-Files/
**Purpose**: Client-specific documentation and correspondence

**Structure**: Individual folder per client containing:
- Contract documents
- Job specifications
- Placement records
- Communication history
- Invoicing documents

### 05-Reports-Analytics/
**Purpose**: Business intelligence and performance tracking

#### Monthly-Reports/
- Automated system performance reports
- Placement statistics
- AI accuracy metrics

#### Client-Reports/
- Client-specific placement summaries
- Candidate pipeline reports
- Custom analytics

#### Performance-Metrics/
- System performance data
- Workflow execution logs
- Error reports and resolutions

#### Backup-Data/
- Automated data backups
- Configuration backups
- Historical data exports

### 06-Templates-Resources/
**Purpose**: Standard templates and resources for consistency

#### CV-Templates/
- Standard CV formats for candidates
- CV improvement guidelines

#### Job-Description-Templates/
- Standard job posting formats
- Client-specific templates

#### Email-Templates/
- Candidate communication templates
- Client communication templates

#### Contract-Templates/
- Placement agreement templates
- Service contracts

## Folder Permissions & Access

### Recruiter Access Levels
- **Read/Write**: All folders except system processing folders
- **Upload Only**: New-Unprocessed, Active-Jobs
- **Read Only**: Processed archives, reports

### System Access
- **Full Access**: Processing folders, database updates
- **Read Access**: All source folders
- **Write Access**: Result folders, archives

### Client Access (Optional)
- **Limited Read**: Their specific client folder only
- **Upload**: Job descriptions to designated folder
- **Download**: Reports and placement summaries

## Setup Instructions

### Step 1: Create Root Folder
1. Create main "Recruitment Agency" folder in Google Drive
2. Set sharing permissions for your team
3. Note the folder ID for configuration

### Step 2: Create Sub-Folders
1. Create all folders according to the structure above
2. Maintain exact naming (case-sensitive)
3. Record each folder ID for workflow configuration

### Step 3: Set Permissions
1. Configure appropriate access levels
2. Set up team member permissions
3. Configure system service account access

### Step 4: Configure Workflows
1. Update workflow configuration with folder IDs
2. Test each trigger folder
3. Verify file movement between folders

## Folder ID Collection

For each folder, collect the Google Drive folder ID (found in the URL):

```
Folder Name                     | Folder ID
-------------------------------|------------------
New-Unprocessed               | [YOUR_ID_HERE]
Processing                    | [YOUR_ID_HERE]
Suitable-Matches              | [YOUR_ID_HERE]
Alternative-Matches           | [YOUR_ID_HERE]
Rejected                      | [YOUR_ID_HERE]
Active-Jobs                   | [YOUR_ID_HERE]
Archived-Jobs                 | [YOUR_ID_HERE]
```

## Maintenance Guidelines

### Daily Tasks
- Monitor New-Unprocessed for stuck files
- Clear Processing folder if files remain
- Review Suitable-Matches for immediate action

### Weekly Tasks
- Archive old processed CVs
- Clean up temporary files
- Review folder organization

### Monthly Tasks
- Archive completed client projects
- Generate backup copies
- Review and optimize folder structure

## Troubleshooting

### Common Issues

#### Files Not Processing
- Check file is in correct trigger folder
- Verify file format is supported
- Ensure workflow has folder access permissions

#### Wrong Folder Movement
- Review match score thresholds
- Check workflow logic configuration
- Verify folder ID mappings

#### Permission Errors
- Confirm service account has access
- Check folder sharing settings
- Verify team member permissions

### Best Practices

1. **Consistent Naming**: Use standardized file naming conventions
2. **Regular Cleanup**: Archive old files regularly
3. **Access Control**: Maintain appropriate permission levels
4. **Backup Strategy**: Regular backup of important folders
5. **Documentation**: Keep folder ID records updated

---

**Note**: This folder structure is designed to work seamlessly with the Recruit8N n8n workflows. Any changes to folder names or structure will require corresponding updates to the workflow configuration.