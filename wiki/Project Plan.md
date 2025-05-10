# AI Job Application Bot: Revised Phased Development Plan

Here's a revised phased approach for building your AI-powered job application bot, focusing on an MVP with core application capabilities first, followed by iterative enhancements.

## Core Technologies & Principles (To be applied throughout all phases):

- **Primary Language**: Python
- **LLM Integration**: Gemini, OpenAI (and potentially others)
- **NLP Libraries**: spaCy, NLTK (for text processing, keyword extraction where needed)
- **Web Interaction/Automation**: Playwright
- **Data Storage**: SQLite (for simplicity initially for tracking), JSON/YAML for configurations and profiles.
- **Multi-Agent Frameworks**: (e.g., LangGraph, AutoGen) - Consider for later phases if complexity warrants, especially for diverse site handling or complex decision making.
- **Model Context Protocol (MCP)**: Explore if managing context across multiple LLM calls or agents becomes a significant challenge.
- **Modular Design**: Build components that can be developed, tested, and upgraded independently.
- **Iterative Development**: Each phase builds upon the previous, adding new, focused functionality.
- **Security**: Prioritize secure handling of user credentials and personal data from the outset.

## Phase 1: MVP - Core Automated Application Submission

**Goal**: Develop the core capability of the bot to take a user's resume and a job posting link, navigate to the job application page, fill out the form, handle basic surveys, and submit the application. This phase also includes essential foundational setup.

### Tasks:

- **Foundation & Setup**:
  - **Project Initialization**: Set up Python environment, version control (Git), and project structure (src, data, config).
  - **Define Core Data Models**:
    - **User Profile**: Structure (Python class/Pydantic model) for applicant info (name, contact, basic education/experience snippets for direct form filling, login credentials for job sites).
    - **Base Resume**: How the primary resume is stored (e.g., path to a .txt or .docx file).
  - **Configuration Management**: System for API keys (LLMs) and bot settings. Use environment variables or a config file.
  - **Secure Credential Storage**: Implement a secure method for storing and accessing job site login credentials (e.g., keyring library, OS keychain integration, or encrypted file with user-provided master password).

- **Input Processing**:
  - Function to accept the path to the user's resume.
  - Function to accept the URL of the job posting.

- **Web Navigation & Site Interaction** (Target one common platform first, e.g., LinkedIn "Easy Apply" or a specific ATS like Workday/Greenhouse):
  - **Browser Automation Setup**: Configure Playwright.
  - **Job Link Resolution**: From the job posting URL, navigate to the actual application form URL (this might involve parsing the initial posting page).
  - **Login Automation**: Automate login to the target job platform if required.

- **Application Form Filling**:
  - **Field Identification**: Analyze the HTML structure of the target application form(s). Develop robust selectors (CSS/XPath) for common fields (name, email, phone, address, simple experience fields). This will be site-specific initially.
  - **Data Mapping**: Map information from the user's profile and potentially parsed basic resume details to the identified form fields.
  - **Automated Filling**: Implement logic to populate text inputs, select from simple dropdowns, and handle basic checkboxes/radio buttons.
  - **Resume Upload (Manual Prompt for MVP)**: Initially, the bot might pause and prompt the user to manually handle the resume file upload step on the webpage. Full automation can be complex and added later.

- **Basic Survey Handling (Experimental & User-Reviewed)**:
  - Identify very common, simple survey questions (e.g., "How did you hear about this role?", "Are you authorized to work...?").
  - Provide pre-defined answers or use an LLM to generate suggestions for simple, factual questions.
  - **Crucial**: All LLM-generated or pre-filled survey answers must be presented to the user for review and explicit approval before submission.

- **Application Submission**:
  - Automate the final submission click, with clear logging of success or failure.

- **Basic Error Handling & Logging**:
  - Implement try-except blocks for common web automation issues (element not found, timeout).
  - Log key steps and any errors encountered.

**Key Technologies/Focus**: Python, Playwright, basic data structures, secure credential management, HTML/CSS selectors, foundational LLM use for simple Q&A (with strict user oversight).

## Phase 2: LinkedIn Referral Integration

**Goal**: Enhance the MVP bot to identify opportunities for referrals on LinkedIn for open roles and assist the user in requesting them.

### Tasks:

- **Role & Contact Identification (LinkedIn)**:
  - After identifying a job (either through the bot or manually by the user), if the job is on LinkedIn or if the company is known, add functionality to search for potential referrers (e.g., 1st or 2nd-degree connections working at the target company, recruiters).
  - This may involve navigating LinkedIn profiles and search results.

