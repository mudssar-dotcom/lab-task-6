# lab-task-6
#include <iostream>
#include <string>
#include <sstream>   // for stringstream
using namespace std;

struct Book {
    int id;
    string title;
    string author;
};

const int MAX = 100;
int deletedStack[MAX];
int top = -1;

void push(int id) {
    if (top < MAX - 1) {
        deletedStack[++top] = id;
    } else {
        cout << "Stack Overflow!\n";
    }
}

int pop() {
    if (top >= 0) {
        return deletedStack[top--];
    }
    cout << "Nothing to undo.\n";
    return -1;
}

class Library {
private:
    Book* books;
    int capacity;
    int count;

public:
    Library(int size) {
        capacity = size;
        count = 0;
        books = new Book[capacity];
    }

    void addBook(int id, string title, string author) {
        if (count < capacity) {
            books[count].id = id;
            books[count].title = title;
            books[count].author = author;
            count++;
            cout << "Book added successfully!\n";
        } else {
            cout << "Library is full!\n";
        }
    }

    void deleteBook(int id) {
        for (int i = 0; i < count; i++) {
            if (books[i].id == id) {
                push(id);
                for (int j = i; j < count - 1; j++) {
                    books[j] = books[j + 1];
                }
                count--;
                cout << "Book deleted successfully!\n";
                return;
            }
        }
        cout << "Book not found!\n";
    }

    void undoDelete() {
        int id = pop();
        if (id != -1) {
            string title, author;
            cin.ignore();
            cout << "Re-enter details of deleted book (ID " << id << "):\n";
            cout << "Title: ";
            getline(cin, title);
            cout << "Author: ";
            getline(cin, author);
            addBook(id, title, author);
        }
    }

    void searchBook(int id) {
        for (int i = 0; i < count; i++) {
            if (books[i].id == id) {
                Book* ptr = &books[i];
                cout << "Book Found -> ID: " << ptr->id 
                     << ", Title: " << ptr->title 
                     << ", Author: " << ptr->author << endl;
                return;
            }
        }
        cout << "Book not found!\n";
    }

    void displayBooks() {
        if (count == 0) {
            cout << "No books available.\n";
            return;
        }

        cout << "\n----- Library Records -----\n";
        cout << "ID\tTitle\t\tAuthor\n";
        cout << "--------------------------------\n";

        string table[100][3];
        for (int i = 0; i < count; i++) {
            stringstream ss;
            ss << books[i].id;
            table[i][0] = ss.str();  // convert int -> string
            table[i][1] = books[i].title;
            table[i][2] = books[i].author;
            cout << table[i][0] << "\t" << table[i][1] << "\t\t" << table[i][2] << endl;
        }
    }
};

int main() {
    Library lib(100);
    int choice, id;
    string title, author;

    do {
        cout << "\n====== Library Menu ======\n";
        cout << "1. Add Book\n";
        cout << "2. Delete Book\n";
        cout << "3. Undo Delete\n";
        cout << "4. Search Book\n";
        cout << "5. Display All Books\n";
        cout << "6. Exit\n";
        cout << "Enter choice: ";
        cin >> choice;

        switch (choice) {
        case 1:
            cout << "Enter Book ID: ";
            cin >> id;
            cin.ignore();
            cout << "Enter Title: ";
            getline(cin, title);
            cout << "Enter Author: ";
            getline(cin, author);
            lib.addBook(id, title, author);
            break;
        case 2:
            cout << "Enter Book ID to delete: ";
            cin >> id;
            lib.deleteBook(id);
            break;
        case 3:
            lib.undoDelete();
            break;
        case 4:
            cout << "Enter Book ID to search: ";
            cin >> id;
            lib.searchBook(id);
            break;
        case 5:
            lib.displayBooks();
            break;
        case 6:
            cout << "Exiting...\n";
            break;
        default:
            cout << "Invalid choice!\n";
        }
    } while (choice != 6);

    return 0;
}
