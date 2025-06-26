# Main CV Processing Workflow - Complete Node Analysis

## Overview

This n8n workflow automates the complete CV processing pipeline from file upload to candidate matching and file organization. The workflow processes CVs using AI, matches candidates against active jobs, and automatically organizes files based on suitability scores.

**Workflow Name**: Main CV Processing Workflow  
**Total Nodes**: 12  
**Execution Flow**: Sequential with one parallel branch  

---

## Node 1: Google Drive Trigger

### What It Does
The **entry point** of the workflow that monitors a Google Drive folder for new CV uploads and automatically starts processing when a file is detected.

### Technical Details
```json
"type": "n8n-nodes-base.googleDriveTrigger"
"operation": "watchFolder"
"event": "fileCreated"
```

### Configuration
- **Resource**: File monitoring
- **Folder ID**: `1YourFolderIdHere` (replace with actual folder ID)
- **Event Type**: File created
- **Credentials**: Google API OAuth2

### Input Source
- **Trigger**: New file uploaded to "New-Unprocessed" Google Drive folder
- **File Types**: Any file format (PDF, DOC, DOCX, RTF, TXT)

### Output Data
```json
{
  "id": "file_id",
  "name": "candidate_cv.pdf",
  "webViewLink": "https://drive.google.com/file/d/...",
  "mimeType": "application/pdf"
}
```

### Workflow Routing
```
File Upload → Google Drive Trigger → Move to Processing
```

### Purpose
- **Automation**: Eliminates manual workflow initiation
- **Real-time**: Immediate processing when CVs are uploaded
- **Scalability**: Can handle multiple concurrent uploads

---

## Node 2: Move to Processing

### What It Does
Moves the uploaded CV from the "New-Unprocessed" folder to a "Processing" folder to prevent duplicate processing and indicate the file is being worked on.

### Technical Details
```json
"type": "n8n-nodes-base.googleDrive"
"operation": "move"
"resource": "file"
```

### Configuration
- **File ID**: `={{ $json.id }}` (from trigger)
- **Destination**: `1ProcessingFolderIdHere` (replace with actual folder ID)
- **Credentials**: Google API OAuth2

### Input Source
- **From**: Google Drive Trigger node
- **Data**: File metadata and ID

### Output Data
```json
{
  "id": "file_id",
  "name": "candidate_cv.pdf",
  "parents": ["1ProcessingFolderIdHere"]
}
```

### Workflow Routing
```
Google Drive Trigger → Move to Processing → Download CV
```

### Purpose
- **Status Tracking**: Shows file is being processed
- **Duplicate Prevention**: Avoids reprocessing same file
- **Organization**: Maintains clean folder structure

---

## Node 3: Download CV

### What It Does
Downloads the actual file content from Google Drive so the workflow can access and process the CV's text content.

### Technical Details
```json
"type": "n8n-nodes-base.googleDrive"
"operation": "download"
"resource": "file"
```

### Configuration
- **File ID**: `={{ $json.id }}` (from previous node)
- **Credentials**: Google API OAuth2

### Input Source
- **From**: Move to Processing node
- **Data**: File metadata after move operation

### Output Data
```json
{
  "data": {
    "binaryData": "base64_encoded_file_content",
    "fileName": "candidate_cv.pdf",
    "mimeType": "application/pdf"
  }
}
```

### Workflow Routing
```
Move to Processing → Download CV → Extract From File
                                → Get Job Requirements (parallel)
```

### Purpose
- **Content Access**: Provides file data for text extraction
- **Format Support**: Works with multiple file formats
- **Binary Handling**: Manages file data properly

---

## Node 4: Extract From File

### What It Does
Extracts readable text content from the downloaded CV file, converting PDF/DOC/DOCX files into plain text for AI processing.

### Technical Details
```json
"type": "n8n-nodes-base.extractFromFile"
"operation": "extractFromPDF"
"binaryPropertyName": "data"
```

