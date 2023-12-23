# Metapointer

import java.util.HashMap;
import java.util.Map;
import java.util.Random;
import java.util.Scanner;

class User {
    String phoneNum;
    double availableAmount;

    public User(String phoneNum, double availableAmount) {
        this.phoneNum = phoneNum;
        this.availableAmount = availableAmount;
    }
}

class Transaction {
    String from;
    String to;
    double amount;

    public Transaction(String from, String to, double amount) {
        this.from = from;
        this.to = to;
        this.amount = amount;
    }
}

public class GooglePayClone {
    private Map<String, User> users = new HashMap<>();
    private Map<String, Transaction> transactions = new HashMap<>();

    public static void main(String[] args) {
        GooglePayClone googlePay = new GooglePayClone();
        googlePay.start();
    }

    private void start() {
        Scanner scanner = new Scanner(System.in);

        while (true) {
            System.out.println("1. Log In");
            System.out.println("2. Exit");
            System.out.print("Select an option: ");
            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume the newline character

            switch (choice) {
                case 1:
                    loginUser(scanner);
                    break;
                case 2:
                    System.out.println("Exiting...");
                    System.exit(0);
                    break;
                default:
                    System.out.println("Invalid option. Try again.");
            }
        }
    }

    private void loginUser(Scanner scanner) {
        System.out.print("Enter your phone number: ");
        String phoneNum = scanner.nextLine();

        if (!users.containsKey(phoneNum)) {
            System.out.print("First log-in. Enter initial amount: ");
            double initialAmount = scanner.nextDouble();
            scanner.nextLine(); // Consume the newline character
            users.put(phoneNum, new User(phoneNum, initialAmount));
        }

        User currentUser = users.get(phoneNum);

        System.out.println("Available Amount: $" + currentUser.availableAmount);

        System.out.print("Enter the recipient's phone number: ");
        String recipientPhoneNum = scanner.nextLine();

        System.out.print("Enter the amount to transfer: $");
        double transferAmount = scanner.nextDouble();
        scanner.nextLine(); // Consume the newline character

        if (currentUser.availableAmount >= transferAmount) {
            handleTransaction(currentUser, users.get(recipientPhoneNum), transferAmount);
        } else {
            System.out.println("Insufficient funds. Transaction failed.");
        }
    }

    private void handleTransaction(User sender, User recipient, double amount) {
        sender.availableAmount -= amount;
        recipient.availableAmount += amount;

        Transaction transaction = new Transaction(sender.phoneNum, recipient.phoneNum, amount);
        transactions.put(transaction.from + transaction.to, transaction);

        handleCashback(amount);

        System.out.println("Transaction successful!");
        System.out.println("Cashback details: " + getCashbackDetails(amount));
    }

    private void handleCashback(double amount) {
        if (amount % 500 != 0) {
            if (amount < 1000) {
                // 5% cashback
                users.get(users.keySet().iterator().next()).availableAmount += amount * 0.05;
            } else {
                // 2% cashback
                users.get(users.keySet().iterator().next()).availableAmount += amount * 0.02;
            }
        } else {
            // Randomly give a coupon or notify the user
            Random random = new Random();
            if (random.nextBoolean()) {
                System.out.println("Congratulations! You've won a coupon.");
            } else {
                System.out.println("Better luck next time. No cashback this time.");
            }
        }
    }

    private String getCashbackDetails(double amount) {
        if (amount % 500 != 0) {
            if (amount < 1000) {
                return "5% cashback applied.";
            } else {
                return "2% cashback applied.";
            }
        } else {
            Random random = new Random();
            if (random.nextBoolean()) {
                return "Congratulations! You've won a coupon.";
            } else {
                return "Better luck next time. No cashback this time.";
            }
        }
    }
}
