# customer.io
import sqlite3

# Function to create the database table if not exists
def create_table():
    conn = sqlite3.connect('customer.db')
    c = conn.cursor()
    c.execute('''CREATE TABLE IF NOT EXISTS customers
                 (id INTEGER PRIMARY KEY, username TEXT, password TEXT, balance REAL)''')
    conn.commit()
    conn.close()

# Function to add a new customer to the database
def add_customer(username, password):
    conn = sqlite3.connect('customer.db')
    c = conn.cursor()
    c.execute("INSERT INTO customers (username, password, balance) VALUES (?, ?, ?)", (username, password, 0))
    conn.commit()
    conn.close()

# Function to validate customer login
def validate_login(username, password):
    conn = sqlite3.connect('customer.db')
    c = conn.cursor()
    c.execute("SELECT * FROM customers WHERE username=? AND password=?", (username, password))
    customer = c.fetchone()
    conn.close()
    return customer

# Function to deposit amount
def deposit_amount(username, amount):
    conn = sqlite3.connect('customer.db')
    c = conn.cursor()
    c.execute("SELECT balance FROM customers WHERE username=?", (username,))
    current_balance = c.fetchone()[0]
    new_balance = current_balance + amount
    c.execute("UPDATE customers SET balance=? WHERE username=?", (new_balance, username))
    conn.commit()
    conn.close()
    return new_balance

# Function to withdraw amount
def withdraw_amount(username, amount):
    conn = sqlite3.connect('customer.db')
    c = conn.cursor()
    c.execute("SELECT balance FROM customers WHERE username=?", (username,))
    current_balance = c.fetchone()[0]
    if amount > current_balance:
        conn.close()
        return -1  # Insufficient balance
    new_balance = current_balance - amount
    c.execute("UPDATE customers SET balance=? WHERE username=?", (new_balance, username))
    conn.commit()
    conn.close()
    return new_balance

# Function to check balance
def check_balance(username):
    conn = sqlite3.connect('customer.db')
    c = conn.cursor()
    c.execute("SELECT balance FROM customers WHERE username=?", (username,))
    balance = c.fetchone()[0]
    conn.close()
    return balance

# Function to display account details menu
def account_menu(username):
    while True:
        print("\nAccount Details")
        print("a) Amount Deposit")
        print("b) Amount Withdrawal")
        print("c) Check Balance")
        print("d) Exit")
        
        choice = input("Enter your choice: ")
        
        if choice == 'a':
            amount = float(input("Enter amount to deposit: "))
            while amount <= 0:
                print("Amount should be greater than 0.")
                amount = float(input("Enter amount to deposit: "))
            new_balance = deposit_amount(username, amount)
            print(f"Deposit successful! Current balance: {new_balance}")
        elif choice == 'b':
            amount = float(input("Enter amount to withdraw: "))
            while amount <= 0:
                print("Amount should be greater than 0.")
                amount = float(input("Enter amount to withdraw: "))
            new_balance = withdraw_amount(username, amount)
            if new_balance == -1:
                print("Insufficient balance.")
            else:
                print(f"Withdrawal successful! Current balance: {new_balance}")
        elif choice == 'c':
            balance = check_balance(username)
            print(f"Current balance: {balance}")
        elif choice == 'd':
            break
        else:
            print("Invalid choice. Please try again.")

# Function to display main menu
def menu():
    while True:
        print("\nCustomer")
        print("1. Customer Login")
        print("2. New Customer Sign in")
        print("3. Exit")
        
        choice = input("Enter your choice: ")
        
        if choice == '1':
            username = input("Enter username: ")
            password = input("Enter password: ")
            customer = validate_login(username, password)
            if customer:
                print(f"Welcome {username}!")
                account_menu(username)
            else:
                print("Not a valid customer.")
        elif choice == '2':
            username = input("Enter new username: ")
            password = input("Enter password: ")
            add_customer(username, password)
            print("Customer added successfully!")
        elif choice == '3':
            print("Exited Application successfully…")
            break
        else:
            print("Invalid choice. Please try again.")

# Main function to run the application
def main():
    create_table()
    menu()

if __name__ == "__main__":
    main()