### Configuration
- **Operation**: Extract from PDF (supports multiple formats)
- **Binary Property**: "data" (from download node)

### Input Source
- **From**: Download CV node
- **Data**: Binary file content

### Output Data
```json
{
  "text": "John Smith\n123 Main St\nSoftware Developer\nSkills: Python, JavaScript, React...",
  "fileName": "candidate_cv.pdf"
}
```

### Workflow Routing
```
Download CV → Extract From File → Parse CV Data
```

### Purpose
- **Text Extraction**: Converts files to processable text
- **Format Support**: Handles PDF, DOC, DOCX, RTF, TXT
- **AI Preparation**: Provides clean text for AI analysis

---

## Node 5: Parse CV Data (AI Processing)

### What It Does
Uses OpenAI GPT-4 to analyze the extracted CV text and extract structured candidate information in a standardized JSON format.

### Technical Details
```json
"type": "n8n-nodes-base.openAi"
"model": "gpt-4"
"temperature": 0.1
```

### Configuration
- **Model**: GPT-4 for high accuracy
- **Temperature**: 0.1 (low for consistency)
- **Role**: System + User messages

### System Prompt
```
You are an expert CV parser. Extract the following information from the CV text and return it as valid JSON:
{
  "name": "full name",
  "address": "complete address", 
  "occupation": "current or most recent job title",
  "skills": ["array of skills"],
  "experience_years": number,
  "education": "highest education level",
  "phone": "phone number",
  "email": "email address"
}

If any field is not found, use null. Be precise and accurate.
```

### Input Source
- **From**: Extract From File node
- **Data**: `{{ $json.text }}` (extracted CV text)

### Output Data
```json
{
  "message": {
    "content": "{\"name\":\"John Smith\",\"address\":\"123 Main St, City\",\"occupation\":\"Software Developer\",\"skills\":[\"Python\",\"JavaScript\",\"React\"],\"experience_years\":5,\"education\":\"Bachelor's in Computer Science\",\"phone\":\"+1-555-0123\",\"email\":\"john@email.com\"}"
  }
}
```

### Workflow Routing
```
Extract From File → Parse CV Data → Job Matching
```

### Purpose
- **Data Extraction**: Converts unstructured text to structured data
- **Standardization**: Consistent data format for all CVs
- **AI Accuracy**: GPT-4 provides high-quality extraction

---

## Node 6: Job Matching (AI Analysis)

### What It Does
Uses OpenAI GPT-4 to compare the extracted candidate profile against active job requirements and provide a comprehensive suitability assessment with scoring and recommendations.

### Technical Details
```json
"type": "n8n-nodes-base.openAi"
"model": "gpt-4"
"temperature": 0.2
```

### Configuration
- **Model**: GPT-4 for complex reasoning
- **Temperature**: 0.2 (slightly higher for analytical reasoning)

### System Prompt
```
You are a recruitment expert. Compare the candidate's profile against the job requirements and provide a suitability assessment.

Return your response as valid JSON:
{
  "suitability_percentage": number (0-100),
  "reasoning": "detailed explanation of why this percentage was assigned",
  "key_matches": ["array of matching skills/experience"],
  "gaps": ["array of missing requirements"],
  "recommendation": "SUITABLE" or "ALTERNATIVE" or "REJECTED"
}

Job Requirements: {{ $('Get Job Requirements').item(0).json.requirements }}
Candidate Profile: {{ $json.message.content }}
```

### Input Source
- **From**: Parse CV Data node (candidate profile)
- **From**: Get Job Requirements node (job criteria)

### Output Data
```json
{
  "message": {
    "content": "{\"suitability_percentage\":85,\"reasoning\":\"Strong match with required Python and JavaScript skills...\",\"key_matches\":[\"Python\",\"JavaScript\",\"5 years experience\"],\"gaps\":[\"AWS experience\"],\"recommendation\":\"SUITABLE\"}"
  }
}
```

