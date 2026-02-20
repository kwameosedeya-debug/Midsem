#Midsem
#include <iostream>
#include <limits>
#include "Student.h"
#include "AttendanceSession.h"
#include "FileManager.h"
#include "Utils.h"

using namespace std;

// Function prototypes
void displayMainMenu();
void handleStudentManagement(vector<Student>& students);
void handleSessionManagement(vector<AttendanceSession>& sessions, vector<Student>& students);
void handleReports(const vector<AttendanceSession>& sessions, const vector<Student>& students);

int main() {
    vector<Student> students;
    vector<AttendanceSession> sessions;
    
    // Load data from files
    FileManager::loadStudents(students);
    FileManager::loadSessions(sessions);
    FileManager::loadAttendanceRecords(sessions, students);
    
    int choice;
    
    do {
        Utils::clearScreen();
        displayMainMenu();
        cout << "Enter your choice: ";
        cin >> choice;
        
        // Input validation
        if (cin.fail()) {
            cin.clear();
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            cout << "Invalid input! Please enter a number.\n";
            Utils::pause();
            continue;
        }
        
        switch (choice) {
            case 1:
                handleStudentManagement(students);
                break;
            case 2:
                handleSessionManagement(sessions, students);
                break;
            case 3:
                handleReports(sessions, students);
                break;
            case 4:
                cout << "Saving data and exiting...\n";
                FileManager::saveStudents(students);
                FileManager::saveSessions(sessions);
                FileManager::saveAttendanceRecords(sessions);
                cout << "Thank you for using Digital Attendance System!\n";
                break;
            default:
                cout << "Invalid choice! Please try again.\n";
                Utils::pause();
        }
    } while (choice != 4);
    
    return 0;
}

void displayMainMenu() {
    cout << "====================================\n";
    cout << "   DIGITAL ATTENDANCE SYSTEM\n";
    cout << "====================================\n";
    cout << "1. Student Management\n";
    cout << "2. Attendance Session Management\n";
    cout << "3. Reports and Summary\n";
    cout << "4. Exit\n";
    cout << "====================================\n";
}

void handleStudentManagement(vector<Student>& students) {
    int choice;
    
    do {
        Utils::clearScreen();
        cout << "====================================\n";
        cout << "      STUDENT MANAGEMENT\n";
        cout << "====================================\n";
        cout << "1. Register New Student\n";
        cout << "2. View All Registered Students\n";
        cout << "3. Search Student by Index Number\n";
        cout << "4. Update Student Details\n";
        cout << "5. Back to Main Menu\n";
        cout << "====================================\n";
        cout << "Enter your choice: ";
        cin >> choice;
        
        switch (choice) {
            case 1: {
                // Register new student
                string name, indexNo, course;
                cout << "Enter student name: ";
                cin.ignore();
                getline(cin, name);
                cout << "Enter index number: ";
                getline(cin, indexNo);
                cout << "Enter course: ";
                getline(cin, course);
                
                // Check if index number already exists
                bool exists = false;
                for (const auto& s : students) {
                    if (s.getIndexNumber() == indexNo) {
                        exists = true;
                        break;
                    }
                }
                
                if (exists) {
                    cout << "Student with this index number already exists!\n";
                } else {
                    Student newStudent(name, indexNo, course);
                    students.push_back(newStudent);
                    cout << "Student registered successfully!\n";
                }
                Utils::pause();
                break;
            }
            case 2: {
                // View all students
                if (students.empty()) {
                    cout << "No students registered yet.\n";
                } else {
                    cout << "\nRegistered Students:\n";
                    cout << "------------------------------------\n";
                    for (const auto& student : students) {
                        student.displayInfo();
                        cout << "------------------------------------\n";
                    }
                }
                Utils::pause();
                break;
            }
            case 3: {
                // Search student by index number
                string indexNo;
                cout << "Enter index number to search: ";
                cin.ignore();
                getline(cin, indexNo);
                
                bool found = false;
                for (const auto& student : students) {
                    if (student.getIndexNumber() == indexNo) {
                        cout << "\nStudent Found:\n";
                        student.displayInfo();
                        found = true;
                        break;
                    }
                }
                
                if (!found) {
                    cout << "No student found with index number: " << indexNo << endl;
                }
                Utils::pause();
                break;
            }
            case 4: {
                // Update student details
                string indexNo;
                cout << "Enter index number of student to update: ";
                cin.ignore();
                getline(cin, indexNo);
                
                bool found = false;
                for (auto& student : students) {
                    if (student.getIndexNumber() == indexNo) {
                        string newName, newCourse;
                        cout << "Enter new name (or press Enter to skip): ";
                        getline(cin, newName);
                        cout << "Enter new course (or press Enter to skip): ";
                        getline(cin, newCourse);
                        
                        if (!newName.empty()) student.setName(newName);
                        if (!newCourse.empty()) student.setCourse(newCourse);
                        
                        cout << "Student details updated successfully!\n";
                        found = true;
                        break;
                    }
                }
                
                if (!found) {
                    cout << "No student found with index number: " << indexNo << endl;
                }
                Utils::pause();
                break;
            }
            case 5:
                cout << "Returning to main menu...\n";
                break;
            default:
                cout << "Invalid choice! Please try again.\n";
                Utils::pause();
        }
    } while (choice != 5);
}

