In this investigation, I analyzed a potentially malicious browser extension to understand its behavior, identify its data collection techniques, and investigate how it handles and transmits sensitive user information.

The analysis was performed using **Radare2 (r2)** for static analysis.
## **Analysis and Investigation**

### **Task 1: Identify the encoding method used to obscure target URLs**

**Question:** Which encoding method does the browser extension use to obscure target URLs, making them more difficult to detect during analysis?

**Methodology:**

I started by opening the extension's `app.js` file in Radare2 and inspecting the available strings using the `izz` command.

While reviewing the output, I noticed a suspicious encoded string stored in a variable within the JavaScript code. I copied the encoded value and decoded it to understand what information it contained. I got the answer for the second task.

The encoding method that was used can be seen in the code and I was able to understand it by looking at the encoded string which ended with == . 

![[Pasted image 20261108114411.png]]

### **Task 2: Identify the website targeted by the extension**

**Question:** Which website does the extension monitor for data theft, targeting user accounts to steal sensitive information?

**Methodology:**

After identifying the encoded string in the previous step, I decoded it to reveal the website being targeted by the extension.

![[Pasted image 20261108113615.png]]


### **Task 3: Identify the HTML element used for data exfiltration**

**Question:** Which type of HTML element is utilized by the extension to send stolen data?

**Methodology:**

During the analysis, I found code that creates a new HTML element dynamically, assigns a URL containing encoded data to one of its properties, and then appends that element to the document body.

By examining the element creation and the property assignment, I could determine which HTML element the extension uses to transmit the information.

![[Cybersecurity/CTF/Writeups/CyberDefenders/FakeGPT/Pasted image 20261108114050.png]]

### **Task 4: Identify the first condition that causes the extension to deactivate**

**Question:** What is the first specific condition in the code that triggers the extension to deactivate itself?

**Methodology:**

I inspected the beginning of the JavaScript code for `loader.js` and looked for conditional statements that control whether the extension continues executing.

I focused on the environment-checking logic, particularly the conditions used to detect browser configurations that might indicate an automated or analysis environment.

The code contains multiple checks combined in a conditional statement.

![[Pasted image 20261108121020.png]]

### **Task 5: Identify the event used to monitor form submissions**

**Question:** Which event does the extension capture to track user input submitted through forms?

**Methodology:**

I searched through the JavaScript code for event listeners attached to the document.

I paid particular attention to the listener associated with form submission. By examining the callback function, I could understand how the extension accesses the submitted form and extracts its contents.

The code uses the submitted form data to retrieve information from specific input fields.

![[Pasted image 20261108115736.png]]

### **Task 6: Identify the API or method used to monitor keystrokes**

**Question:** Which API or method does the extension use to capture and monitor user keystrokes?

**Methodology:**

Next, I examined another event listener within the JavaScript code that monitors keyboard activity.

I looked for the event registration associated with keyboard interactions and inspected the callback function to understand how individual key presses are processed.

The callback accesses information from the keyboard event and passes the collected information to the extension's credential-exfiltration functionality.

![[Pasted image 20261108121820.png]]

### **Task 7: Identify the domain used to transmit stolen information**

**Question:** What is the domain where the extension transmits the exfiltrated data?

**Methodology:**

I returned to the data transmission functionality and inspected the URL constructed by the extension.

I focused on the destination used when sending the encrypted information. By examining the URL assignment and the surrounding code, I was able to identify the external server responsible for receiving the collected data.

This step helped establish where the extension sends the information after collecting and processing it.

![[Pasted image 20261108120121.png]]

### **Task 8: Identify the function responsible for credential exfiltration**

**Question:** Which function in the code is used to exfiltrate user credentials, including the username and password?

**Methodology:**

I inspected the JavaScript functions responsible for handling the credentials collected from the browser.

I looked for a function that accepts the username and password as parameters and examined how those values are combined into a data structure.

I then followed the execution flow to understand how the credentials are passed to the encryption functionality and eventually transmitted to the external server.

![[Pasted image 20261108120729.png]]

### **Task 9: Identify the encryption algorithm used before transmission**

**Question:** Which encryption algorithm is applied to secure the data before sending?

**Methodology:**

I examined the function responsible for encrypting the collected information before transmission.

I focused on the cryptographic library calls and inspected how the encryption key, initialization vector, and encryption operation were handled.

By identifying the cryptographic method referenced in the code, I could determine which algorithm the extension uses to encrypt the collected data.

![[Pasted image 20261108120511.png]]

### **Task 10: Identify how the extension accesses session-related information**

**Question:** What does the extension access to store or manipulate session-related data and authentication information?

**Methodology:**

Initially I considered the browser mechanisms commonly associated with storing session-related information and authentication data and submitted what I believed was the most likely answer (and I was right).

The answer can be seen inside `manifest.json` in permissions.

## **Conclusion**

Through static analysis of the browser extension's `app.js` file using Radare2, I was able to investigate its behavior and trace the main stages of its operation.

The analysis involved inspecting strings, identifying encoded data, examining conditional statements, reviewing event listeners, and following the execution flow of the credential collection and transmission functions.

By combining string analysis with manual inspection of the JavaScript code, I was able to investigate how the extension identifies targeted websites, monitors user interactions and form submissions, collects sensitive credential information, processes and encrypts collected data, transmits information to an external server, checks the browser environment before executing its functionality.

This investigation demonstrated how static analysis and reverse-engineering techniques can be used to understand suspicious browser extension behavior without executing the potentially malicious code.

