#include <iostream>
#include <vector>
#include <string>
using namespace std;

/* =======================
   Book Class
   ======================= */
class Book {
private:
    int id;
    string title;
    string author;
    bool isBorrowed;

public:
    Book(int id, string title, string author) {
        this->id = id;
        this->title = title;
        this->author = author;
        this->isBorrowed = false;
    }

    int getId() {
        return id;
    }

    string getTitle() {
        return title;
    }

    string getAuthor() {
        return author;
    }

    bool getStatus() {
        return isBorrowed;
    }

    void borrowBook() {
        isBorrowed = true;
    }

    void returnBook() {
        isBorrowed = false;
    }

    void display() {
        cout << "ID: " << id
             << ", Title: " << title
             << ", Author: " << author
             << ", Status: " << (isBorrowed ? "Borrowed" : "Available")
             << endl;
    }
};

/* =======================
   User Class
   ======================= */
class User {
private:
    int userId;
    string name;
    vector<int> borrowedBooks;

public:
    User(int userId, string name) {
        this->userId = userId;
        this->name = name;
    }

    int getUserId() {
        return userId;
    }

    string getName() {
        return name;
    }

    void borrowBook(int bookId) {
        borrowedBooks.push_back(bookId);
    }

    void returnBook(int bookId) {
        for (int i = 0; i < borrowedBooks.size(); i++) {
            if (borrowedBooks[i] == bookId) {
                borrowedBooks.erase(borrowedBooks.begin() + i);
                break;
            }
        }
    }
};

/* =======================
   Library Class
   ======================= */
class Library {
private:
    vector<Book> books;
    vector<User> users;

public:
    void addBook(Book book) {
        books.push_back(book);
        cout << "Book added successfully.\n";
    }

    void addUser(User user) {
        users.push_back(user);
        cout << "User added successfully.\n";
    }

    Book* searchBookById(int bookId) {
        for (int i = 0; i < books.size(); i++) {
            if (books[i].getId() == bookId) {
                return &books[i];
            }
        }
        return nullptr;
    }

    User* searchUserById(int userId) {
        for (int i = 0; i < users.size(); i++) {
            if (users[i].getUserId() == userId) {
                return &users[i];
            }
        }
        return nullptr;
    }

    void borrowBook(int userId, int bookId) {
        User* user = searchUserById(userId);
        Book* book = searchBookById(bookId);

        if (user == nullptr) {
            cout << "User not found.\n";
            return;
        }

        if (book == nullptr) {
            cout << "Book not found.\n";
            return;
        }

        if (book->getStatus()) {
            cout << "Book already borrowed.\n";
            return;
        }

        book->borrowBook();
        user->borrowBook(bookId);
        cout << "Book borrowed successfully.\n";
    }

    void returnBook(int userId, int bookId) {
        User* user = searchUserById(userId);
        Book* book = searchBookById(bookId);

        if (user == nullptr || book == nullptr) {
            cout << "Invalid user or book.\n";
            return;
        }

        if (!book->getStatus()) {
            cout << "Book is not borrowed.\n";
            return;
        }

        book->returnBook();
        user->returnBook(bookId);
        cout << "Book returned successfully.\n";
    }

    void displayBooks() {
        cout << "\n--- Library Books ---\n";
        for (int i = 0; i < books.size(); i++) {
            books[i].display();
        }
    }
};

/* =======================
   Main Function (Tests)
   ======================= */
int main() {
    Library library;

    // Add books (Positive test)
    library.addBook(Book(1, "C++ Programming", "Bjarne Stroustrup"));
    library.addBook(Book(2, "Data Structures", "Mark Allen"));
    library.addBook(Book(3, "Operating Systems", "Silberschatz"));

    // Add users
    library.addUser(User(101, "Nathan"));
    library.addUser(User(102, "Alice"));

    library.displayBooks();

    // Borrow book (Positive)
    cout << "\nBorrowing Book ID 1 by User 101\n";
    library.borrowBook(101, 1);

    // Borrow same book again (Negative)
    cout << "\nBorrowing Book ID 1 again\n";
    library.borrowBook(102, 1);

    library.displayBooks();

    // Return book (Positive)
    cout << "\nReturning Book ID 1 by User 101\n";
    library.returnBook(101, 1);

    // Return already returned book (Negative)
    cout << "\nReturning Book ID 1 again\n";
    library.returnBook(101, 1);

    library.displayBooks();

    return 0;
} 
