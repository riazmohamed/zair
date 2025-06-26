# Recruit8N User Manual

## Table of Contents
1. [System Overview](#system-overview)
2. [Daily Operations](#daily-operations)
3. [CV Processing](#cv-processing)
4. [Job Management](#job-management)
5. [Candidate Matching](#candidate-matching)
6. [Database Management](#database-management)
7. [Reporting & Analytics](#reporting--analytics)
8. [Troubleshooting](#troubleshooting)

## System Overview

Recruit8N is an AI-powered recruitment automation system that:
- Automatically processes and parses CVs
- Matches candidates to job opportunities
- Manages your recruitment pipeline
- Provides intelligent alternative job suggestions
- Maintains organized candidate and job databases

### Key Benefits
- **Time Saving**: Automates 80% of initial screening work
- **Accuracy**: AI-powered matching with 85%+ accuracy
- **Organization**: Automated file management and data entry
- **Intelligence**: Smart alternative matching and reverse candidate search
- **Scalability**: Handle 100s of CVs and jobs simultaneously

## Daily Operations

### Morning Routine (5 minutes)
1. Check email notifications for overnight matches
2. Review "Pending" items in Match Results sheet
3. Verify folder organization in Google Drive

### CV Processing Workflow
1. **Receive CVs** → Upload to "New-Unprocessed" folder
2. **Automatic Processing** → System extracts data and matches jobs
3. **Review Results** → Check Google Sheets for match results
4. **Take Action** → Contact candidates or update status

### Job Posting Workflow
1. **Receive Job** → Upload description to "Active-Jobs" folder
2. **Automatic Analysis** → System parses requirements and matches candidates
3. **Review Matches** → Check Reverse Matches sheet
4. **Contact Candidates** → Reach out to suitable matches

## CV Processing

### Supported File Formats
- PDF documents
- Microsoft Word (.doc, .docx)
- Rich Text Format (.rtf)
- Text files (.txt)

### Processing Steps
1. **Upload**: Place CV in "01-Incoming CVs/New-Unprocessed/" folder
2. **Automatic Processing**: 
   - File moved to "Processing" folder
   - Text extracted and parsed
   - Candidate data extracted (name, skills, experience, etc.)
   - Matched against active job opportunities
   - Results saved to database
   - File moved to appropriate result folder

### Processing Results
CVs are automatically sorted into:
- **Suitable-Matches/**: High match candidates (70%+ match)
- **Alternative-Matches/**: Candidates for different roles (40-69% match)
- **Rejected/**: Candidates with low compatibility (<40% match)

### Data Extracted from CVs
- **Personal Info**: Name, address, phone, email
- **Professional**: Current occupation, experience years
- **Skills**: Technical and soft skills
- **Education**: Degrees, certifications
- **Additional**: Languages, projects, certifications

## Job Management

### Adding New Jobs
1. Save job description as PDF or Word document
2. Upload to "02-Job Vacancies/Active-Jobs/" folder
3. System automatically:
   - Extracts job requirements
   - Adds to Job Vacancies sheet
   - Searches candidate database for matches
   - Sends notification with results

### Job Information Extracted
- Job title and company
- Location and remote options
- Required and preferred skills
- Experience and education requirements
- Salary range (if mentioned)
- Key responsibilities

### Managing Job Status
Update job status in "Active Jobs" sheet:
- **ACTIVE**: Currently recruiting
- **FILLED**: Position has been filled
- **PAUSED**: Temporarily on hold
- **CANCELLED**: No longer recruiting

## Candidate Matching

### Matching Process
The system uses AI to analyze:
1. **Skill Alignment**: Required vs. candidate skills
2. **Experience Level**: Years and relevance of experience
3. **Education Fit**: Degree requirements and field of study
4. **Career Progression**: Natural career advancement path
5. **Industry Experience**: Relevant sector knowledge

### Match Scores
- **90-100%**: Exceptional match - immediate interview
- **80-89%**: Strong match - high priority
- **70-79%**: Good match - suitable candidate
- **60-69%**: Moderate match - consider for interview
- **50-59%**: Weak match - alternative roles possible
- **Below 50%**: Poor match - not suitable

### Understanding Match Results
Each match includes:
- **Suitability Percentage**: Overall compatibility score
- **Key Matches**: Skills and experience that align
- **Gaps**: Missing requirements or concerns
- **Reasoning**: AI explanation of the score
- **Recommendation**: SUITABLE, ALTERNATIVE, or REJECTED

### Alternative Job Matching
For candidates not suitable for primary roles:
- System searches all active positions
- Identifies transferable skills
- Suggests career development paths
- Provides confidence scores for alternatives

## Database Management

### Google Sheets Structure

#### Candidates Sheet
- Master database of all processed candidates
- Automatically populated from CV processing
- Update status as candidates progress through pipeline

#### Active Jobs Sheet
- All current job openings
- Automatically populated from job descriptions
- Update status when positions are filled

#### Match Results Sheet
- Results of candidate-job matching
- Review and update status after candidate contact
- Add recruiter notes for tracking

#### Alternative Matches Sheet
- Alternative job suggestions for candidates
- Track which alternatives were presented
- Monitor candidate responses

#### Reverse Matches Sheet
- Results when new jobs are matched against existing candidates
- Track which candidates were contacted
- Monitor interview scheduling

### Data Maintenance

#### Regular Tasks (Weekly)
1. Update candidate statuses
2. Archive filled positions
3. Clean up duplicate entries
4. Review and improve AI matching accuracy

#### Data Quality Checks
- Verify email addresses are valid
- Check for duplicate candidate entries
- Ensure job statuses are current
- Validate CV file links work

## Reporting & Analytics

### Key Metrics to Track

#### Processing Metrics
- CVs processed per day/week
- Average processing time
- File format success rates
- Data extraction accuracy

#### Matching Metrics
- Average match scores
- Suitable matches per job
- Alternative placement success rate
- Time from match to contact

#### Business Metrics
- Candidates placed per month
- Client satisfaction scores
- Time to fill positions
- Revenue per placement

### Creating Reports
1. Use Google Sheets built-in charts and pivot tables
2. Export data for external analysis tools
3. Set up automated weekly/monthly summary emails
4. Create client-specific placement reports

### Performance Monitoring
- Monitor n8n workflow execution success rates
- Track API usage and costs
- Review error logs regularly
- Optimize workflows based on performance data

## Troubleshooting

### Common Issues & Solutions

#### CV Not Processing
**Symptoms**: CV uploaded but no data in sheets
**Solutions**:
- Check file format is supported
- Verify CV is in correct folder
- Check n8n workflow execution logs
- Ensure Google Drive permissions are correct

#### Incorrect Data Extraction
**Symptoms**: Wrong information in candidate database
**Solutions**:
- Review original CV for clarity
- Check if CV format is unusual
- Manually correct data in sheets
- Report pattern to improve AI prompts

#### No Job Matches Found
**Symptoms**: CV processed but no matches
**Solutions**:
- Verify active jobs exist in database
- Check job requirements aren't too restrictive
- Review candidate skills extraction
- Consider alternative matching

#### Email Notifications Not Sending
**Symptoms**: Matches found but no email alerts
**Solutions**:
- Check SMTP credentials in n8n
- Verify email template formatting
- Test email sending manually
- Check spam/junk folders

### Error Codes & Messages

#### Google API Errors
- **403 Forbidden**: Check API permissions and quotas
- **404 Not Found**: Verify file/folder IDs are correct
- **429 Rate Limit**: Reduce processing frequency

#### OpenAI API Errors
- **401 Unauthorized**: Check API key validity
- **429 Rate Limit**: Monitor usage and upgrade plan if needed
- **500 Server Error**: Retry request, contact OpenAI if persistent

### Getting Help

#### Self-Service Resources
1. Check n8n execution logs for error details
2. Review Google Sheets for data consistency
3. Test workflows with sample data
4. Consult setup documentation

#### Support Escalation
1. Document specific error messages
2. Note time and conditions when issue occurred
3. Gather relevant workflow execution IDs
4. Contact system administrator with details

### Best Practices

#### File Management
- Use consistent CV file naming
- Keep folder structure organized
- Regular cleanup of processed files
- Backup important data regularly

#### Data Quality
- Review AI matches for accuracy
- Provide feedback on incorrect matches
- Keep candidate information updated
- Maintain clean, duplicate-free database

#### Communication
- Respond to high-confidence matches quickly
- Update candidate statuses promptly
- Use system notes for tracking interactions
- Follow up on alternative suggestions

---

**Remember**: This system is designed to augment your recruitment expertise, not replace it. Always apply professional judgment when reviewing AI recommendations and making candidate decisions.