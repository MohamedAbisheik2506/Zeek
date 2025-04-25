# Zeek Protocol Analysis for Cybersecurity

## Project Overview

This repository contains the implementation of a Zeek-based network security monitoring system for protocol analysis, developed as part of the **Cyber Security Automation (CMP020L022S)** coursework at the University of Roehampton, London. The project was undertaken by **Mohamed Abisheik Mohamed Ali (A00037677)** under the supervision of **Dr. Charles Clarke** for the MSc. Cybersecurity program.

The primary goal was to deploy Zeek to monitor network traffic, create custom scripts to detect HTTP POST requests to a specific server (192.168.115.5), and automate event reporting using Python and Elasticsearch. The project includes a static HTML page hosted on GitHub Pages to document the setup, scripts, testing, and results.

**Live Demo**: [View the Project Website](https://username.github.io/zeek-cybersecurity-project) (Replace with your GitHub Pages URL)

## Table of Contents
1. [Project Structure](#project-structure)
2. [Features](#features)
3. [Setup Instructions](#setup-instructions)
4. [Usage](#usage)
5. [Deployment on GitHub Pages](#deployment-on-github-pages)
6. [Scripts](#scripts)
7. [Testing](#testing)
8. [Contributing](#contributing)
9. [References](#references)
10. [License](#license)

## Project Structure
```
zeek-cybersecurity-project/
├── index.html          # Main HTML page for project documentation
├── media/              # Folder for images (e.g., screenshots)
├── README.md           # This file
```

## Features
- **Zeek Configuration**: Installed and configured Zeek to monitor network traffic on a specified interface (eth1).
- **Custom Scripts**: Developed Zeek scripts to log HTTP POST requests to 192.168.115.5.
- **Automation**: Python script to query Elasticsearch for event counts and generate daily reports.
- **Web Documentation**: Static HTML page hosted on GitHub Pages to showcase the project.
- **Testing**: Validated Zeek's functionality using a test HTTP server.

## Setup Instructions
To replicate the Zeek setup on a Linux system (e.g., Ubuntu), follow these steps:

1. **Install Zeek**:
   ```bash
   sudo apt update
   sudo apt install zeek -y
   ```

2. **Configure Zeek**:
   - Edit the node configuration:
     ```bash
     sudo nano /opt/zeek/etc/node.cfg
     ```
     Add:
     ```
     [sensor]
     type=worker
     host=localhost
     interface=eth1
     ```
   - Find your network interface using:
     ```bash
     ip a
     ```

3. **Run Zeek**:
   ```bash
   sudo zeek -i eth1 /usr/local/zeek/share/zeek/site/local.zeek
   ```

4. **Deploy Zeek Configuration**:
   ```bash
   cd /usr/local/zeek
   sudo ./bin/zeekctl deploy
   ```

5. **Set Up Elasticsearch** (for reporting):
   - Ensure an Elasticsearch instance is running at `192.168.115.1:9200`.
   - Install the Python Elasticsearch client:
     ```bash
     pip install elasticsearch
     ```

## Usage
1. **Monitor Logs**:
   - Check Zeek logs at `/nsm/zeek/logs/current/` (e.g., `https.log`).
   - Use `sudo nano /nsm/zeek/logs/current/` to view or edit logs.

2. **Run the Automation Script**:
   - Execute the Python script to generate daily event reports:
     ```bash
     python3 report_script.py
     ```
   - Reports are appended to `/reports/daily_report.txt`.

3. **View the Website**:
   - Access the project documentation at your GitHub Pages URL (e.g., `https://username.github.io/zeek-cybersecurity-project`).

## Deployment on GitHub Pages
The project documentation is hosted on GitHub Pages. To deploy:

1. **Create a Repository**:
   - Create a public repository named `zeek-cybersecurity-project` (or `username.github.io` for a personal site).
   - Initialize with a README.

2. **Upload Files**:
   - Upload `index.html`, `README.md`, and any images to the repository.
   - Place images in a `media/` folder.

3. **Enable GitHub Pages**:
   - Go to repository Settings > Pages.
   - Set the source to the `main` branch and root folder (`/`).
   - Save, and the site will be live at `https://username.github.io/zeek-cybersecurity-project`.

4. **Update Links**:
   - Edit `index.html` to update the GitHub repository link in the Introduction section.
   - Re-upload the updated file.

## Scripts
### Zeek Script (`local.zeek`)
Located at `/opt/zeek/share/zeek/site/local.zeek`, this script logs HTTP POST requests to 192.168.115.5:
```zeek
event http_request(c: connection, method: string, original_URI: string, unescaped_URI: string, version: string)
{
    if (method == "POST" && c$id$resp_h == 192.168.115.5) {
        print fmt("POST to Auth/Dev from %s: %s", c$id$orig_h, original_URI);
    }
}
```

### Python Reporting Script
This script queries Elasticsearch for event counts and generates daily reports:
```python
from elasticsearch import Elasticsearch
from datetime import datetime

# Connect to Elasticsearch instance
es = Elasticsearch(['192.168.115.1:9200'])

# Query to count events from the last 24 hours
query = {
    "range": {
        "@timestamp": {
            "gte": "now-1d/d"  # Events from the start of the day, one day ago
        }
    }
}

# Execute the count query on all Security Onion indices
res = es.count(index="so-*", query=query)

# Write the result to a daily report file
report_path = "/reports/daily_report.txt"
with open(report_path, "a") as f:
    f.write(f"{datetime.now()}: {res['count']} events on 192.168.115.0/24\n")
```

## Testing
The system was tested using a Python HTTP server:
```bash
sudo python3 -m http.server 80 --bind 192.168.115.5
```
- Logs were verified at `/nsm/zeek/logs/current/https.log`.
- The Zeek script successfully captured HTTP POST requests.

## Contributing
This project was developed as part of coursework and is not actively maintained. For suggestions or inquiries, contact Mohamed Abisheik Mohamed Ali via GitHub.

## References
- [Zeek Documentation](https://docs.zeek.org/en/master/)
- [Elasticsearch Python Client](https://elasticsearch-py.readthedocs.io/en/latest/)
- [Security Onion Documentation](https://docs.securityonion.net/en/2.3/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)

## License
This project is for educational purposes and does not include a specific license. All code and documentation are the intellectual property of Mohamed Abisheik Mohamed Ali and the University of Roehampton.

---

**Author**: Mohamed Abisheik Mohamed Ali (A00037677)  
**Course**: MSc. Cybersecurity, University of Roehampton  
**Lecturer**: Dr. Charles Clarke
