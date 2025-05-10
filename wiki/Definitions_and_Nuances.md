# Definitions and Nuances: AI Job Applier

## User Profile
- **Purpose:** Persistent, canonical record of the user's personal and professional identity.
- **Contents:**
  - Name, contact info, address, citizenship, work authorization, etc.
  - Core identifiers (email, phone, LinkedIn, etc.)
  - Preferences (job types, locations, salary expectations)
  - Static info not always present on a resume (e.g., legal status, emergency contacts)
- **Usage:** Used for any application, onboarding, or system that needs to "know" the user. Rarely changes.

## Resume
- **Purpose:** Tailored, role-specific marketing document for employers.
- **Contents:**
  - Education, work experience, projects, skills, certifications, achievements
  - Customizable for each job application
  - Multiple versions possible (e.g., "Data Scientist Resume", "ML Engineer Resume")
- **Usage:** Sent to employers, parsed for ATS, attached to job applications. Frequently updated and versioned.

## Why Keep Them Separate?
- **Separation of Concerns:** Profile is stable and system-facing; resume is dynamic and employer-facing.
- **Flexibility:** Multiple resumes from a single profile; some info is private and not for resumes.
- **Security:** Sensitive info (like SSN) should not be in every resume sent out.

## Other Key Objects
- **Job Application Log:** Tracks each application attempt, metadata, status, and notes.
- **Config:** Stores paths, mode, and logging settings (credentials in env vars).

## Schema Alignment & Lessons Learned
- **Schema Consistency:** All YAML files must match the expected structure in their corresponding data models. For example, `user_profile.yaml` must have all personal fields under a `personal_information` key.
- **Recent Correction:** Fixed a KeyError by restructuring `user_profile.yaml` to wrap personal fields under `personal_information`.
- **Best Practice:** Always validate YAML structure against the data class or loader logic to avoid runtime errors.

---

*This file documents the rationale and structure for major data models and objects in the AI Job Applier project. Update as the project evolves.* 