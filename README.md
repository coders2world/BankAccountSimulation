import java.util.ArrayList;
import java.util.Scanner;

class Transaction {
    private String type;
    private double amount;
    private double balanceAfter;
    private String date;
    
    public Transaction(String type, double amount, double balanceAfter) {
        this.type = type;
        this.amount = amount;
        this.balanceAfter = balanceAfter;
        this.date = java.time.LocalDateTime.now().format(
            java.time.format.DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")
        );
    }
    
    public String getType() { return type; }
    public double getAmount() { return amount; }
    public double getBalanceAfter() { return balanceAfter; }
    public String getDate() { return date; }
}

// Base Account class
class Account {
    protected String accountNumber;
    protected String accountHolder;
    protected double balance;
    protected ArrayList<Transaction> transactionHistory;
    
    public Account(String accountNumber, String accountHolder, double initialBalance) {
        this.accountNumber = accountNumber;
        this.accountHolder = accountHolder;
        this.balance = initialBalance;
        this.transactionHistory = new ArrayList<>();
        if (initialBalance > 0) {
            transactionHistory.add(new Transaction("INITIAL", initialBalance, balance));
        }
    }
    
    // Method Overloading: deposit with different parameters
    // Overload 1: deposit with amount only
    public void deposit(double amount) {
        if (amount <= 0) {
            System.out.println("Invalid amount! Deposit must be positive.");
            return;
        }
        balance += amount;
        transactionHistory.add(new Transaction("DEPOSIT", amount, balance));
        System.out.println("Deposited: $" + String.format("%.2f", amount));
        System.out.println("Current Balance: $" + String.format("%.2f", balance));
    }
    
    // Overload 2: deposit with amount and description
    public void deposit(double amount, String description) {
        if (amount <= 0) {
            System.out.println("Invalid amount! Deposit must be positive.");
            return;
        }
        balance += amount;
        transactionHistory.add(new Transaction("DEPOSIT-" + description, amount, balance));
        System.out.println("Deposited: $" + String.format("%.2f", amount) + " (" + description + ")");
        System.out.println("Current Balance: $" + String.format("%.2f", balance));
    }
    
    // Method Overloading: withdraw with different parameters
    // Overload 1: withdraw with amount only
    public void withdraw(double amount) {
        if (amount <= 0) {
            System.out.println("Invalid amount! Withdrawal must be positive.");
            return;
        }
        if (amount > balance) {
            System.out.println("Insufficient funds! Available balance: $" + String.format("%.2f", balance));
            return;
        }
        balance -= amount;
        transactionHistory.add(new Transaction("WITHDRAW", amount, balance));
        System.out.println("Withdrawn: $" + String.format("%.2f", amount));
        System.out.println("Current Balance: $" + String.format("%.2f", balance));
    }
    
    // Overload 2: withdraw with amount and purpose
    public void withdraw(double amount, String purpose) {
        if (amount <= 0) {
            System.out.println("Invalid amount! Withdrawal must be positive.");
            return;
        }
        if (amount > balance) {
            System.out.println("Insufficient funds! Available balance: $" + String.format("%.2f", balance));
            return;
        }
        balance -= amount;
        transactionHistory.add(new Transaction("WITHDRAW-" + purpose, amount, balance));
        System.out.println("Withdrawn: $" + String.format("%.2f", amount) + " (Purpose: " + purpose + ")");
        System.out.println("Current Balance: $" + String.format("%.2f", balance));
    }
    
    // Method that will be overridden
    public void displayAccountType() {
        System.out.println("Account Type: Regular Account");
    }
    
    // Method that will be overridden
    public double calculateInterest() {
        return 0.0; // Regular accounts have no interest
    }
    
    public void displayBalance() {
        System.out.println("\n" + createTableHeader("ACCOUNT INFORMATION", 50));
        displayAccountType(); // Polymorphic call
        System.out.println(formatTableRow("Account Number", accountNumber, 50));
        System.out.println(formatTableRow("Account Holder", accountHolder, 50));
        System.out.println(formatTableRow("Current Balance", "$" + String.format("%.2f", balance), 50));
        System.out.println(createTableFooter(50));
    }
    
