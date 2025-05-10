# AI Job Applier

An automated job application tool that uses browser automation to apply for jobs on popular job boards.

## Features

- Config-driven browser automation for job applications
- Support for multiple job sites (LinkedIn, Indeed, Instahyre)
- User profile and resume management
- Job application logging
- CLI interface for easy usage

## Setup

1. Clone the repository:
```bash
git clone <repository-url>
cd agentic_jobber
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

3. Install Playwright browsers:
```bash
playwright install
```

4. Set up environment variables:
Create a `.env` file in the project root with the following variables:
```
JOB_SITE_USERNAME=your_username
JOB_SITE_PASSWORD=your_password
```

5. Prepare your user profile and resume:
Place your user profile and resume YAML files in the `data/` directory.

## Usage

### Basic Usage

Apply to a single job:
```bash
python -m src.integration "https://www.linkedin.com/jobs/view/12345"
```

### Advanced Options

```bash
python -m src.integration --help
```

Options:
- `--profile`: Path to user profile YAML
- `--resume`: Path to resume YAML
- `--log`: Path to job application log YAML
- `--mode`: Run mode (`dev` or `prod`)
- `--dry-run`: Skip browser automation, test data flow only
- `--verbose`: Enable detailed logging
- `--batch`: File containing multiple job URLs (one per line)
- `--output`: Custom output file for results/logs
- `--headless`: Run browser in headless mode (no GUI)

### Batch Mode

Apply to multiple jobs:
```bash
python -m src.integration --batch jobs.txt
```
Where `jobs.txt` contains one job URL per line.

## Project Structure

```
agentic_jobber/
├── config/
│   ├── config.yaml             # Main configuration
│   └── site_configs/           # Site-specific configurations
│       ├── linkedin.json
│       ├── indeed.json
│       └── instahyre.json
├── data/
│   ├── user_profile.yaml       # User information
│   ├── resume.yaml             # Resume data
│   └── job_application_log.yaml # Application history
├── logs/
│   ├── application.log         # Application logs
│   └── screenshots/            # Screenshots of applications
├── src/
│   ├── browser_automation.py   # Browser automation logic
│   ├── config_loader.py        # Site config loading
│   ├── integration.py          # Main integration script
│   ├── job_application_log.py  # Job application logging
│   ├── resume.py               # Resume model
│   └── user_profile.py         # User profile model
├── tests/                      # Test files
├── wiki/                       # Project documentation
├── requirements.txt            # Project dependencies
└── README.md                   # This file
```

## Adding New Job Sites

1. Create a new JSON configuration file in `config/site_configs/`
2. Add the site pattern to `ConfigLoader.site_patterns` in `src/config_loader.py`
3. Test with `--dry-run` before actual submission

## Contributing

1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## License

MIT 