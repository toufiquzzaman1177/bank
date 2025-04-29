#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <conio.h>

struct account {
    int acc_no;
    char name[50];
    float balance;
};

void create_account();
void deposit_money();
void withdraw_money();
void check_balance();
void display_account();
void delete_account();

int main() {
    int choice;

    printf("\n\n\t\t===== ZAMAN BANK LTD =====\n");

    while(1) {
        printf("\n\n\t\tMAIN MENU");
        printf("\n\t\t1. CREATE NEW ACCOUNT");
        printf("\n\t\t2. DEPOSIT MONEY");
        printf("\n\t\t3. WITHDRAW MONEY");
        printf("\n\t\t4. CHECK BALANCE");
        printf("\n\t\t5. DISPLAY ACCOUNT DETAILS");
        printf("\n\t\t6. DELETE ACCOUNT");
        printf("\n\t\t7. EXIT");
        printf("\n\t\tEnter your choice: ");
        scanf("%d", &choice);

        switch(choice) {
            case 1:
                create_account();
                break;
            case 2:
                deposit_money();
                break;
            case 3:
                withdraw_money();
                break;
            case 4:
                check_balance();
                break;
            case 5:
                display_account();
                break;
            case 6:
                delete_account();
                break;
            case 7:
                printf("\n\t\tThank you for using our banking system!\n");
                exit(0);
            default:
                printf("\n\t\tInvalid choice! Please try again.");
        }
    }
    return 0;
}

void create_account() {
    FILE *fp;
    struct account customer;

    fp = fopen("accounts.dat", "ab+");
    if(fp == NULL) {
        printf("\n\t\tError opening file!");
        return;
    }

    printf("\n\t\tEnter account number: ");
    scanf("%d", &customer.acc_no);

    printf("\t\tEnter customer name: ");
    fflush(stdin);
    gets(customer.name);

    printf("\t\tEnter initial deposit: ");
    scanf("%f", &customer.balance);

    fwrite(&customer, sizeof(customer), 1, fp);
    fclose(fp);

    printf("\n\t\tAccount created successfully!");
}

void deposit_money() {
    FILE *fp, *temp;
    struct account customer;
    int acc_no, found = 0;
    float amount;

    fp = fopen("accounts.dat", "rb+");
    if(fp == NULL) {
        printf("\n\t\tError opening file!");
        return;
    }

    temp = fopen("temp.dat", "wb");
    if(temp == NULL) {
        printf("\n\t\tError creating temporary file!");
        fclose(fp);
        return;
    }

    printf("\n\t\tEnter account number: ");
    scanf("%d", &acc_no);

    while(fread(&customer, sizeof(customer), 1, fp) == 1) {
        if(customer.acc_no == acc_no) {
            found = 1;
            printf("\n\t\tCurrent balance: %.2f", customer.balance);
            printf("\n\t\tEnter amount to deposit: ");
            scanf("%f", &amount);
            customer.balance += amount;
            printf("\n\t\tNew balance: %.2f", customer.balance);
        }
        fwrite(&customer, sizeof(customer), 1, temp);
    }

    fclose(fp);
    fclose(temp);

    if(!found) {
        printf("\n\t\tAccount not found!");
        remove("temp.dat");
    } else {
        remove("accounts.dat");
        rename("temp.dat", "accounts.dat");
        printf("\n\t\tDeposit successful!");
    }
}

void withdraw_money() {
    FILE *fp, *temp;
    struct account customer;
    int acc_no, found = 0;
    float amount;

    fp = fopen("accounts.dat", "rb+");
    if(fp == NULL) {
        printf("\n\t\tError opening file!");
        return;
    }

    temp = fopen("temp.dat", "wb");
    if(temp == NULL) {
        printf("\n\t\tError creating temporary file!");
        fclose(fp);
        return;
    }

    printf("\n\t\tEnter account number: ");
    scanf("%d", &acc_no);

    while(fread(&customer, sizeof(customer), 1, fp) == 1) {
        if(customer.acc_no == acc_no) {
            found = 1;
            printf("\n\t\tCurrent balance: %.2f", customer.balance);
            printf("\n\t\tEnter amount to withdraw: ");
            scanf("%f", &amount);

            if(amount > customer.balance) {
                printf("\n\t\tInsufficient balance!");
            } else {
                customer.balance -= amount;
                printf("\n\t\tNew balance: %.2f", customer.balance);
            }
        }
        fwrite(&customer, sizeof(customer), 1, temp);
    }

    fclose(fp);
    fclose(temp);

    if(!found) {
        printf("\n\t\tAccount not found!");
        remove("temp.dat");
    } else {
        remove("accounts.dat");
        rename("temp.dat", "accounts.dat");
        printf("\n\t\tWithdrawal successful!");
    }
}

void check_balance() {
    FILE *fp;
    struct account customer;
    int acc_no, found = 0;

    fp = fopen("accounts.dat", "rb");
    if(fp == NULL) {
        printf("\n\t\tError opening file!");
        return;
    }

    printf("\n\t\tEnter account number: ");
    scanf("%d", &acc_no);

    while(fread(&customer, sizeof(customer), 1, fp) == 1) {
        if(customer.acc_no == acc_no) {
            found = 1;
            printf("\n\t\tAccount Holder: %s", customer.name);
            printf("\n\t\tAccount Balance: %.2f", customer.balance);
            break;
        }
    }

    fclose(fp);

    if(!found) {
        printf("\n\t\tAccount not found!");
    }
}

void display_account() {
    FILE *fp;
    struct account customer;
    int acc_no, found = 0;

    fp = fopen("accounts.dat", "rb");
    if(fp == NULL) {
        printf("\n\t\tError opening file!");
        return;
    }

    printf("\n\t\tEnter account number: ");
    scanf("%d", &acc_no);

    while(fread(&customer, sizeof(customer), 1, fp) == 1) {
        if(customer.acc_no == acc_no) {
            found = 1;
            printf("\n\t\t===== ACCOUNT DETAILS =====");
            printf("\n\t\tAccount Number: %d", customer.acc_no);
            printf("\n\t\tAccount Holder: %s", customer.name);
            printf("\n\t\tAccount Balance: %.2f", customer.balance);
            break;
        }
    }

    fclose(fp);

    if(!found) {
        printf("\n\t\tAccount not found!");
    }
}

void delete_account() {
    FILE *fp, *temp;
    struct account customer;
    int acc_no, found = 0;

    fp = fopen("accounts.dat", "rb");
    if(fp == NULL) {
        printf("\n\t\tError opening file!");
        return;
    }

    temp = fopen("temp.dat", "wb");
    if(temp == NULL) {
        printf("\n\t\tError creating temporary file!");
        fclose(fp);
        return;
    }

    printf("\n\t\tEnter account number to delete: ");
    scanf("%d", &acc_no);

    while(fread(&customer, sizeof(customer), 1, fp) == 1) {
        if(customer.acc_no == acc_no) {
            found = 1;
            printf("\n\t\tAccount deleted successfully!");
        } else {
            fwrite(&customer, sizeof(customer), 1, temp);
        }
    }

    fclose(fp);
    fclose(temp);

    if(!found) {
        printf("\n\t\tAccount not found!");
        remove("temp.dat");
    } else {
        remove("accounts.dat");
        rename("temp.dat", "accounts.dat");
    }
}
