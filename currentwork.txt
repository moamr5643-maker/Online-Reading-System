#include <iostream>
#include <vector>
#include <string>
#include <map>
#include <algorithm>

using namespace std;

// Helper to convert number to string
string btos(int i) {
    if (i == 0) return "0";
    string s = "";
    while (i > 0) {
        int d = i % 10;
        char c = (d + '0');
        s += c;
        i /= 10;
    }
    reverse(s.begin(), s.end());
    return s;
}

////////////////////////////////////// Book Entity ///////////////////////////////////////
class Book {
private:
    int id;
    string name;
    string author;
    vector<string> pages;

public:
    Book(int id, string name, string author, int total_pages) 
        : id(id), name(name), author(author) {
        for (int i = 1; i <= total_pages; i++) {
            string content = " Hello everybody  \n---------\n---------\n---------\n---------\n this is page number : " + btos(i);
            pages.push_back(content);
        }
    }

    int getId() const { return id; }
    string getName() const { return name; }
    string getAuthor() const { return author; }
    int getPagesCount() const { return pages.size(); }

    string getPageContent(int page_num) const {
        if (page_num < 0 || page_num >= (int)pages.size()) return "Not Exist!!";
        return pages[page_num];
    }
};

////////////////////////////////////// BooksManager ///////////////////////////////////////
class BooksManager {
private:
    vector<Book> books;
    int cnt_id = 0;

public:
    void add_book(string name, string author, int pages) {
        books.push_back(Book(cnt_id, name, author, pages));
        cnt_id++;
    }

    void view_books() const {
        if (books.empty()) {
            cout << "No books available right now!\n";
            return;
        }
        for (const auto& book : books) {
            cout << "ID : " << book.getId() << " | Book Name : " << book.getName() << " | Author : " << book.getAuthor() << '\n';
        }
    }

    bool is_valid_book(int id) const {
        return id >= 0 && id < cnt_id;
    }

    string getpage(int book_id, int page) const {
        if (!is_valid_book(book_id)) return "Not Exist!!";
        return books[book_id].getPageContent(page);
    }
};

////////////////////////////////////// Session Entity //////////////////////////////////////////
class session {
private:
    int book_id;
    int session_id;

public:
    int lastpage;

    session() : book_id(-1), session_id(-1), lastpage(0) {}
    session(int b_id, int s_id, int p) : book_id(b_id), session_id(s_id), lastpage(p) {}

    void setbookid(int id) { book_id = id; }
    void setsessionid(int id) { session_id = id; }
    void setsessionlastpage(int lastp) { lastpage = lastp; }

    int getbookid() const { return book_id; }
    int getsessionid() const { return session_id; }

    void print() const {
        cout << "book id : " << book_id << " | session id : " << session_id << " | last page : " << lastpage << '\n';
    }
};

/////////////////////////////////// Manage Sessions //////////////////////////////////////
class manage_sessions {
private:
    vector<session> sessions;
    session current_session;
    int cnt_session = 0;

public:
    void show_sessions() const {
        if (sessions.empty()) {
            cout << "No sessions exist, let's start reading ;)\n";
            return;
        }
        for (const auto& s : sessions) {
            s.print();
        }
    }

    bool open_session(int bookid, const BooksManager& booksManager) {
        if (!booksManager.is_valid_book(bookid)) return false;
        
        current_session = session(bookid, cnt_session, 0);
        current_session.print();
        cnt_session++;
        return true;
    }

    bool open_current_session(int sessionid) {
        if (sessionid < 0 || sessionid >= (int)sessions.size()) {
            return false;
        }
        current_session = sessions[sessionid];
        current_session.print();
        return true;
    }

    bool endsession() {
        // If session exists update it, otherwise add new one
        bool found = false;
        for (auto& s : sessions) {
            if (s.getsessionid() == current_session.getsessionid()) {
                s = current_session;
                found = true;
                break;
            }
        }
        if (!found) {
            sessions.push_back(current_session);
        }
        return true;
    }

    string nextpage(const BooksManager& booksManager) {
        current_session.lastpage++;
        string s = booksManager.getpage(current_session.getbookid(), current_session.lastpage);
        if (s == "Not Exist!!") current_session.lastpage--;
        return s;
    }

    string prevpage(const BooksManager& booksManager) {
        if (current_session.lastpage > 0) {
            current_session.lastpage--;
        }
        return booksManager.getpage(current_session.getbookid(), current_session.lastpage);
    }
};

//////////////////////////////////// Profile Entity /////////////////////////////////
class profile {
private:
    string name;
    string email;
    bool isadmin;
    manage_sessions user_sessions; // Every user owns their sessions directly

public:
    profile() : name(""), email(""), isadmin(false) {}
    profile(string name, string email, bool isadmin) 
        : name(name), email(email), isadmin(isadmin) {}

    void show_profile() const {
        cout << "Name : " << name << " | email : " << email << " | role : " << (isadmin ? "Admin" : "Customer") << "\n";
    }

    string get_email() const { return email; }
    string get_name() const { return name; }
    bool get_isadmin() const { return isadmin; }

    manage_sessions& get_sessions_manager() { return user_sessions; }
};

