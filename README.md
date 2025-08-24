# Simple Active Directory Simulator

A lightweight Python project simulating basic Microsoft Active Directory (AD) functionalities. This tool allows managing users and groups in a simulated domain environment, useful for learning AD concepts without needing Windows Server.

## Features
- Add, list, delete, and update users with attributes (username, password hash, email, groups).
- Create and manage groups.
- Basic authentication (login simulation).
- Persistent storage using JSON for data retention.
- Console-based menu for easy interaction.

## Why This Project?
- Demonstrates IT skills in user management, automation, and simulation of enterprise tools like AD.
- Ideal for IT Helpdesk roles involving user troubleshooting and scripting.

## Installation
1. Clone the repo: `git clone https://github.com/yourusername/SimpleActiveDirectorySimulator.git`
2. Install dependencies: `pip install -r requirements.txt`
3. Run: `python ad_simulator.py`

## Usage Example
$ python src/ad_simulator.py === AD Simulator Menu ===
1.  Add User
2.  List Users … Choose option: 1 Username: john.doe Password: [hidden] Email: john@example.com User added successfully!
3.  ## Tech Stack
- Python 3.x
- JSON for data persistence

## Future Enhancements
- LDAP integration for real AD environments.
- GUI interface using Tkinter.
- Password encryption for enhanced security.

## License
MIT
