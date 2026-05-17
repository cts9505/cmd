# CC & IS Assignments Guide

This document contains all the requested assignments with explicit step-by-step navigation, showing exactly where to write the code and how to execute/save it.

---

## 1. CC: Student Record Management System (Visualforce & Apex)

### Step 1: Create the Custom Object
1. **Navigate to:** Setup ➔ Object Manager ➔ Click **Create** (Top Right) ➔ **Custom Object**.
2. **Label:** `Student`, **Plural Label:** `Students`. Click **Save**.
3. **Add Fields:** Go to **Fields & Relationships** ➔ Click **New**.
   - Create `Roll_No__c` (Number)
   - Create `Class__c` (Text)
   - Create `Mobile_No__c` (Phone)

### Step 2: Write the Apex Controller
1. **Navigate to:** Click the Gear Icon ⚙️ (Top Right) ➔ **Developer Console**.
2. **Create File:** File ➔ New ➔ **Apex Class**.
3. **Name it:** `StudentController` and click **OK**.
4. **Write Code:**
```java
public with sharing class StudentController {
    public Student__c student { get; set; }
    public StudentController() { 
        student = new Student__c(); 
    }

    public PageReference saveStudent() {
        upsert student;
        ApexPages.addMessage(new ApexPages.Message(ApexPages.Severity.CONFIRM, 'Record Saved Successfully'));
        return null; // Stays on the same page
    }

    public List<Student__c> getAllStudents() {
        return [SELECT Id, Name, Roll_No__c, Class__c, Mobile_No__c FROM Student__c ORDER BY Roll_No__c ASC];
    }
}
```
5. **Execute/Save:** Press **Ctrl + S** (Windows) or **Cmd + S** (Mac) to save.

### Step 3: Write the Visualforce Page
1. **Navigate to:** Developer Console ➔ File ➔ New ➔ **Visualforce Page**.
2. **Name it:** `StudentPage` and click **OK**.
3. **Write Code:**
```html
<apex:page controller="StudentController">
    <apex:form>
        <apex:pageMessages />
        <apex:pageBlock title="Add New Student">
            <apex:pageBlockSection>
                <apex:inputField value="{!student.Name}"/>
                <apex:inputField value="{!student.Roll_No__c}"/>
                <apex:inputField value="{!student.Class__c}"/>
                <apex:inputField value="{!student.Mobile_No__c}"/>
            </apex:pageBlockSection>
            <apex:pageBlockButtons>
                <!-- This triggers the saveStudent method in Apex -->
                <apex:commandButton value="Save Data" action="{!saveStudent}"/>
            </apex:pageBlockButtons>
        </apex:pageBlock>
        
        <apex:pageBlock title="All Students">
            <apex:pageBlockTable value="{!allStudents}" var="s">
                <apex:column value="{!s.Roll_No__c}"/>
                <apex:column value="{!s.Name}"/>
                <apex:column value="{!s.Class__c}"/>
                <apex:column value="{!s.Mobile_No__c}"/>
            </apex:pageBlockTable>
        </apex:pageBlock>
    </apex:form>
</apex:page>
```
4. **Execute/Save:** Press **Ctrl + S**.
5. **Preview:** Click the **Preview** button at the top left of the Developer Console.

---

## 2. IS: MD5 Proof of Work (Bitcoin Mining in Java)

### Step 1: Write the Code
1. **Navigate to:** Open VS Code or any Java IDE (Eclipse/IntelliJ).
2. **Create File:** Create a new file named `MD5Miner.java`.
3. **Write Code:**
```java
import java.security.MessageDigest;

public class MD5Miner {
    public static String getMD5Hash(String input) throws Exception {
        MessageDigest md = MessageDigest.getInstance("MD5");
        byte[] digest = md.digest(input.getBytes());
        StringBuilder sb = new StringBuilder();
        for (byte b : digest) {
            sb.append(String.format("%02x", b));
        }
        return sb.toString();
    }

    public static void main(String[] args) throws Exception {
        String data = "HelloWorldBlock"; // Block data
        int nonce = 0;
        String difficultyTarget = "0000"; // PoW target (must start with 4 zeros)

        System.out.println("Mining starting...");
        while (true) {
            String hash = getMD5Hash(data + nonce);
            if (hash.startsWith(difficultyTarget)) {
                System.out.println("Success! Block mined.");
                System.out.println("Nonce: " + nonce);
                System.out.println("Hash: " + hash);
                break;
            }
            nonce++;
        }
    }
}
```

### Step 2: Execute
1. Open your terminal in the folder containing the file.
2. Compile: `javac MD5Miner.java`
3. Run: `java MD5Miner`

---

## 3. CC: Console-Based Employee Management (Apex)

