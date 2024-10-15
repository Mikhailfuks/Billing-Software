#include <iostream>
#include <string>
#include <vector>
#include <iomanip>
#include <fstream>
#include <sstream>

using namespace std;

// Product Class
class Product {
public:
    int productId;
    string name;
    double price;

    Product(int productId, string name, double price) {
        this->productId = productId;
        this->name = name;
        this->price = price;
    }
};

// Invoice Class
class Invoice {
public:
    int invoiceId;
    string customerName;
    vector<pair<Product*, int>> items; // product and quantity

    Invoice(int invoiceId, string customerName) {
        this->invoiceId = invoiceId;
        this->customerName = customerName;
    }

    // Add an item to the invoice
    void addItem(Product* product, int quantity) {
        items.push_back(make_pair(product, quantity));
    }

    // Calculate total invoice amount
    double calculateTotal() {
        double total = 0;
        for (auto const& item : items) {
            total += item.first->price * item.second;
        }
        return total;
    }

    // Display the invoice
    void displayInvoice() {
        cout << "\nInvoice" << endl;
        cout << "Invoice ID: " << invoiceId << endl;
        cout << "Customer Name: " << customerName << endl;
        cout << "-------------------------" << endl;
        cout << "Product" << setw(20) << "Quantity" << setw(10) << "Price" << endl;
        cout << "-------------------------" << endl;
        for (auto const& item : items) {
            cout << item.first->name << setw(20) << item.second << setw(10) << item.first->price << endl;
        }
        cout << "-------------------------" << endl;
        cout << "Total: $" << calculateTotal() << endl;
    }
};

// Billing System Class
class BillingSystem {
private:
    vector<Product> products;
    vector<Invoice> invoices;
    int nextInvoiceId = 1;

public:
    // Load products from a file
    void loadProducts(const string& filename) {
        ifstream file(filename);
        if (file.is_open()) {
            int productId;
            string name;
            double price;
            while (file >> productId >> name >> price) {
                products.push_back(Product(productId, name, price));
            }
            file.close();
        } else {
            cout << "Error opening products file!" << endl;
        }
    }

    // Save products to a file
    void saveProducts(const string& filename) {
        ofstream file(filename);

        if (file.is_open()) {
            for (Product& product : products) {
                file << product.productId << " " << product.name << " " << product.price << endl;
            }
            file.close();
        } else {
            cout << "Error saving products file!" << endl;
        }
    }

    // Add a new product
    void addProduct(const string& name, double price) {
        products.push_back(Product(products.size() + 1, name, price));
        cout << "Product added successfully!" << endl;
    }

    // Get product by ID
    Product* getProduct(int productId) {
        for (Product& product : products) {
            if (product.productId == productId) {
                return &product;
            }
        }
        return nullptr;
    }

    // Create a new invoice
    Invoice* createInvoice(string customerName) {
        Invoice* invoice = new Invoice(nextInvoiceId++, customerName);
        invoices.push_back(*invoice);
        return invoice;
    }

    // Main billing system menu
    void startBilling() {
        cout << "Welcome to the Billing System!" << endl;

        while (true) {
            cout << "\nMenu:" << endl;
            cout << "1. Add Product" << endl;
            cout << "2. Create Invoice" << endl;
            cout << "3. Display Invoice" << endl;
            cout << "4. Exit" << endl;
            cout << "Enter your choice: ";
            int choice;
            cin >> choice;

            switch (choice) {
                case 1: {
                    string name;
                    double price;
                    cout << "Enter product name: ";
                    cin.ignore(); // Clear the input buffer
                    getline(cin, name);
                    cout << "Enter product price: ";
                    cin >> price;
                    addProduct(name, price);
                    break;
                }
                case 2: {
                    string customerName;
                    cout << "Enter customer name: ";
                    cin.ignore(); 
                    getline(cin, customerName);
                    Invoice* invoice = createInvoice(customerName);
                    cout << "Invoice created successfully!" << endl;

                    while (true) {

                        cout << "\nInvoice Items:" << endl;
                        cout << "1. Add Item" << endl;
                        cout << "2. Finish Invoice" << endl;
                        cout << "Enter your choice: ";
                        int itemChoice;
                        cin >> itemChoice;

                        if (itemChoice == 1) {
                            int productId;
                            int quantity;
                            cout << "Enter product ID: ";
                            cin >> productId;
                            cout << "Enter quantity: ";
                            cin >> quantity;
                            Product* product = getProduct(productId);
                            if (product != nullptr) {
                                invoice->addItem(product, quantity);
                                cout << "Item added successfully!" << endl;
                            } else {
                                cout << "Invalid product ID." << endl;
                            }
                        } else if (itemChoice == 2) {
                            invoice->displayInvoice();
                            break;
                        } else {
                            cout << "Invalid choice." << endl;
                        }
                    }
                    break;
                }
                case 3: {
                    int invoiceId;
                    cout << "Enter invoice ID: ";
                    cin >> invoiceId;