void handleSessionManagement(vector<AttendanceSession>& sessions, vector<Student>& students) {
    int choice;
    
    do {
        Utils::clearScreen();
        cout << "====================================\n";
        cout << "   ATTENDANCE SESSION MANAGEMENT\n";
        cout << "====================================\n";
        cout << "1. Create New Lecture Session\n";
        cout << "2. Mark Attendance\n";
        cout << "3. Update Attendance\n";
        cout << "4. View All Sessions\n";
        cout << "5. Back to Main Menu\n";
        cout << "====================================\n";
        cout << "Enter your choice: ";
        cin >> choice;
        
        switch (choice) {
            case 1: {
                // Create new session
                if (students.empty()) {
                    cout << "Please register students first!\n";
                    Utils::pause();
                    break;
                }
                
                string courseCode, date, startTime;
                int duration;
                
                cout << "Enter course code: ";
                cin.ignore();
                getline(cin, courseCode);
                cout << "Enter date (DD/MM/YYYY): ";
                getline(cin, date);
                cout << "Enter start time (HH:MM): ";
                getline(cin, startTime);
                cout << "Enter duration (minutes): ";
                cin >> duration;
                
                AttendanceSession newSession(courseCode, date, startTime, duration);
                
                // Initialize attendance for all students
                for (const auto& student : students) {
                    newSession.addAttendanceRecord(student.getIndexNumber(), "Absent");
                }
                
                sessions.push_back(newSession);
                cout << "Session created successfully!\n";
                Utils::pause();
                break;
            }
            case 2: {
                // Mark attendance
                if (sessions.empty()) {
                    cout << "No sessions available. Please create a session first.\n";
                    Utils::pause();
                    break;
                }
                
                // Display available sessions
                cout << "\nAvailable Sessions:\n";
                for (size_t i = 0; i < sessions.size(); i++) {
                    cout << i + 1 << ". ";
                    sessions[i].displaySessionInfo();
                }
                
                int sessionIndex;
                cout << "Select session number: ";
                cin >> sessionIndex;
                
                if (sessionIndex < 1 || sessionIndex > sessions.size()) {
                    cout << "Invalid session number!\n";
                    Utils::pause();
                    break;
                }
                
                sessions[sessionIndex - 1].markAttendance(students);
                Utils::pause();
                break;
            }
            case 3: {
                // Update attendance
                if (sessions.empty()) {
                    cout << "No sessions available.\n";
                    Utils::pause();
                    break;
                }
                
                // Display available sessions
                cout << "\nAvailable Sessions:\n";
                for (size_t i = 0; i < sessions.size(); i++) {
                    cout << i + 1 << ". ";
                    sessions[i].displaySessionInfo();
                }
                
                int sessionIndex;
                cout << "Select session number: ";
                cin >> sessionIndex;
                
                if (sessionIndex < 1 || sessionIndex > sessions.size()) {
                    cout << "Invalid session number!\n";
                    Utils::pause();
                    break;
                }
                
                string indexNo, newStatus;
                cout << "Enter student index number: ";
                cin.ignore();
                getline(cin, indexNo);
                cout << "Enter new status (Present/Absent/Late): ";
                getline(cin, newStatus);
                
                if (sessions[sessionIndex - 1].updateAttendance(indexNo, newStatus)) {
                    cout << "Attendance updated successfully!\n";
                } else {
                    cout << "Failed to update attendance. Check index number.\n";
                }
                Utils::pause();
                break;
            }
            case 4: {
                // View all sessions
                if (sessions.empty()) {
                    cout << "No sessions available.\n";
                } else {
                    cout << "\nAll Sessions:\n";
                    cout << "====================================\n";
                    for (const auto& session : sessions) {
                        session.displaySessionInfo();
                        cout << "------------------------------------\n";
                    }
                }
                Utils::pause();
                break;
            }
            case 5:
                cout << "Returning to main menu...\n";
                break;
            default:
                cout << "Invalid choice! Please try again.\n";
                Utils::pause();
        }
    } while (choice != 5);
}