///////////////////////// Manage Profiles ////////////////////////////////////////
class manageprofile {
private:
    map<string, profile> profiles;

public:
    void show_profiles() const {
        if (profiles.empty()) {
            cout << "No users registered yet.\n";
            return;
        }
        for (const auto& pair : profiles) {
            pair.second.show_profile();
        }
    }

    bool add_profile(const profile& new_prof) {
        string email = new_prof.get_email();
        if (profiles.find(email) == profiles.end()) {
            profiles[email] = new_prof;
            return true;
        }
        return false;
    }

    profile* get_profile(string email) {
        if (profiles.find(email) != profiles.end()) {
            return &profiles[email];
        }
        return nullptr;
    }
};

///////////////////////// VIEW & CONTROLLER //////////////////////

int option;
void hellopage() {
    cout << "\nHELLO :) \n 1- Sign up / Login \n Enter your option: ";
    cin >> option;
}

int choice;
void printchoices() {
    cout << "\n1- View Your Profile\n2- View Your sessions\n3- List all existing books\n";
    cout << "4- Open existing session\n5- Open new session\n6- Quit\n\nEnter Your choice : ";
    cin >> choice;
}

int sessionop;
void printsessionoptions() {
    cout << "\n1- NEXT \n2- PREV \n3- End session!!\n\nEnter your option : ";
    cin >> sessionop;
}

int main() {
    manageprofile Manageprofileo;
    BooksManager booksManager;

    while (true) {
        hellopage();
        string name, email;
        bool isadmin = false;

        if (option == 1) {
            cout << "Enter your name : ";
            cin >> name;
            cout << "Enter your email : ";
            cin >> email;
            cout << "IF You are admin Enter 1, otherwise Enter any number : ";
            int x;
            cin >> x;
            if (x == 1) isadmin = true;

            profile new_prof(name, email, isadmin);
            if (Manageprofileo.add_profile(new_prof)) {
                cout << "Your Profile is added Successfully!!\n";
            } else {
                cout << "Welcome Back! Profile loaded successfully.\n";
            }
        } else {
            cout << "Enter a valid option!!\n";
            continue;
        }

        profile* current_user = Manageprofileo.get_profile(email);
        if (!current_user) continue;

        cout << "\nHELLO " << current_user->get_name() << " :)\n";

        if (!current_user->get_isadmin()) {
            while (true) {
                printchoices();
                if (choice == 1) {
                    current_user->show_profile();
                } else if (choice == 2) {
                    current_user->get_sessions_manager().show_sessions();
                } else if (choice == 3) {
                    booksManager.view_books();
                } else if (choice == 4) {
                    cout << "Enter Session ID : ";
                    int id;
                    cin >> id;
                    if (current_user->get_sessions_manager().open_current_session(id)) {
                        while (true) {
                            printsessionoptions();
                            if (sessionop == 1) {
                                cout << "\n--- Page Content ---\n" << current_user->get_sessions_manager().nextpage(booksManager) << "\n--------------------\n";
                            } else if (sessionop == 2) {
                                cout << "\n--- Page Content ---\n" << current_user->get_sessions_manager().prevpage(booksManager) << "\n--------------------\n";
                            } else if (sessionop == 3) {
                                current_user->get_sessions_manager().endsession();
                                break;
                            }
                        }
                    } else {
                        cout << "Invalid Session ID!!\n";
                    }
                } else if (choice == 5) {
                    cout << "Enter Book ID : ";
                    int id;
                    cin >> id;
                    if (current_user->get_sessions_manager().open_session(id, booksManager)) {
                        while (true) {
                            printsessionoptions();
                            if (sessionop == 1) {
                                cout << "\n--- Page Content ---\n" << current_user->get_sessions_manager().nextpage(booksManager) << "\n--------------------\n";
                            } else if (sessionop == 2) {
                                cout << "\n--- Page Content ---\n" << current_user->get_sessions_manager().prevpage(booksManager) << "\n--------------------\n";
                            } else if (sessionop == 3) {
                                current_user->get_sessions_manager().endsession();
                                break;
                            }
                        }
                    } else {
                        cout << "Invalid Book ID!!\n";
                    }
                } else if (choice == 6) {
                    cout << "Good Bye, see you soon :)\n";
                    break;
                } else {
                    cout << "Invalid option!!\n";
                }
            }
        } else {
            while (true) {
                cout << "\n1- Add Book\n2- View Your profile\n3- View customers profiles\n4- Logout\n\nEnter your option : ";
                int op;
                cin >> op;
                if (op == 1) {
                    cout << "Enter book name : ";
                    string nameb;
                    cin >> nameb;
                    cout << "Enter Book Author : ";
                    string Authorb;
                    cin >> Authorb;
                    cout << "Enter Book pages : ";
                    int p;
                    cin >> p;
                    booksManager.add_book(nameb, Authorb, p);
                    cout << "Book added successfully!\n";
                } else if (op == 2) {
                    current_user->show_profile();
                } else if (op == 3) {
                    Manageprofileo.show_profiles();
                } else if (op == 4) {
                    break;
                } else {
                    cout << "Invalid option\n";
                }
            }
        }
    }
    return 0;
}