    public void displayTransactionHistory() {
        int width = 90;
        System.out.println("\n" + createTableHeader("TRANSACTION HISTORY", width));
        
        String headerFormat = "| %-4s | %-18s | %-15s | %-10s | %-15s |";
        System.out.println(String.format(headerFormat, "No.", "Date & Time", "Type", "Amount", "Balance After"));
        System.out.println(createTableSeparator(width));
        
        if (transactionHistory.isEmpty()) {
            System.out.println(centerText("No transactions yet", width));
        } else {
            int count = 1;
            for (Transaction t : transactionHistory) {
                String dataFormat = "| %-4d | %-18s | %-15s | $%-9.2f | $%-14.2f |";
                System.out.println(String.format(dataFormat,
                    count++,
                    t.getDate(),
                    t.getType(),
                    t.getAmount(),
                    t.getBalanceAfter()
                ));
            }
        }
        
        System.out.println(createTableFooter(width));
    }
    
    // Helper methods
    protected String createTableHeader(String title, int width) {
        StringBuilder sb = new StringBuilder();
        sb.append("+").append("-".repeat(width - 2)).append("+\n");
        sb.append(centerText(title, width)).append("\n");
        sb.append("+").append("-".repeat(width - 2)).append("+");
        return sb.toString();
    }
    
    protected String createTableSeparator(int width) {
        return "+" + "-".repeat(width - 2) + "+";
    }
    
    protected String createTableFooter(int width) {
        return "+" + "-".repeat(width - 2) + "+";
    }
    
    protected String centerText(String text, int width) {
        int padding = (width - text.length() - 2) / 2;
        int extraPadding = (width - text.length() - 2) % 2;
        return "|" + " ".repeat(padding) + text + " ".repeat(padding + extraPadding) + "|";
    }
    
    protected String formatTableRow(String label, String value, int width) {
        String content = label + ": " + value;
        return "| " + content + " ".repeat(width - content.length() - 4) + " |";
    }
    
    public double getBalance() { return balance; }
    public String getAccountNumber() { return accountNumber; }
    public String getAccountHolder() { return accountHolder; }
}

// SavingsAccount class - demonstrates Method Overriding
class SavingsAccount extends Account {
    private double interestRate;
    
    public SavingsAccount(String accountNumber, String accountHolder, double initialBalance) {
        super(accountNumber, accountHolder, initialBalance);
        this.interestRate = 3.5; // 3.5% interest rate
    }
    
    // Method Overriding: Override displayAccountType()
    @Override
    public void displayAccountType() {
        System.out.println("Account Type: Savings Account (Interest Rate: " + interestRate + "%)");
    }
    
    // Method Overriding: Override calculateInterest()
    @Override
    public double calculateInterest() {
        return balance * (interestRate / 100);
    }
    
    // New method specific to SavingsAccount
    public void applyInterest() {
        double interest = calculateInterest();
        balance += interest;
        transactionHistory.add(new Transaction("INTEREST", interest, balance));
        System.out.println("Interest Applied: $" + String.format("%.2f", interest));
        System.out.println("New Balance: $" + String.format("%.2f", balance));
    }
}

public class BankAccountSimulation {
    
    private static void displayMenu() {
        int width = 40;
        System.out.println("\n" + createTableBorder(width, "top"));
        System.out.println(centerTextInTable("BANKING MENU", width));
        System.out.println(createTableBorder(width, "middle"));
        
        String[] menuItems = {
            "1. Deposit Money",
            "2. Deposit with Description",
            "3. Withdraw Money",
            "4. Withdraw with Purpose",
            "5. Check Balance",
            "6. View Transaction History",
            "7. Apply Interest (Savings Only)",
            "8. Exit"
        };
        
        for (String item : menuItems) {
            System.out.println(leftAlignInTable(item, width));
        }
        
        System.out.println(createTableBorder(width, "bottom"));
    }
    
