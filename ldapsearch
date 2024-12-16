import subprocess
import re
from myapp.models import GitlabUser  # Import your Django model

def run_ldapsearch(base_dn, search_filter, ldap_server):
    try:
        # Construct the ldapsearch command
        command = [
            "ldapsearch",
            "-x",  # Use simple authentication
            "-H", ldap_server,  # LDAP server URL
            "-b", base_dn,  # Base DN for the search
            search_filter,  # Search filter
            "mail"  # Attribute to fetch (email addresses)
        ]

        # Run the ldapsearch command and capture the output
        result = subprocess.run(command, stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)

        # Check if the command was successful
        if result.returncode != 0:
            print("Error running ldapsearch:", result.stderr)
            return []

        # Extract email addresses from the output using regex
        email_pattern = re.compile(r"^mail:\s*(.+)$", re.MULTILINE)
        emails = email_pattern.findall(result.stdout)

        return emails

    except Exception as e:
        print("An error occurred:", e)
        return []

if __name__ == "__main__":
    # Replace with your LDAP server and base DN
    ldap_server = "ldap://your-ldap-server.com"
    base_dn = "dc=example,dc=com"

    print("Fetching email addresses for GitLab users...")

    # Fetch all GitLab users from the database
    users = GitlabUser.objects.all()

    for user in users:
        search_filter = f"(uid={user.username})"  # Assuming 'username' is the field in your model
        email_addresses = run_ldapsearch(base_dn, search_filter, ldap_server)

        if email_addresses:
            print(f"Found email addresses for {user.username}:")
            for email in email_addresses:
                print(email)
        else:
            print(f"No email addresses found for {user.username} or an error occurred.")