void handleReports(const vector<AttendanceSession>& sessions, const vector<Student>& students) {
    int choice;
    
    do {
        Utils::clearScreen();
        cout << "====================================\n";
        cout << "       REPORTS AND SUMMARY\n";
        cout << "====================================\n";
        cout << "1. View Attendance for a Session\n";
        cout << "2. Generate Attendance Summary\n";
        cout << "3. Student Attendance Report\n";
        cout << "4. Back to Main Menu\n";
        cout << "====================================\n";
        cout << "Enter your choice: ";
        cin >> choice;
        
        switch (choice) {
            case 1: {
                // View attendance for a session
                if (sessions.empty()) {
                    cout << "No sessions available.\n";
                    Utils::pause();
                    break;
                }
                
                cout << "\nAvailable Sessions:\n";
                for (size_t i = 0; i < sessions.size(); i++) {
                    cout << i + 1 << ". ";
                    sessions[i].displaySessionInfo();
                }
                
                int sessionIndex;
                cout << "Select session number: ";
                cin >> sessionIndex;
                
                if (sessionIndex < 1 || sessionIndex > sessions.size()) {
                    cout << "Invalid session number!\n";
                } else {
                    sessions[sessionIndex - 1].displayAttendanceList(students);
                }
                Utils::pause();
                break;
            }
            case 2: {
                // Generate attendance summary
                if (sessions.empty()) {
                    cout << "No sessions available.\n";
                } else {
                    cout << "\nATTENDANCE SUMMARY\n";
                    cout << "====================================\n";
                    
                    for (const auto& session : sessions) {
                        cout << "\nSession: ";
                        session.displaySessionInfo();
                        auto summary = session.getAttendanceSummary();
                        cout << "Present: " << summary.present 
                             << " | Absent: " << summary.absent 
                             << " | Late: " << summary.late << endl;
                    }
                }
                Utils::pause();
                break;
            }
            case 3: {
                // Student attendance report
                if (students.empty()) {
                    cout << "No students registered.\n";
                    Utils::pause();
                    break;
                }
                
                string indexNo;
                cout << "Enter student index number: ";
                cin.ignore();
                getline(cin, indexNo);
                
                // Find student
                const Student* foundStudent = nullptr;
                for (const auto& student : students) {
                    if (student.getIndexNumber() == indexNo) {
                        foundStudent = &student;
                        break;
                    }
                }
                
                if (!foundStudent) {
                    cout << "Student not found!\n";
                    Utils::pause();
                    break;
                }
                
                cout << "\nATTENDANCE REPORT FOR:\n";
                foundStudent->displayInfo();
                cout << "------------------------------------\n";
                
                int present = 0, absent = 0, late = 0;
                
                for (const auto& session : sessions) {
                    auto status = session.getStudentAttendance(indexNo);
                    if (status == "Present") present++;
                    else if (status == "Absent") absent++;
                    else if (status == "Late") late++;
                }
                
                cout << "Total Sessions: " << sessions.size() << endl;
                cout << "Present: " << present << endl;
                cout << "Absent: " << absent << endl;
                cout << "Late: " << late << endl;
                
                if (!sessions.empty()) {
                    double attendancePercentage = (present + late) * 100.0 / sessions.size();
                    cout << "Attendance Percentage: " << attendancePercentage << "%" << endl;
                }
                Utils::pause();
                break;
            }
            case 4:
                cout << "Returning to main menu...\n";
                break;
            default:
                cout << "Invalid choice! Please try again.\n";
                Utils::pause();
        }
    } while (choice != 4);
}#ifndef STUDENT_H
#define STUDENT_H

#include <string>
using namespace std;

class Student {
private:
    string name;
    string indexNumber;
    string course;
    
public:
    // Constructors
    Student();
    Student(string name, string indexNumber, string course);
    
    // Getters
    string getName() const;
    string getIndexNumber() const;
    string getCourse() const;
    
    // Setters
    void setName(string name);
    void setIndexNumber(string indexNumber);
    void setCourse(string course);
    
    // Display
    void displayInfo() const;
    
    // Serialization for file storage
    string toString() const;
    static Student fromString(const string& data);
};

#endif
