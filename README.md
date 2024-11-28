#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Maximum limits
#define MAX_USERS 100
#define MAX_MENU_ITEMS 50
#define MAX_ORDERS 10

// Structures
typedef struct {
    char username[50];
    char password[50];
} User;

typedef struct {
    char itemName[50];
    float price;
} MenuItem;

typedef struct {
    char username[50];
    MenuItem items[MAX_ORDERS];
    int quantities[MAX_ORDERS];
    int itemCount;
    float total;
} Order;

// Globals
User users[MAX_USERS];
MenuItem menu[MAX_MENU_ITEMS];
Order orders[MAX_USERS];
int userCount = 0, menuCount = 0, orderCount = 0;

// Function prototypes
void createAccount();
void login();
void browseMenu();
void placeOrder(const char* username);
void generateInvoice(const Order* order);

int main() {
    int choice;
    char loggedInUser[50];

    // Sample menu
    strcpy(menu[0].itemName, "Pizza");
    menu[0].price = 12.99;
    strcpy(menu[1].itemName, "Burger");
    menu[1].price = 8.49;
    strcpy(menu[2].itemName, "Pasta");
    menu[2].price = 10.99;
    menuCount = 3;

    printf("Welcome to the Restaurant Order and Billing System\n");

    while (1) {
        printf("\n1. Create Account\n2. Login\n3. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                createAccount();
                break;
            case 2:
                login(loggedInUser);
                if (strlen(loggedInUser) > 0) {
                    int userChoice;
                    do {
                        printf("\nWelcome, %s\n", loggedInUser);
                        printf("1. Browse Menu\n2. Place Order\n3. Logout\n");
                        printf("Enter your choice: ");
                        scanf("%d", &userChoice);
                        switch (userChoice) {
                            case 1:
                                browseMenu();
                                break;
                            case 2:
                                placeOrder(loggedInUser);
                                break;
                            case 3:
                                printf("Logged out successfully.\n");
                                strcpy(loggedInUser, "");
                                break;
                            default:
                                printf("Invalid choice. Try again.\n");
                        }
                    } while (strlen(loggedInUser) > 0);
                }
                break;
            case 3:
                printf("Exiting the system. Thank you!\n");
                return 0;
            default:
                printf("Invalid choice. Try again.\n");
        }
    }
}

// Functions
void createAccount() {
    if (userCount >= MAX_USERS) {
        printf("User limit reached. Cannot create more accounts.\n");
        return;
    }

    printf("Enter username: ");
    scanf("%s", users[userCount].username);
    printf("Enter password: ");
    scanf("%s", users[userCount].password);

    userCount++;
    printf("Account created successfully!\n");
}

void login(char* loggedInUser) {
    char username[50], password[50];
    printf("Enter username: ");
    scanf("%s", username);
    printf("Enter password: ");
    scanf("%s", password);

    for (int i = 0; i < userCount; i++) {
        if (strcmp(users[i].username, username) == 0 &&
            strcmp(users[i].password, password) == 0) {
            printf("Login successful!\n");
            strcpy(loggedInUser, username);
            return;
        }
    }
    printf("Invalid username or password. Try again.\n");
    loggedInUser[0] = '\0';
}

void browseMenu() {
    printf("\nMenu:\n");
    for (int i = 0; i < menuCount; i++) {
        printf("%d. %s - $%.2f\n", i + 1, menu[i].itemName, menu[i].price);
    }
}

void placeOrder(const char* username) {
    Order newOrder;
    strcpy(newOrder.username, username);
    newOrder.itemCount = 0;
    newOrder.total = 0;

    int choice, quantity;
    do {
        browseMenu();
        printf("Enter the item number to order (0 to finish): ");
        scanf("%d", &choice);

        if (choice > 0 && choice <= menuCount) {
            printf("Enter quantity for %s: ", menu[choice - 1].itemName);
            scanf("%d", &quantity);

            newOrder.items[newOrder.itemCount] = menu[choice - 1];
            newOrder.quantities[newOrder.itemCount] = quantity;
            newOrder.total += menu[choice - 1].price * quantity;
            newOrder.itemCount++;
        } else if (choice != 0) {
            printf("Invalid item number. Try again.\n");
        }
    } while (choice != 0);

    if (newOrder.itemCount > 0) {
        printf("Order placed successfully!\n");
        orders[orderCount++] = newOrder;
        generateInvoice(&newOrder);
    } else {
        printf("No items ordered.\n");
    }
}

void generateInvoice(const Order* order) {
    printf("\nInvoice for %s\n", order->username);
    printf("----------------------------\n");
    for (int i = 0; i < order->itemCount; i++) {
        printf("%s x %d = $%.2f\n", order->items[i].itemName,
               order->quantities[i],
               order->items[i].price * order->quantities[i]);
    }
    printf("Total: $%.2f\n", order->total);
    printf("----------------------------\n");
}
