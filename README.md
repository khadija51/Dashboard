# Dashboard
> A modern, responsive dashboard for visualizing and managing key business metrics.
> What This Code Does:

This Python program creates a GitHub Dashboard.
It asks you to enter a GitHub username, then shows:

The user's profile info (name, followers, bio, etc.)

A table of all the user's public repositories, showing stars, forks, and languages.



---

Explanation of Each Part:


---

import requests

This brings in the requests library, which lets the program connect to the internet and get data from GitHub.

from prettytable import PrettyTable

This imports PrettyTable, a tool that helps show data in a nice table format in the console.


---

1. Class: GitHubUser

This class handles everything about a GitHub user.

_init_(self, username)

Saves the GitHub username.

Creates empty storage for profile data and repository list.



---

fetch_profile(self)

Connects to GitHub’s API to get the user’s profile information.

If successful (status code 200), it saves the data.

If not, it shows an error.



---

fetch_repositories(self)

Connects to GitHub’s API to get the user’s public repositories.

Saves them if the request is successful.

Shows an error if it fails.



---

display_profile(self)

Prints the user’s:

Name

Number of public repos

Followers and following

Bio


If some data is missing, it shows "N/A" (Not Available).



---

display_repositories(self)

Shows a table with all repositories.

Each row shows:

Repository name

Number of stars

Number of forks

Programming language




---

2. Class: GitHubDashboard

This class manages the full dashboard process.

_init_(self, username)

Creates a GitHubUser object using the given username.



---

load_data(self)

Calls two functions:

fetch_profile() to get profile info.

fetch_repositories() to get repo list.




---

show_dashboard(self)

Calls two functions:

display_profile() to print user info.

display_repositories() to print the table.




---

3. The Main Program

if _name_ == "_main_":

This part starts the program when you run the file.

Steps:

1. Asks you to enter a GitHub username.


2. Creates a dashboard using that name.


3. Loads the data.


4. Shows the dashboard.


5. If anything goes wrong (wrong name, internet issue, etc.), it shows an error message.




---

Example Output (in terminal)

If you enter a GitHub username like torvalds, it may show:

--- GitHub Profile ---
Name: Linus Torvalds
Public Repos: 6
Followers: 100000
Following: 0
Bio: Creator of Linux

--- Repositories ---
+-------------+-------+--------+----------+
| Repo Name   | Stars | Forks  | Language |
+-------------+-------+--------+----------+
| linux       | 160000| 50000  | C        |
| subsurface  | 3000  | 900    | C++      |
+-------------+-------+--------+----------+

import requests
from prettytable import PrettyTable

class GitHubUser:
    def _init_(self, username):
        self.username = username
        self.profile_data = {}
        self.repos = []

    def fetch_profile(self):
        url = f"https://api.github.com/users/{self.username}"
        response = requests.get(url)
        if response.status_code == 200:
            self.profile_data = response.json()
        else:
            raise Exception("User not found or API rate limit exceeded")

    def fetch_repositories(self):
        url = f"https://api.github.com/users/{self.username}/repos"
        response = requests.get(url)
        if response.status_code == 200:
            self.repos = response.json()
        else:
            raise Exception("Error fetching repositories")

    def display_profile(self):
        print("\n--- GitHub Profile ---")
        print(f"Name: {self.profile_data.get('name', 'N/A')}")
        print(f"Public Repos: {self.profile_data.get('public_repos', 0)}")
        print(f"Followers: {self.profile_data.get('followers', 0)}")
        print(f"Following: {self.profile_data.get('following', 0)}")
        print(f"Bio: {self.profile_data.get('bio', 'N/A')}")

    def display_repositories(self):
        print("\n--- Repositories ---")
        table = PrettyTable()
        table.field_names = ["Repo Name", "Stars", "Forks", "Language"]
        for repo in self.repos:
            table.add_row([repo['name'], repo['stargazers_count'], repo['forks_count'], repo.get('language', 'N/A')])
        print(table)


class GitHubDashboard:
    def _init_(self, username):
        self.user = GitHubUser(username)

    def load_data(self):
        self.user.fetch_profile()
        self.user.fetch_repositories()

    def show_dashboard(self):
        self.user.display_profile()
        self.user.display_repositories()


if _name_ == "_main_":
    username = input("Enter GitHub username: ")
    try:
        dashboard = GitHubDashboard(username)
        dashboard.load_data()
        dashboard.show_dashboard()
    except Exception as e:
        print("Error:", e)