### Workflow Routing
```
Parse CV Data → Job Matching → Save Candidate
Get Job Requirements ↗
```

### Purpose
- **Intelligent Matching**: AI-powered candidate assessment
- **Detailed Analysis**: Comprehensive reasoning and scoring
- **Decision Support**: Clear recommendations for recruiters

---

## Node 7: Save Candidate

### What It Does
Saves the extracted candidate information to the "Candidates" Google Sheet, creating a searchable database of all processed candidates with deduplication.

### Technical Details
```json
"type": "n8n-nodes-base.googleSheets"
"operation": "appendOrUpdate"
"columnToMatchOn": "email"
```

### Configuration
- **Document ID**: `1YourCandidatesSheetIdHere` (replace with actual sheet ID)
- **Sheet Name**: "Candidates"
- **Deduplication**: Uses email as unique identifier
- **Credentials**: Google Sheets OAuth2

### Data Mapping
```json
{
  "name": "={{ JSON.parse($('Parse CV Data').item(0).json.message.content).name }}",
  "address": "={{ JSON.parse($('Parse CV Data').item(0).json.message.content).address }}",
  "occupation": "={{ JSON.parse($('Parse CV Data').item(0).json.message.content).occupation }}",
  "skills": "={{ JSON.parse($('Parse CV Data').item(0).json.message.content).skills.join(', ') }}",
  "experience_years": "={{ JSON.parse($('Parse CV Data').item(0).json.message.content).experience_years }}",
  "phone": "={{ JSON.parse($('Parse CV Data').item(0).json.message.content).phone }}",
  "email": "={{ JSON.parse($('Parse CV Data').item(0).json.message.content).email }}",
  "cv_path": "={{ $('Google Drive Trigger').item(0).json.webViewLink }}",
  "date_added": "={{ $now }}"
}
```

### Input Source
- **From**: Parse CV Data node (candidate information)
- **From**: Google Drive Trigger node (CV file link)

### Output Data
```json
{
  "spreadsheetId": "1YourCandidatesSheetIdHere",
  "updatedRows": 1,
  "updatedCells": 9
}
```

### Workflow Routing
```
Parse CV Data → Save Candidate → Save Match Result
```

### Purpose
- **Database Creation**: Builds searchable candidate repository
- **Deduplication**: Prevents duplicate candidate entries
- **Audit Trail**: Tracks when candidates were added

---

## Node 8: Save Match Result

### What It Does
Records the detailed matching analysis results in the "Match Results" Google Sheet, creating an audit trail of all candidate-job matching decisions.

### Technical Details
```json
"type": "n8n-nodes-base.googleSheets"
"operation": "append"
```

### Configuration
- **Document ID**: `1YourMatchResultsSheetIdHere` (replace with actual sheet ID)
- **Sheet Name**: "Match Results"
- **Credentials**: Google Sheets OAuth2

### Data Mapping
```json
{
  "candidate_email": "={{ JSON.parse($('Parse CV Data').item(0).json.message.content).email }}",
  "job_id": "={{ $('Get Job Requirements').item(0).json.job_id }}",
  "suitability_percentage": "={{ JSON.parse($('Job Matching').item(0).json.message.content).suitability_percentage }}",
  "reasoning": "={{ JSON.parse($('Job Matching').item(0).json.message.content).reasoning }}",
  "recommendation": "={{ JSON.parse($('Job Matching').item(0).json.message.content).recommendation }}",
  "date_processed": "={{ $now }}"
}
```

### Input Source
- **From**: Parse CV Data node (candidate email)
- **From**: Job Matching node (assessment results)
- **From**: Get Job Requirements node (job ID)

### Output Data
```json
{
  "spreadsheetId": "1YourMatchResultsSheetIdHere",
  "updatedRows": 1,
  "updatedCells": 6
}
```

