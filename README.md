# atu-c-midsem-project
Digital assessment project 
#include <iostream>
#include <fstream>
#include <vector>
#include <string>

using namespace std;

/* ===========================
   STUDENT CLASS
=========================== */
class Student {
public:
    string name;
    string indexNumber;

    Student() {}

    Student(string n, string i) {
        name = n;
        indexNumber = i;
    }

    void display() {
        cout << "Name: " << name << " | Index: " << indexNumber << endl;
    }

    string toFileFormat() {
        return name + "," + indexNumber;
    }
};

/* ===========================
   ATTENDANCE RECORD STRUCT
=========================== */
struct AttendanceRecord {
    string indexNumber;
    string status;  // Present, Absent, Late
};

/* ===========================
   ATTENDANCE SESSION CLASS
=========================== */
class AttendanceSession {
public:
    string courseCode;
    string date;
    string startTime;
    int duration;

    vector<AttendanceRecord> records;

    void createSession() {
        cout << "Enter Course Code: ";
        cin >> courseCode;

        cout << "Enter Date (YYYY_MM_DD): ";
        cin >> date;

        cout << "Enter Start Time: ";
        cin >> startTime;

        cout << "Enter Duration (minutes): ";
        cin >> duration;
    }

    void markAttendance(vector<Student>& students) {
        records.clear();

        for (auto& student : students) {
            AttendanceRecord record;
            record.indexNumber = student.indexNumber;

            cout << "Mark attendance for "
                 << student.name << " (1=Present, 2=Absent, 3=Late): ";

            int choice;
            cin >> choice;

            if (choice == 1)
                record.status = "Present";
            else if (choice == 2)
                record.status = "Absent";
            else
                record.status = "Late";

            records.push_back(record);
        }
    }

    void displayReport() {
        int present = 0, absent = 0, late = 0;

        cout << "\nAttendance Report\n";
        cout << "Course: " << courseCode << " | Date: " << date << endl;

        for (auto& record : records) {
            cout << "Index: " << record.indexNumber
                 << " | Status: " << record.status << endl;

            if (record.status == "Present") present++;
            else if (record.status == "Absent") absent++;
            else if (record.status == "Late") late++;
        }

        cout << "\nSummary:\n";
        cout << "Present: " << present << endl;
        cout << "Absent: " << absent << endl;
        cout << "Late: " << late << endl;
    }

    void saveSession() {
        string filename = "session_" + courseCode + "_" + date + ".txt";
        ofstream file(filename);

        file << courseCode << endl;
        file << date << endl;
        file << startTime << endl;
        file << duration << endl;

        for (auto& record : records) {
            file << record.indexNumber << "," << record.status << endl;
        }

        file.close();
        cout << "Session saved successfully.\n";
    }
};

/* ===========================
   GLOBAL STUDENT FUNCTIONS
=========================== */

void saveStudents(vector<Student>& students) {
    ofstream file("students.txt");

    for (auto& student : students) {
        file << student.toFileFormat() << endl;
    }

    file.close();
}

void loadStudents(vector<Student>& students) {
    ifstream file("students.txt");
    string line;

    while (getline(file, line)) {
        int commaPos = line.find(",");
        string name = line.substr(0, commaPos);
        string index = line.substr(commaPos + 1);

        students.push_back(Student(name, index));
    }

    file.close();
}

void registerStudent(vector<Student>& students) {
    string name, index;

    cin.ignore();
    cout << "Enter Student Name: ";
    getline(cin, name);

    cout << "Enter Index Number: ";
    cin >> index;

    students.push_back(Student(name, index));
    saveStudents(students);

    cout << "Student registered successfully.\n";
}

void viewStudents(vector<Student>& students) {
    for (auto& student : students) {
        student.display();
    }
}

void searchStudent(vector<Student>& students) {
    string index;
    cout << "Enter Index Number to search: ";
    cin >> index;

    for (auto& student : students) {
        if (student.indexNumber == index) {
            student.display();
            return;
        }
    }

    cout << "Student not found.\n";
}

/* ===========================
   MAIN FUNCTION
=========================== */

int main() {
    vector<Student> students;
    AttendanceSession session;

    loadStudents(students);

    int choice;

    do {
        cout << "\n===== DIGITAL ATTENDANCE SYSTEM =====\n";
        cout << "1. Register Student\n";
        cout << "2. View Students\n";
        cout << "3. Search Student\n";
        cout << "4. Create Session\n";
        cout << "5. Mark Attendance\n";
        cout << "6. View Attendance Report\n";
        cout << "7. Save Session\n";
        cout << "8. Exit\n";
        cout << "Enter choice: ";
        cin >> choice;

        switch (choice) {
            case 1:
                registerStudent(students);
                break;

            case 2:
                viewStudents(students);
                break;

            case 3:
                searchStudent(students);
                break;

            case 4:
                session.createSession();
                break;

            case 5:
                session.markAttendance(students);
                break;

            case 6:
                session.displayReport();
                break;

            case 7:
                session.saveSession();
                break;

            case 8:
                cout << "Exiting program...\n";
                break;

            default:
                cout << "Invalid option.\n";
        }

    } while (choice != 8);

    return 0;
}
