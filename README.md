
import jso
from datetime import datetime, date
from pathlib import Path

FILE = Path("study_sessions.json")

def load_sessions():
    if FILE.exists():
        try:
            return json.loads(FILE.read_text(encoding="utf-8"))
        except json.JSONDecodeError:
            return []
    return []

def save_sessions(sessions):
    FILE.write_text(
        json.dumps(sessions, indent=4, ensure_ascii=False),
        encoding="utf-8"
    )

def add_session(sessions):
    subject = input("Subject: ").strip()

    if not subject:
        print("Subject cannot be empty.")
        return

    try:
        minutes = int(input("Study duration (minutes): "))
        if minutes <= 0:
            print("Duration must be positive.")
            return
    except ValueError:
        print("Please enter a valid number.")
        return

    session = {
        "subject": subject,
        "minutes": minutes,
        "date": date.today().isoformat(),
        "time": datetime.now().strftime("%H:%M")
    }

    sessions.append(session)
    save_sessions(sessions)
    print("Study session saved!")

def show_sessions(sessions):
    if not sessions:
        print("No study sessions yet.")
        return

    print("\n--- Study History ---")
    for index, session in enumerate(sessions, 1):
        print(
            f"{index}. {session['subject']} - "
            f"{session['minutes']} minutes - "
            f"{session['date']} {session['time']}"
        )

def show_statistics(sessions):
    total = sum(item["minutes"] for item in sessions)
    today = date.today().isoformat()

    today_total = sum(
        item["minutes"]
        for item in sessions
        if item["date"] == today
    )

    print("\n--- Statistics ---")
    print(f"Total sessions: {len(sessions)}")
    print(f"Total study time: {total // 60}h {total % 60}m")
    print(f"Today's study time: {today_total} minutes")

def main():
    sessions = load_sessions()

    while True:
        print("\n=== Study Session Tracker ===")
        print("1. Add study session")
        print("2. View study history")
        print("3. View statistics")
        print("4. Exit")

        choice = input("Choose an option: ").strip()

        if choice == "1":
            add_session(sessions)
        elif choice == "2":
            show_sessions(sessions)
        elif choice == "3":
            show_statistics(sessions)
        elif choice == "4":
            print("Goodbye!")
            break
        else:
            print("Invalid choice. Try again.")

if __name__ == "__main__":
    main()
