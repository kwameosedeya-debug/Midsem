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
    
     
}

void handleReports(const vector<AttendanceSession>& sessions, const vector<Student>& students) {
    int 
                
                
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
    

};

#endif