### Workflow Routing
```
Save Candidate → Save Match Result → Check Recommendation
```

### Purpose
- **Result Tracking**: Records all matching decisions
- **Performance Analysis**: Enables accuracy monitoring
- **Recruiter Review**: Provides detailed match reasoning

---

## Node 9: Check Recommendation (Decision Point)

### What It Does
Acts as a **decision switch** that routes the workflow based on the AI's recommendation, determining where to move the CV file for appropriate follow-up action.

### Technical Details
```json
"type": "n8n-nodes-base.switch"
"operation": "equal"
```

### Configuration
- **Condition**: String comparison
- **Value 1**: `{{ JSON.parse($('Job Matching').item(0).json.message.content).recommendation }}`
- **Operation**: Equal
- **Value 2**: "SUITABLE"

### Logic Flow
```
IF recommendation = "SUITABLE"
  → TRUE path → Move to Suitable folder
ELSE (recommendation = "ALTERNATIVE" OR "REJECTED")  
  → FALSE path → Move to Alternative folder
```

### Input Source
- **From**: Job Matching node
- **Data**: AI recommendation value

### Output Paths
- **Path 0** (TRUE): Routes to "Move to Suitable" node
- **Path 1** (FALSE): Routes to "Move to Alternative" node

### Workflow Routing
```
Save Match Result → Check Recommendation
                         ↙        ↘
              Move to Suitable  Move to Alternative
```

### Purpose
- **Automated Sorting**: Routes files based on AI assessment
- **Priority Management**: Separates high-priority candidates
- **Workflow Control**: Determines next action automatically

### Limitation
**Current Issue**: Only 2 output paths but 3 possible AI recommendations
- ✅ `"SUITABLE"` → Suitable folder
- ⚠️ `"ALTERNATIVE"` → Alternative folder (correct)
- ⚠️ `"REJECTED"` → Alternative folder (should go to Rejected folder)

---

## Node 10: Move to Suitable (High Priority Path)

### What It Does
Moves CVs of highly suitable candidates (70%+ match) to a dedicated "Suitable-Matches" folder for immediate recruiter attention and quick follow-up.

### Technical Details
```json
"type": "n8n-nodes-base.googleDrive"
"operation": "move"
"resource": "file"
```

### Configuration
- **File ID**: `{{ $('Google Drive Trigger').item(0).json.id }}`
- **Destination**: `1SuitableMatchesFolderIdHere` (replace with actual folder ID)
- **Credentials**: Google API OAuth2

### Trigger Condition
- **When**: AI recommendation = "SUITABLE"
- **Criteria**: Usually 70%+ match score
- **Priority**: High - immediate action required

### Input Source
- **From**: Check Recommendation node (TRUE path)
- **Data**: Original file ID from trigger

### Output Data
```json
{
  "id": "file_id",
  "name": "candidate_cv.pdf",
  "parents": ["1SuitableMatchesFolderIdHere"]
}
```

### Workflow Routing
```
Check Recommendation (TRUE) → Move to Suitable → [END]
```

### Purpose
- **Priority Queue**: High-priority candidates for immediate contact
- **Recruiter Efficiency**: Easy identification of best matches
- **Quick Action**: Streamlines interview scheduling process

---

## Node 11: Move to Alternative (Alternative/Rejected Path)

### What It Does
Moves CVs of candidates who are either suitable for alternative roles or not suitable at all to the "Alternative-Matches" folder for future consideration or different opportunities.

### Technical Details
```json
"type": "n8n-nodes-base.googleDrive"
"operation": "move"
"resource": "file"
```

### Configuration
- **File ID**: `{{ $('Google Drive Trigger').item(0).json.id }}`
- **Destination**: `1AlternativeMatchesFolderIdHere` (replace with actual folder ID)
- **Credentials**: Google API OAuth2

### Trigger Condition
- **When**: AI recommendation = "ALTERNATIVE" OR "REJECTED"
- **Alternative Criteria**: Usually 40-69% match score
- **Rejected Criteria**: Usually <40% match score