- **Referral Request Message Drafting**:
  - Use an LLM to draft personalized referral request messages.
  - The prompt to the LLM should include context like the user's profile, the job description, and the potential referrer's profile (if accessible).

- **Semi-Automated Message Sending**:
  - The bot should present the drafted message to the user for review, editing, and approval.
  - Upon approval, the bot can automate sending the message via LinkedIn.

- **User Interaction & Approval**:
  - Ensure the user has full control and must approve any message before it's sent.
  - Consider rate limiting and respectful interaction patterns to avoid spamming.

**Key Technologies/Focus**: Playwright (for LinkedIn interaction), LLM for message generation, understanding LinkedIn's UI structure.

## Phase 3: Resume Tailoring & Personalized Cover Letter Generation

**Goal**: Integrate capabilities to tailor the user's resume and generate personalized cover letters based on specific job descriptions.

### Tasks:

- **Job Description (JD) Analysis**:
  - Develop a robust JD parser (if not sufficiently covered in Phase 1 for keyword extraction).
  - Use an LLM to extract key skills, experience requirements, company values, and tone from the JD.

- **Resume Analysis & Keyword Gap**:
  - Parse the user's base resume (support for .txt, .docx, potentially .pdf using libraries like python-docx, PyPDF2).
  - Compare skills/keywords from the JD with the user's resume to identify gaps or areas for emphasis.

- **Contextual Resume Tailoring Suggestions/Drafting**:
  - Use an LLM to suggest specific edits to the resume (e.g., rephrasing bullet points, adding specific keywords contextually, highlighting relevant projects).
  - The bot should present these suggestions clearly, perhaps showing a "diff" or side-by-side comparison. The user makes the final edits.
  - Optionally, allow the bot to create a draft tailored resume (new file) for user review.

- **Personalized Cover Letter Generation**:
  - Use an LLM to draft a cover letter.
  - The prompt should include the JD, user's profile/resume (ideally the tailored version), and any specific points the user wants to highlight.
  - The generated cover letter must be for user review and editing.

- **Document Management**:
  - Manage versions of tailored resumes and cover letters (e.g., naming convention Resume_CompanyName_Role.docx).

**Key Technologies/Focus**: Advanced LLM prompting (for analysis, generation, and editing suggestions), NLP libraries (spaCy/NLTK for pre-processing), file I/O for different resume formats.

## Phase 4: Job Tracking Capabilities

**Goal**: Develop a system for the bot to log all applied jobs and for the user to manually track their status and add notes.

### Tasks:

- **Data Storage for Tracking**:
  - Set up a simple database (e.g., SQLite) or a structured file (CSV, JSON) for application logs.
  - Define schema: Company, Role, Job URL, Date Applied, Status (e.g., Applied, Pending, Interviewing, Offer, Rejected), Notes, Path to Tailored Resume/Cover Letter Used.

- **Automated Logging**:
  - After a successful application submission (from Phase 1), automatically create an entry in the tracking system.

- **Manual Entry & Updates**:
  - Provide a mechanism for the user to manually add applications they applied to outside the bot.
  - Allow users to update the status and add notes to any tracked application.

- **Basic Reporting/Display**:
  - Functionality to display a list of all tracked jobs, filterable by status or date.

**Key Technologies/Focus**: Python, SQLite (or other chosen database/file format), data management.

## Phase 5: User Interface (UI) Development & Rigorous Testing

**Goal**: Create a simple, user-friendly interface for the bot and conduct comprehensive testing of all integrated features.

### Tasks:

- **UI/UX Design (Simple)**:
  - Sketch out a basic interface for:
    - Inputting user profile information and credentials (one-time setup).
    - Starting a new application (providing job link, base resume).
    - Reviewing and approving LLM-generated content (survey answers, referral messages, resume suggestions, cover letters).
    - Viewing and managing tracked job applications.

- **UI Development**:
  - Choose and implement a UI framework. Options:
    - Web-based (local): Streamlit, Gradio, or Flask (if more customization is needed).
    - Desktop: Tkinter (built-in), PyQt.
  - Focus on ease of use for the defined tasks.

- **End-to-End Testing**:
  - Test the entire workflow: from inputting a job link to application submission, referral requests, document tailoring, and tracking.
  - Test with various job descriptions and different (supported) career portals/job boards.
  - Focus on edge cases, error conditions, and UI responsiveness.