### Step 1: Create the Standard/Custom Object
1. Setup ➔ Object Manager ➔ Create `Employee__c` object.
2. Add fields: `Emp_ID__c` (Number), `Email__c` (Email), `Birthdate__c` (Date), `Department__c` (Picklist).

### Step 2: Write the Apex Code
1. **Navigate to:** Developer Console ➔ File ➔ New ➔ **Apex Class**.
2. **Name it:** `EmployeeManager`.
3. **Write Code:**
```java
public class EmployeeManager {
    public static void insertEmployee(String name, Decimal empId, String email, Date dob, String dept) {
        Employee__c emp = new Employee__c();
        emp.Name = name;
        emp.Emp_ID__c = empId;
        emp.Email__c = email;
        emp.Birthdate__c = dob;
        emp.Department__c = dept;
        
        insert emp;
        System.debug('Employee Inserted: ' + emp.Id);
    }
    
    public static void listEmployees() {
        List<Employee__c> emps = [SELECT Name, Emp_ID__c, Department__c FROM Employee__c];
        for(Employee__c e : emps) {
            System.debug('| ID: ' + e.Emp_ID__c + ' | Name: ' + e.Name + ' | Dept: ' + e.Department__c);
        }
    }
}
```
4. **Save:** **Ctrl + S**.

### Step 3: Execute (Console Based)
1. **Navigate to:** Developer Console ➔ **Debug** (Top Menu) ➔ **Open Execute Anonymous Window** (Ctrl + E).
2. **Write execution code:**
```java
// Create a record
EmployeeManager.insertEmployee('John Doe', 101, 'john@example.com', Date.newInstance(1990, 5, 20), 'IT');
// View records
EmployeeManager.listEmployees();
```
3. **Execute:** Check the "Open Log" box at the bottom and click the **Execute** button.
4. **View Output:** In the log that opens, check the **Debug Only** box in the bottom filter to see the results.

---

## 4. IS: XOR Encryption (C++)

### Step 1: Write Code
1. Open VS Code or any text editor.
2. Create `xor_encrypt.cpp`.
3. **Write Code:**
```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string plaintext;
    int modFactor; // key

    cout << "Enter plaintext string: ";
    getline(cin, plaintext);
    cout << "Enter mod factor (key integer): ";
    cin >> modFactor;

    string ciphertext = plaintext;
    // Encryption
    for (int i = 0; i < ciphertext.length(); i++) {
        ciphertext[i] = ciphertext[i] ^ modFactor; // Bitwise XOR
    }
    
    cout << "Ciphertext: " << ciphertext << endl;
    return 0;
}
```
### Step 2: Execute
1. Compile: `g++ xor_encrypt.cpp -o xor_encrypt`
2. Run: `./xor_encrypt`

---

## 5. CC: Bank Account System (Apex + Console)

### Step 1: Write Apex Logic
1. Developer Console ➔ File ➔ New ➔ Apex Class ➔ `BankAccountManager`.
2. **Write Code:**
```java
public class BankAccountManager {
    // Assuming Custom Object Customer__c exists with fields.
    public static void createCustomer(String name, String email) {
        Customer__c cust = new Customer__c(Name = name, Email__c = email);
        insert cust;
        System.debug('Customer Created with ID: ' + cust.Id);
    }
}
```
3. **Save:** **Ctrl + S**.
4. **Execute:** Go to Debug ➔ Open Execute Anonymous Window ➔ `BankAccountManager.createCustomer('Alice', 'alice@bank.com');` ➔ Click Execute.

---

## 6. IS: Columnar & Rail Fence (Java)

1. Create `Ciphers.java`.
2. **Write Code:**
```java
public class Ciphers {
    public static String railFenceEncrypt(String text, int rails) {
        StringBuilder[] r = new StringBuilder[rails];
        for (int i = 0; i < rails; i++) r[i] = new StringBuilder();
        
        int row = 0, dir = 1;
        for (char c : text.toCharArray()) {
            r[row].append(c);
            if (row == 0) dir = 1;
            else if (row == rails - 1) dir = -1;
            row += dir;
        }
        
        StringBuilder out = new StringBuilder();
        for (StringBuilder sb : r) out.append(sb);
        return out.toString();
    }

    public static void main(String[] args) {
        System.out.println("Rail Fence Cipher: " + railFenceEncrypt("HELLO WORLD", 3));
    }
}
```
3. **Execute:** `javac Ciphers.java` then `java Ciphers`.

---

## 7. CC: Employee Management LWC Forms & Validations