    private static String createTableBorder(int width, String position) {
        return "+" + "-".repeat(width - 2) + "+";
    }
    
    private static String centerTextInTable(String text, int width) {
        int padding = (width - text.length() - 2) / 2;
        int extraPadding = (width - text.length() - 2) % 2;
        return "|" + " ".repeat(padding) + text + " ".repeat(padding + extraPadding) + "|";
    }
    
    private static String leftAlignInTable(String text, int width) {
        return "| " + text + " ".repeat(width - text.length() - 3) + "|";
    }
    
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        System.out.println("\n" + createTableBorder(50, "top"));
        System.out.println(centerTextInTable("WELCOME TO BANK ACCOUNT SIMULATOR", 50));
        System.out.println(createTableBorder(50, "bottom"));
        
        System.out.print("\nEnter Account Number: ");
        String accNum = sc.nextLine();
        
        System.out.print("Enter Account Holder Name: ");
        String accHolder = sc.nextLine();
        
        System.out.print("Enter Initial Balance: $");
        double initialBalance = sc.nextDouble();
        sc.nextLine(); // consume newline
        
        System.out.print("Account Type (1 for Regular, 2 for Savings): ");
        int accountType = sc.nextInt();
        sc.nextLine(); // consume newline
        
        Account account;
        if (accountType == 2) {
            account = new SavingsAccount(accNum, accHolder, initialBalance);
        } else {
            account = new Account(accNum, accHolder, initialBalance);
        }
        
        while (true) {
            displayMenu();
            System.out.print("Enter your choice: ");
            
            int choice = sc.nextInt();
            sc.nextLine(); // consume newline
            
            switch (choice) {
                case 1:
                    // Method Overloading - calling deposit(double)
                    System.out.print("\nEnter deposit amount: $");
                    double depositAmount = sc.nextDouble();
                    sc.nextLine();
                    account.deposit(depositAmount);
                    break;
                    
                case 2:
                    // Method Overloading - calling deposit(double, String)
                    System.out.print("\nEnter deposit amount: $");
                    double depositAmount2 = sc.nextDouble();
                    sc.nextLine();
                    System.out.print("Enter description: ");
                    String description = sc.nextLine();
                    account.deposit(depositAmount2, description);
                    break;
                    
                case 3:
                    // Method Overloading - calling withdraw(double)
                    System.out.print("\nEnter withdrawal amount: $");
                    double withdrawAmount = sc.nextDouble();
                    sc.nextLine();
                    account.withdraw(withdrawAmount);
                    break;
                    
                case 4:
                    // Method Overloading - calling withdraw(double, String)
                    System.out.print("\nEnter withdrawal amount: $");
                    double withdrawAmount2 = sc.nextDouble();
                    sc.nextLine();
                    System.out.print("Enter purpose: ");
                    String purpose = sc.nextLine();
                    account.withdraw(withdrawAmount2, purpose);
                    break;
                    
                case 5:
                    // Method Overriding - polymorphic call to displayAccountType()
                    account.displayBalance();
                    break;
                    
                case 6:
                    account.displayTransactionHistory();
                    break;
                    
                case 7:
                    // Method Overriding - applying interest for savings account
                    if (account instanceof SavingsAccount) {
                        ((SavingsAccount) account).applyInterest();
                    } else {
                        System.out.println("Interest is only available for Savings Accounts!");
                    }
                    break;
                    
                case 8:
                    System.out.println("\n" + createTableBorder(50, "top"));
                    System.out.println(centerTextInTable("Thank you for using our service!", 50));
                    System.out.println(createTableBorder(50, "bottom") + "\n");
                    sc.close();
                    System.exit(0);
                    
                default:
                    System.out.println("Invalid choice! Please try again.");
            }
        }
    }
}