- **Refinement & Bug Fixing**:
  - Iterate on the UI and bot logic based on testing results and usability feedback (even if it's just your own feedback initially).
  - Enhance error handling and provide clearer feedback to the user.

- **Documentation**:
  - Create basic user documentation (how to set up and use the bot).
  - Ensure code is well-commented.

**Key Technologies/Focus**: Chosen UI framework (Streamlit, Gradio, Flask, Tkinter, etc.), testing methodologies, debugging tools.

## Working with a Junior Developer / AI IDE (Applicable across all phases):

- **Clear Task Definitions**: Break down tasks within each phase into smaller, well-defined units.
- **Version Control Discipline**: Ensure consistent use of Git for branching, commits, and pull requests.
- **Code Reviews**: Regularly review code for quality, correctness, and adherence to design.
- **Pair Programming**: Useful for complex parts like initial web automation logic or tricky LLM prompt engineering.

- **AI IDE (Cursor, GitHub Copilot, etc.)**:
  - Leverage for boilerplate code, generating function stubs from comments, suggesting completions.
  - Assistance in debugging and exploring alternative implementations.
  - **Crucial**: Always critically review, understand, and test AI-generated code. It's an assistant, not a replacement for developer understanding and responsibility.


DEV MODE

# Updates while building Phase - 1

## Hybrid Resume Data Model Approach (MVP Nuance)

### Rationale
To balance reliability and flexibility, the MVP resume data model uses a hybrid approach:
- **Core fields** (required): The model validates that essential sections (e.g., personal_information, education_details, experience_details, skills) are present and non-empty.
- **Flexible extras**: Any additional sections in the YAML resume are loaded and accessible, but not required. The model warns (but does not fail) if extra fields are present.

### Implementation
- The Resume class loads all YAML sections as attributes.
- On validation, it raises an error if any core field is missing or empty.
- If extra fields are present, it issues a warning (using Python's warnings module).

### Core Required Fields
- personal_information
- education_details
- experience_details
- skills (or technical_skills)

### Benefits
- Ensures all automation-critical data is present.
- Allows users to add new sections without breaking the code.
- Provides clear feedback for both missing and extra fields.

### Example
A resume YAML with extra fields (e.g., certifications, languages) will load successfully, but only the absence of core fields will cause an error. Extra fields will trigger a warning, not a failure.

## Job Application Log Model (MVP Nuance)

### Rationale
A job application log model provides a structured way to record and track every job application submitted using the bot (or manually). It enables accountability, follow-up, and analytics for your job search process.

### Finalized Fields for Job Application Log Entry
- company: Name of the company
- role: Job title/position
- job_url: Link to the job posting
- date_applied: When you applied (timestamp)
- location: Exact location of the job (city, country, or remote)
- work_culture: Work arrangement (e.g., remote, hybrid, on-site)
- salary_discussed: Salary discussed with HR before interview (yes/no/amount)
- status: Current status (e.g., Applied, Interviewing, Offer, Rejected)
- notes: Any extra info (e.g., follow-up, interview feedback)
- resume_used: Path to the resume version used (if tailored)
- cover_letter_used: Path to the cover letter (if used)
- referral: Boolean flag (true/false) if you got a referral, and (optionally) a string for the referrer's name/source
- last_updated: Timestamp for last status update (for follow-up logic)
- application_id: (Optional) Unique ID for each application

### Example YAML Entry
```yaml
- company: "OpenAI"
  role: "AI Engineer"
  job_url: "https://careers.openai.com/jobs/123"
  date_applied: "2024-05-10T14:23:00"
  location: "San Francisco, CA"
  work_culture: "Remote"
  salary_discussed: "150,000 USD"
  status: "Applied"
  notes: "Referred by John Doe, follow up in 2 weeks"
  resume_used: "data/resume_ayc_nlc.yaml"
  cover_letter_used: "data/cover_letter_openai.txt"
  referral: 
    flag: true
    source: "LinkedIn - John Doe"
  last_updated: "2024-05-10T14:23:00"
  application_id: "openai-20240510-001"
```

### Implementation
- The JobApplicationLogEntry and ReferralInfo dataclasses represent each entry and referral details.
- The JobApplicationLog class manages a list of entries, with methods to add, update, and save/load from YAML.
- This model supports easy extension for future analytics, reminders, and follow-up automation.