### Step 1: Setup LWC in VS Code
1. Open your Salesforce project in VS Code (Ensure SFDX is configured).
2. Press **Ctrl+Shift+P** (Cmd+Shift+P on Mac) ➔ **SFDX: Create Lightning Web Component**.
3. Name it: `employeeForm`.

### Step 2: Write Code
Open `employeeForm.html`:
```html
<template>
    <lightning-card title="Add Employee">
        <div class="slds-p-around_medium">
            <lightning-input label="Employee Name" value={name} onchange={handleName}></lightning-input>
            <lightning-input type="number" label="Employee ID" value={empId} onchange={handleId}></lightning-input>
            <lightning-input type="number" label="Salary" value={salary} onchange={handleSalary}></lightning-input>
            <lightning-input type="email" label="Email" value={email} onchange={handleEmail}></lightning-input>
            <lightning-input type="date" label="Joining Date" value={joinDate} onchange={handleJoinDate}></lightning-input>
            
            <br/>
            <lightning-button label="Save Employee" variant="brand" onclick={handleSave}></lightning-button>
        </div>
    </lightning-card>
</template>
```

Open `employeeForm.js`:
```js
import { LightningElement, track } from 'lwc';
import { ShowToastEvent } from 'lightning/platformShowToastEvent';

export default class EmployeeForm extends LightningElement {
    name = ''; empId; salary; email; joinDate;

    handleName(e) { this.name = e.target.value; }
    handleId(e) { this.empId = parseFloat(e.target.value); }
    handleSalary(e) { this.salary = parseFloat(e.target.value); }
    handleEmail(e) { this.email = e.target.value; }
    handleJoinDate(e) { this.joinDate = e.target.value; }

    handleSave() {
        // Validations
        if(this.name.length < 3) return this.showError('Name must be at least 3 characters.');
        if(!this.empId || this.empId <= 0) return this.showError('ID must be > 0.');
        if(this.salary <= 10000 || this.salary >= 500000) return this.showError('Salary must be between 10k and 500k.');
        
        let selectedDate = new Date(this.joinDate);
        if(selectedDate > new Date()) return this.showError('Joining Date cannot be in the future.');

        // Normally you call Apex here.
        this.showSuccess('Validation Passed! Ready to Save to DB.');
    }

    showError(msg) {
        this.dispatchEvent(new ShowToastEvent({ title: 'Validation Error', message: msg, variant: 'error' }));
    }
    showSuccess(msg) {
        this.dispatchEvent(new ShowToastEvent({ title: 'Success', message: msg, variant: 'success' }));
    }
}
```

Open `employeeForm.js-meta.xml`:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<LightningComponentBundle xmlns="http://soap.sforce.com/2006/04/metadata">
    <apiVersion>58.0</apiVersion>
    <isExposed>true</isExposed>
    <targets>
        <target>lightning__AppPage</target>
        <target>lightning__RecordPage</target>
        <target>lightning__HomePage</target>
    </targets>
</LightningComponentBundle>
```

### Step 3: Execute (Deploy to Org)
1. Right-click the `employeeForm` folder in VS Code ➔ **SFDX: Deploy Source to Org**.
2. **Navigate to:** Salesforce Salesforce UI ➔ Setup ➔ **Lightning App Builder**.
3. Create a new "App Page", drag the `employeeForm` component onto the page, save, and activate it.

---

## 8. CC: Send Email Notification via Apex

### Step 1: Write Apex Class
1. Developer Console ➔ File ➔ New ➔ Apex Class ➔ `EmailNotifier`.
2. **Write Code:**
```java
public class EmailNotifier {
    public static void sendSimpleEmail(String recipient, String subject, String body) {
        // Check if email is valid simple regex style
        if(recipient == null || !recipient.contains('@') || !recipient.contains('.')){
            System.debug('Invalid Email Address');
            return;
        }

        Messaging.SingleEmailMessage mail = new Messaging.SingleEmailMessage();
        mail.setToAddresses(new String[] { recipient });
        mail.setSubject(subject);
        mail.setPlainTextBody(body);
        
        // Send email
        Messaging.SendEmailResult[] results = Messaging.sendEmail(new Messaging.SingleEmailMessage[] { mail });
        
        if (results[0].isSuccess()) {
            System.debug('Email sent successfully!');
        } else {
            System.debug('Email failed: ' + results[0].getErrors()[0].getMessage());
        }
    }
}
```
3. **Save:** **Ctrl + S**.

### Step 2: Execute
1. Developer Console ➔ Debug ➔ Open Execute Anonymous Window.
2. **Run:**
```java
EmailNotifier.sendSimpleEmail('user@test.com', 'Test Notification', 'Hello from Salesforce!');
```
3. Click **Execute**. *(Note: Make sure Setup ➔ Deliverability is set to "All Email" in your org)*.
