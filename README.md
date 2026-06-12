import json
import base64
from pathlib import Path

DB_FILE = "passwords.json"


class PasswordManager:
    def __init__(self):
        self.db = Path(DB_FILE)

        if not self.db.exists():
            self.db.write_text("{}")

    def load(self):
        with open(self.db, "r", encoding="utf-8") as f:
            return json.load(f)

    def save(self, data):
        with open(self.db, "w", encoding="utf-8") as f:
            json.dump(data, f, indent=4)

    def encrypt(self, text):
        return base64.b64encode(text.encode()).decode()

    def decrypt(self, text):
        return base64.b64decode(text.encode()).decode()

    def add_password(self):
        website = input("Website: ")
        username = input("Username: ")
        password = input("Password: ")

        data = self.load()

        data[website] = {
            "username": username,
            "password": self.encrypt(password)
        }

        self.save(data)

        print("Saved successfully.")

    def get_password(self):
        website = input("Website: ")

        data = self.load()

        if website not in data:
            print("Not found.")
            return

        print("\nCredentials")
        print("-" * 30)
        print("Username:", data[website]["username"])
        print(
            "Password:",
            self.decrypt(data[website]["password"])
        )

    def list_websites(self):
        data = self.load()

        print("\nSaved Websites")
        print("-" * 30)

        for site in data:
            print(site)


def main():
    manager = PasswordManager()

    while True:
        print("\nPassword Manager")
        print("1. Add Password")
        print("2. Get Password")
        print("3. List Websites")
        print("4. Exit")

        choice = input("Select: ")

        if choice == "1":
            manager.add_password()

        elif choice == "2":
            manager.get_password()

        elif choice == "3":
            manager.list_websites()

        elif choice == "4":
            break

        else:
            print("Invalid option")


if __name__ == "__main__":
    main()
