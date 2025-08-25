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

requirements.txt:
# No external dependencies required for core functionality
# Add 'ldap3' if extending to real AD integration
src/ad_simulator.py:
import json
import hashlib
import getpass
import os

class User:
    def __init__(self, username, password, email):
        self.username = username
        self.password_hash = self._hash_password(password)
        self.email = email
        self.groups = []

    def _hash_password(self, password):
        return hashlib.sha256(password.encode()).hexdigest()

    def add_to_group(self, group):
        if group not in self.groups:
            self.groups.append(group)

    def check_password(self, password):
        return self._hash_password(password) == self.password_hash

class Group:
    def __init__(self, name):
        self.name = name
        self.members = []

    def add_member(self, user):
        if user not in self.members:
            self.members.append(user)

class ActiveDirectorySimulator:
    def __init__(self, data_file='data/ad_data.json'):
        self.data_file = data_file
        self.users = {}
        self.groups = {}
        self.load_data()

    def load_data(self):
        if os.path.exists(self.data_file):
            with open(self.data_file, 'r') as f:
                data = json.load(f)
                for u_data in data.get('users', []):
                    user = User(u_data['username'], '', u_data['email'])
                    user.password_hash = u_data['password_hash']
                    user.groups = u_data['groups']
                    self.users[u_data['username']] = user
                for g_data in data.get('groups', []):
                    self.groups[g_data['name']] = Group(g_data['name'])
        print("Data loaded.")

    def save_data(self):
        users_data = [
            {
                'username': user.username,
                'password_hash': user.password_hash,
                'email': user.email,
                'groups': user.groups
            } for user in self.users.values()
        ]
        groups_data = [{'name': group.name} for group in self.groups.values()]
        data = {'users': users_data, 'groups': groups_data}
        with open(self.data_file, 'w') as f:
            json.dump(data, f, indent=4)
        print("Data saved.")

    def add_user(self, username, password, email):
        if username in self.users:
            print("User already exists!")
            return False
        user = User(username, password, email)
        self.users[username] = user
        self.save_data()
        print(f"User {username} added successfully!")
        return True

    def list_users(self):
        if not self.users:
            print("No users found.")
            return
        for user in self.users.values():
            print(f"Username: {user.username}, Email: {user.email}, Groups: {', '.join(user.groups)}")

    def delete_user(self, username):
        if username in self.users:
            del self.users[username]
            self.save_data()
            print(f"User {username} deleted.")
        else:
            print("User not found.")

    def create_group(self, name):
        if name in self.groups:
            print("Group already exists!")
            return False
        group = Group(name)
        self.groups[name] = group
        self.save_data()
        print(f"Group {name} created.")
        return True

    def add_user_to_group(self, username, group_name):
        if username not in self.users or group_name not in self.groups:
            print("User or group not found.")
            return
        user = self.users[username]
        group = self.groups[group_name]
        user.add_to_group(group_name)
        group.add_member(user)
        self.save_data()
        print(f"User {username} added to group {group_name}.")

    def authenticate(self, username, password):
        if username in self.users:
            user = self.users[username]
            if user.check_password(password):
                print(f"Authentication successful for {username}.")
                return True
        print("Authentication failed.")
        return False

def main():
    ad = ActiveDirectorySimulator()
    while True:
        print("\n=== AD Simulator Menu ===")
        print("1. Add User")
        print("2. List Users")
        print("3. Delete User")
        print("4. Create Group")
        print("5. Add User to Group")
        print("6. Authenticate (Login)")
        print("7. Exit")
        choice = input("Choose option: ")
        
        if choice == '1':
            username = input("Username: ")
            password = getpass.getpass("Password: ")
            email = input("Email: ")
            ad.add_user(username, password, email)
        elif choice == '2':
            ad.list_users()
        elif choice == '3':
            username = input("Username to delete: ")
            ad.delete_user(username)
        elif choice == '4':
            name = input("Group name: ")
            ad.create_group(name)
        elif choice == '5':
            username = input("Username: ")
            group_name = input("Group name: ")
            ad.add_user_to_group(username, group_name)
        elif choice == '6':
            username = input("Username: ")
            password = getpass.getpass("Password: ")
            ad.authenticate(username, password)
        elif choice == '7':
            ad.save_data()
            print("Exiting...")
            break
        else:
            print("Invalid choice!")

if __name__ == "__main__":
    main()

tests/tests.py:
import unittest
from src.ad_simulator import ActiveDirectorySimulator, User

class TestADSimulator(unittest.TestCase):
    def setUp(self):
        self.ad = ActiveDirectorySimulator('data/test_data.json')

    def test_add_user(self):
        self.assertTrue(self.ad.add_user('testuser', 'testpass', 'test@example.com'))
        self.assertIn('testuser', self.ad.users)

    def test_authenticate(self):
        self.ad.add_user('authuser', 'authpass', 'auth@example.com')
        self.assertTrue(self.ad.authenticate('authuser', 'authpass'))

if __name__ == '__main__':
    unittest.main()

data/ad_data.json: Leave empty; the script generates it when you run it.
