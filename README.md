# Dashboard
> A modern, responsive dashboard for visualizing and managing key business metrics.
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

