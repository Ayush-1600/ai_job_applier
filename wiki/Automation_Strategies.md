# Web Form Automation Strategies: Reference

This document summarizes the main strategies for automated web form filling in the context of the AI Job Applier project. Use this as a reference for architectural and implementation decisions.

---

## Progress Log

- ✅ Instahyre config-driven dry run completed successfully
- ✅ Indeed config-driven dry run tbd.
- ⚠️  .env encoding issue resolved by saving as UTF-8 (no BOM)
- System loads user data, config, and logs the application in dry run mode for both sites
- Next: Implement real browser automation and expand to more job sites

---

## 1. Config-Driven (Selector-Based) Automation
- **How it works:** Uses pre-defined configs (YAML/JSON) with selectors for each supported site. Automation reads the config and fills the form accordingly.
- **Pros:** Fast, reliable for known sites, easy to maintain for stable job boards, no AI inference cost.
- **Cons:** Brittle if site structure changes, manual config creation/updates needed.
- **Best for:** High-traffic, well-known job boards (LinkedIn, Indeed, etc.).

## 2. Template-Based Automation
- **How it works:** Uses higher-level templates mapping logical fields to selectors or field types. Reusable across similar sites.
- **Pros:** Reusable, easier to add new sites with common patterns, reduces config duplication.
- **Cons:** Still needs mapping for new/unusual sites, less flexible for custom forms.
- **Best for:** Clusters of sites with similar layouts.

## 3. Vision-Based LLM Automation
- **How it works:** Uses a vision-capable LLM to "see" the page, identify fields, and instruct the bot how to fill the form.
- **Pros:** Works on any site, no manual config needed, adapts to layout changes.
- **Cons:** Slower, more expensive, may make mistakes, needs robust error handling.
- **Best for:** New, rare, or rapidly changing sites; fallback when no config/template exists.

## 4. Hybrid/Strategy Pattern
- **How it works:** Combines the above strategies. Chooses the best approach per site; logs successful LLM runs to generate new configs.
- **Pros:** Speed for known sites, flexibility for new ones, extensible.
- **Cons:** More complex architecture, needs good strategy selection logic.
- **Best for:** Scalable, production-grade systems handling both common and rare sites.

---

### Summary Table

| Strategy                | Speed | Reliability | Maintenance | Flexibility | Cost   | Best For                |
|-------------------------|-------|-------------|-------------|-------------|--------|-------------------------|
| Config-Driven           | High  | High        | Medium      | Low         | Low    | Popular, stable sites   |
| Template-Based          | High  | Medium      | Low         | Medium      | Low    | Similar form clusters   |
| Vision-Based LLM        | Low   | Medium      | Low         | High        | High   | Unseen/rare sites       |
| Hybrid (Strategy)       | High  | High        | Medium      | High        | Medium | Real-world, scalable    |

---

**For MVP:** Start with config-driven automation. Plan for hybrid/strategy pattern in future iterations. 

---

## **Low-Level System Diagram (MVP: Config-Driven Automation)**

```
+-------------------+      +-------------------+      +-------------------+      +-------------------+
|  User Input (CLI) | ---> |  Load Config JSON | ---> |  Browser Automation| ---> |   Log Results     |
+-------------------+      +-------------------+      +-------------------+      +-------------------+
        |                        |                          |                          |
        |                        |                          |                          |
        v                        v                          v                          v
  (job link, resume,      (site-specific selectors)   (fill form fields,         (success/failure,
   name, email)                                      upload resume, submit)      errors, timestamp)
```

---

## **Step-by-Step Execution Plan**

### 1. **User Input (CLI)**
- User provides:
  - Job site link (e.g., LinkedIn job posting)
  - Resume file path
  - Name and email (or loads from profile YAML)
- CLI parses and validates these inputs.

### 2. **Load Config JSON**
- System detects which site the job link belongs to (e.g., LinkedIn, Indeed, Instahyre).
- Loads the corresponding JSON config file, which contains:
  - CSS/XPath selectors for: name, email, resume upload, job title search, submit button.
  - Any site-specific quirks (e.g., wait times, extra clicks).