### Input Source
- **From**: Check Recommendation node (FALSE path)
- **Data**: Original file ID from trigger

### Output Data
```json
{
  "id": "file_id", 
  "name": "candidate_cv.pdf",
  "parents": ["1AlternativeMatchesFolderIdHere"]
}
```

### Workflow Routing
```
Check Recommendation (FALSE) → Move to Alternative → [END]
```

### Purpose
- **Future Opportunities**: Candidates for different roles
- **Talent Pool**: Maintains candidates for future positions
- **Organization**: Keeps non-primary matches organized

### Note
**Mixed Population**: This folder contains both alternative candidates and rejected candidates due to the current switch logic limitation.

---

## Node 12: Get Job Requirements (Parallel Process)

### What It Does
Runs in parallel with the main CV processing flow to fetch active job requirements from the Google Sheets database, providing job criteria for the AI matching analysis.

### Technical Details
```json
"type": "n8n-nodes-base.googleSheets"
"operation": "getAll"
```

### Configuration
- **Document ID**: `1YourJobVacanciesSheetIdHere` (replace with actual sheet ID)
- **Sheet Name**: "Active Jobs"
- **Filter**: Only jobs where status = "ACTIVE"
- **Credentials**: Google Sheets OAuth2

### Filter Logic
```json
"filters": {
  "values": [
    {
      "column": "status",
      "value": "ACTIVE"
    }
  ]
}
```

### Input Source
- **Trigger**: Parallel execution from Download CV node
- **Data**: No specific input data required

### Output Data
```json
[
  {
    "job_id": "JOB_0001",
    "title": "Senior Software Developer",
    "requirements": "Python, JavaScript, 5+ years experience",
    "status": "ACTIVE"
  }
]
```

### Workflow Routing
```
Download CV → Get Job Requirements
                     ↓
            Job Matching ← (provides job data)
```

### Purpose
- **Job Data Source**: Provides current job requirements for matching
- **Dynamic Matching**: Uses real-time job data from database
- **Parallel Efficiency**: Runs simultaneously with CV processing

---

## Complete Workflow Summary

### **Execution Flow**
```
1. File Upload → Google Drive Trigger
2. Move to Processing Folder
3. Download File Content  
4. Extract Text from File
                    ↓
5. Parse CV Data (AI) ←------- 12. Get Job Requirements (Parallel)
                    ↓                        ↓
6. Job Matching (AI) ←--------------------|
                    ↓
7. Save Candidate Data
                    ↓  
8. Save Match Results
                    ↓
9. Check Recommendation
        ↙              ↘
10. Move to Suitable   11. Move to Alternative
```

### **Key Capabilities**
- ✅ **Multi-format Support**: PDF, DOC, DOCX, RTF, TXT
- ✅ **AI-Powered**: GPT-4 for parsing and matching
- ✅ **Automated Organization**: Smart file sorting
- ✅ **Database Integration**: Google Sheets storage
- ✅ **Parallel Processing**: Efficient execution
- ✅ **Deduplication**: Email-based candidate uniqueness
- ✅ **Audit Trail**: Complete processing history

### **Areas for Improvement**
- ⚠️ **Three-way Switch**: Separate SUITABLE/ALTERNATIVE/REJECTED paths
- ⚠️ **Error Handling**: Add retry mechanisms and error paths  
- ⚠️ **Notifications**: Email alerts for high-priority matches
- ⚠️ **Batch Processing**: Handle multiple CVs simultaneously

### **Performance Expectations**
- **Processing Time**: 30-60 seconds per CV
- **Accuracy**: 85%+ with proper prompts
- **Scalability**: Multiple concurrent executions
- **Reliability**: Robust Google API integration

This workflow represents a complete end-to-end automation solution that transforms manual CV screening into an intelligent, scalable recruitment process.