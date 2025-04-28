# Library-Management-System-Using-SQL
-- Create Database
CREATE DATABASE LibraryDB;
USE LibraryDB;

-- Create Books Table
CREATE TABLE Books (
    BookID INT PRIMARY KEY,
    Title VARCHAR(100),
    Author VARCHAR(100),
    Genre VARCHAR(50),
    Quantity INT
);

-- Create Members Table
CREATE TABLE Members (
    MemberID INT PRIMARY KEY,
    Name VARCHAR(100),
    Phone VARCHAR(15),
    Email VARCHAR(100)
);

-- Create Transactions Table
CREATE TABLE Transactions (
    TransactionID INT PRIMARY KEY,
    BookID INT,
    MemberID INT,
    IssueDate DATE,
    ReturnDate DATE,
    FOREIGN KEY (BookID) REFERENCES Books(BookID),
    FOREIGN KEY (MemberID) REFERENCES Members(MemberID)
);

-- Insert Sample Data into Books
INSERT INTO Books (BookID, Title, Author, Genre, Quantity)
VALUES 
(1, 'The Alchemist', 'Paulo Coelho', 'Fiction', 5),
(2, 'Clean Code', 'Robert C. Martin', 'Programming', 3),
(3, 'Atomic Habits', 'James Clear', 'Self-Help', 4);

-- Insert Sample Data into Members
INSERT INTO Members (MemberID, Name, Phone, Email)
VALUES
(101, 'Alice Johnson', '1234567890', 'alice@gmail.com'),
(102, 'Bob Smith', '0987654321', 'bob@gmail.com');

-- Insert Sample Data into Transactions
INSERT INTO Transactions (TransactionID, BookID, MemberID, IssueDate, ReturnDate)
VALUES
(1001, 1, 101, '2025-04-25', NULL),
(1002, 2, 102, '2025-04-26', NULL);

-- View All Books
SELECT * FROM Books;

-- View All Members
SELECT * FROM Members;

-- View Issued Books (Active Transactions)
SELECT t.TransactionID, b.Title, m.Name, t.IssueDate
FROM Transactions t
JOIN Books b ON t.BookID = b.BookID
JOIN Members m ON t.MemberID = m.MemberID
WHERE t.ReturnDate IS NULL;

-- Issue a New Book
INSERT INTO Transactions (TransactionID, BookID, MemberID, IssueDate, ReturnDate)
VALUES (1003, 3, 101, '2025-04-28', NULL);

-- Update Book Quantity after Issue
UPDATE Books
SET Quantity = Quantity - 1
WHERE BookID = 3;

-- Return a Book
UPDATE Transactions
SET ReturnDate = '2025-04-29'
WHERE TransactionID = 1001;

-- Update Book Quantity after Return
UPDATE Books
SET Quantity = Quantity + 1
WHERE BookID = 1;

-- Find the Most Borrowed Book
SELECT BookID, COUNT(*) AS TimesBorrowed
FROM Transactions
GROUP BY BookID
ORDER BY TimesBorrowed DESC
LIMIT 1;

-- List All Overdue Books (Assuming 7-day borrow period)
SELECT t.TransactionID, b.Title, m.Name, t.IssueDate
FROM Transactions t
JOIN Books b ON t.BookID = b.BookID
JOIN Members m ON t.MemberID = m.MemberID
WHERE t.ReturnDate IS NULL AND t.IssueDate <= DATE_SUB(CURDATE(), INTERVAL 7 DAY);
