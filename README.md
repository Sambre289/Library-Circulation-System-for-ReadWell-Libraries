# Library-Circulation-System-for-ReadWell-Libraries

Aim :
To design a simple and effective library circulation system for ReadWell Libraries, enabling tracking of book availability, borrowing, and return activity through a user-friendly Python and Streamlit interface.

Tools & Libraries Used :
Python 3.x
pandas – data manipulation
streamlit – web UI for interaction
datetime – timestamping reports
Jupyter Notebook – development & testing environment

Stepwise Implementation :
[1] — Importing Required Libraries:

import pandas as pd
from datetime import datetime

[2] — Book List Initialization:

book_list = [
    ("1984", "George Orwell", "available"),
    ("To Kill a Mockingbird", "Harper Lee", "available"),
    ("The Great Gatsby", "F. Scott Fitzgerald", "borrowed")
]

<img width="885" height="225" alt="image" src="https://github.com/user-attachments/assets/2b9fb857-ee29-49c2-a075-da62f3bfdca9" />


[3] — add_book() Function:

def add_book(title, author):
    title_lower = title.lower()
    if title_lower in book_titles:
        print(f"Book '{title}' already exists.")
    else:
        book_list.append((title, author, "available"))
        book_titles.add(title_lower)
        print(f"Book '{title}' by {author} added.")
<img width="887" height="206" alt="image" src="https://github.com/user-attachments/assets/3942ef0b-3f9a-4650-ac5c-e7f80e142411" />


[4] — view_available_books() Function:

def view_available_books():
    df = pd.DataFrame(book_list, columns=["Title", "Author", "Status"])
    available_books = df[df["Status"] == "available"]
    print(available_books)
    return available_books
<img width="878" height="137" alt="image" src="https://github.com/user-attachments/assets/eff402f1-0d9d-4acc-98c0-67d42b5c4399" />


[5] — update_status() Function:

def update_status(title, action):
    global book_list
    updated = False
    for i, (book_title, author, status) in enumerate(book_list):
        if book_title.lower() == title.lower():
            if action == "checkout" and status == "available":
                book_list[i] = (book_title, author, "borrowed")
                print(f"You have borrowed '{book_title}'.")
                updated = True
            elif action == "return" and status == "borrowed":
                book_list[i] = (book_title, author, "available")
                print(f"You have returned '{book_title}'.")
                updated = True
            else:
                print(f"Book is already '{status}' or invalid action.")
                updated = True
    if not updated:
        print(f"Book '{title}' not found.")
  <img width="878" height="450" alt="image" src="https://github.com/user-attachments/assets/be82e403-6a9c-4684-bcfa-e6f2fe19761f" />


[6] — export_loan_report() Function:

def export_loan_report():
    df = pd.DataFrame(book_list, columns=["Title", "Author", "Status"])
    filename = "loan_report.csv"  # Fixed filename
    df.to_csv(filename, index=False)
    print(f"Loan report saved as {filename}")
<img width="882" height="132" alt="image" src="https://github.com/user-attachments/assets/bc08244f-6dd3-49e3-aab7-6f3c1ae922de" />

[7] — load_books_from_csv() Function:

def load_books_from_csv(filename="loan_report.csv"):
    df = pd.read_csv(filename)
    return list(df.itertuples(index=False, name=None))
<img width="885" height="91" alt="image" src="https://github.com/user-attachments/assets/f971bb0f-065b-4c3e-884a-dca1aee15363" />

App.py :
import streamlit as st
import pandas as pd

# Initialize session state on first run
if "books" not in st.session_state:
    st.session_state.books = [
        ("1984", "George Orwell", "available"),
        ("To Kill a Mockingbird", "Harper Lee", "available"),
        ("The Great Gatsby", "F. Scott Fitzgerald", "borrowed")
    ]
    st.session_state.book_titles = set([b[0].lower() for b in st.session_state.books])

st.title("ReadWell Library Circulation System")

#  Add New Book
st.header("Add New Book")
new_title = st.text_input("Enter Book Title")
new_author = st.text_input("Enter Author Name")

if st.button("Add Book"):
    if new_title.strip() == "" or new_author.strip() == "":
        st.warning("Please enter both title and author.")
    elif new_title.lower() in st.session_state.book_titles:
        st.error(f"Book '{new_title}' already exists!")
    else:
        st.session_state.books.append((new_title, new_author, "available"))
        st.session_state.book_titles.add(new_title.lower())
        st.success(f"Book '{new_title}' added successfully!")

# View Available Books
st.header("Available Books")
df_books = pd.DataFrame(st.session_state.books, columns=["Title", "Author", "Status"])
st.dataframe(df_books[df_books["Status"] == "available"])

# Borrow / Return Section
st.header("Borrow / Return Book")
action = st.radio("Action", ["checkout", "return"], horizontal=True)
target_title = st.text_input("Enter Book Title to Borrow/Return")

if st.button("Submit"):
    updated = False
    for i, (title, author, status) in enumerate(st.session_state.books):
        if title.lower() == target_title.lower():
            if action == "checkout" and status == "available":
                st.session_state.books[i] = (title, author, "borrowed")
                st.success(f"You have borrowed '{title}'.")
                updated = True
            elif action == "return" and status == "borrowed":
                st.session_state.books[i] = (title, author, "available")
                st.success(f"You have returned '{title}'.")
                updated = True
            else:
                st.error(f"Invalid action. Book is already '{status}'.")
                updated = True
    if not updated:
        st.error("Book not found.")

# Export Loan Report
st.header(" Export Loan Report")
if st.button("Download CSV Report"):
    df_export = pd.DataFrame(st.session_state.books, columns=["Title", "Author", "Status"])
    csv = df_export.to_csv(index=False)
    st.download_button("Download Loan Report", csv, file_name="loan_report.csv", mime="text/csv")

Files inside the folder :
<img width="930" height="212" alt="image" src="https://github.com/user-attachments/assets/c56814c7-7842-4cf3-b435-a1745e7a76e4" />

Run Streamlit App Command:
 python -m streamlit run app.py

 OUTPUT:
 <img width="1918" height="1026" alt="image" src="https://github.com/user-attachments/assets/0a85c58c-b362-4fb0-a274-c0dae66a98e2" />
<img width="1918" height="1020" alt="image" src="https://github.com/user-attachments/assets/3d7acd3a-e8a6-4fc3-a480-5d67eb7c5aca" />