**Technical Concept:**  
- **Selector:** A string (CSS or XPath) that uniquely identifies an element on a web page.  
  - Example: `input[name="email"]` or `//input[@type="file"]`

### 3. **Browser Automation (Playwright)**
- Launches a browser (headless or visible, for debugging).
- Navigates to the job link.
- Uses selectors from the config to:
  - Fill in the name and email fields.
  - Upload the resume file.
  - (If required) Enter job title in a search field.
  - Click the submit button.
- Waits for confirmation or error message.

**Technical Concepts:**  
- **Playwright:** A Python library for browser automation. It can control Chrome, Firefox, etc., and interact with web pages like a human.
- **Headless Mode:** Running the browser without a GUI, useful for automation and speed.

### 4. **Error Handling and Logging**
- If a selector is not found or an action fails:
  - Log the error (with context: which field, which site, what failed).
  - Continue to the next field or step.
- After submission, log:
  - Success/failure
  - Any error messages
  - Timestamp
  - Job link and user info used

**Technical Concept:**  
- **Logging:** Writing structured information about events/errors to a file for later review and debugging.

---

## **Example Config JSONs**

### LinkedIn
```json
{
  "site": "linkedin.com",
  "fields": {
    "name": "input[name='fullName']",
    "email": "input[name='email']",
    "resume_upload": "input[type='file']",
    "job_title_search": "input[placeholder='Search jobs']",
    "submit_button": "button[type='submit']"
  },
  "special_actions": {
    "login_required": true,
    "login_page": "https://www.linkedin.com/login",
    "username_field": "input#username",
    "password_field": "input#password",
    "login_button": "button[type='submit']",
    "wait_after_login": 3
  }
}
```

### Indeed
```json
{
  "site": "indeed.com",
  "fields": {
    "name": "input#input-applicant-name",
    "email": "input#input-email-address",
    "resume_upload": "input#resume-upload-input",
    "job_title_search": "input#text-input-what",
    "submit_button": "button#indeedApplyButton"
  },
  "special_actions": {
    "login_required": false,
    "wait_after_upload": 2,
    "confirmation_selector": "div.ia-JobApplySuccess"
  }
}
```

### Instahyre
```json
{
  "site": "instahyre.com",
  "fields": {
    "name": "input[name='name']",
    "email": "input[name='email']",
    "resume_upload": "input[type='file'][accept='.pdf,.doc,.docx']",
    "job_title_search": "input.search-input",
    "submit_button": "button.apply-button"
  },
  "special_actions": {
    "login_required": true,
    "login_page": "https://www.instahyre.com/login/",
    "username_field": "input[name='email']",
    "password_field": "input[name='password']",
    "login_button": "button[type='submit']",
    "wait_after_login": 2
  }
}
```

*Each site will have its own config file with selectors tailored to its DOM structure. The actual selectors may need adjustment based on the current site structure.*

---

## **Iterative Error Handling**
- All errors are logged (not fatal).
- The system continues to try filling/submitting the form even if some fields fail.
- Logs are reviewed after each run to improve selectors/configs and add robustness.

---

## **Summary Table of Key Concepts**

| Concept         | What It Means (for Data Scientists)                                  |
|-----------------|---------------------------------------------------------------------|
| Selector        | A way to programmatically find a field/button on a web page         |
| Playwright      | Python tool to automate browsers, like Selenium but newer/faster    |
| Headless Mode   | Browser runs in the background, no window pops up                   |
| Logging         | Recording what happened, especially errors, for debugging           |
| Config JSON     | A file that tells the bot where to find and how to fill each field  |

---

## **Implementation Phases**
1. **Phase 1:** User Input CLI
2. **Phase 2:** Config JSON Structure and Loading
3. **Phase 3:** Browser Automation with Playwright
4. **Phase 4:** Error Handling and Logging
5. **Phase 5:** Testing and Refinement 