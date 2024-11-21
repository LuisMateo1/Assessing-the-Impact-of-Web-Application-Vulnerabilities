# Assessing the Impact of Web Application Vulnerabilities
<h3>Objectives</h3>

- Implement controls to mitigate attacks and software vulnerabilities
- Explain software assurance best practices
#
<h3>Scenario</h3>
Develetech’s storefront website was unfortunately published in a hurry, and not much attention was paid to securing the site. 
You’re especially concerned that the site is vulnerable to injection attacks on its SQL database. 
An attacker may be able to hijack an account in the database to deface the site or tamper with the product data.

**The goal is to test the website’s vulnerabilities to SQL injection to assess how web-based threats can compromise your organization’s security.**
#
<h3>Lab Setup</h3>
For this lab, I just need to start up the MySQL server for the database, and the Apache server for the website

![image](https://github.com/user-attachments/assets/1af76571-94ce-4bb6-9c05-545241a3505c)
#
<h3>Assing Database Functionallity</h3>
Before testing the code it's important to know the basic structure of the database.

For this lab, I'll be using the phpMyAdmin graphical frontend to browse the database.

![image](https://github.com/user-attachments/assets/deb6d945-0506-48b1-a8fc-60994143da44)
![image](https://github.com/user-attachments/assets/e496834c-e64b-4931-b684-1a9aaa0e34d0)

Clicking the devtech_store database shows the three tables in the database.
![image](https://github.com/user-attachments/assets/deb3d796-cf4a-4c50-8217-8a3e6a4da1d4)

Clicking on a table shows its contents:

The categories table lists product categories

![image](https://github.com/user-attachments/assets/6cea4068-07a3-4aa4-8287-0063bcdd80f1)

The products table lists all the products with their product codes, descriptions, prices, availability, and corresponding categories from the categories table.
![image](https://github.com/user-attachments/assets/4103d02f-23b6-4287-9510-b911ff0870c0)

The user's table lists the users who can log onto the website. Each user has a username, password, first name, last name, and permission.

![image](https://github.com/user-attachments/assets/00a1b275-8aa2-44b5-bceb-5501466e2646)

The corresponding SQL statements are shown at the top, to make another query click 'edit'
- The query returns all users with admin permissions, in this case, it's just 'Laura Anderson'
- It's important these 
![image](https://github.com/user-attachments/assets/a106bf7b-8775-4fb1-a4d4-0f9b268f947f)

Something to note is that in the URL, it calls a sql.php script with parameters for the database, table, and position in the table. That is a direct object reference vulnerability, 
if an attacker were to gain access to the database over the internet they could input queries via the URL. 

![image](https://github.com/user-attachments/assets/a666ae97-34ed-4e1e-8b6f-98a268e8e206)
#
<h3>Assessing Website Functionality</h3>

Before testing the site it's important to know website operates

This is the store's website, which runs on the Apache server that I started earlier.
![image](https://github.com/user-attachments/assets/a384d4d9-268d-4815-a163-42022ea53dbe)

Clicking on the catalog button, I can see the separate categories and the products that correspond to them. In this case, monitors and the monitor for sale. 
![image](https://github.com/user-attachments/assets/ece3ef09-964c-4292-8e8a-db570022f247)
- In the URL, I see that it contains 'catagory=1', and hovering over the 'smart TV's' button shows me that the url for it contains 'category=2'. Again, this is a direct object reference,
and it essentially gives a user direct access to the database from the website.
- The intended behavior of the website is to list only one product category at a time, depending on which category the user wants to see.
- The website works by making a query in the database and then outputing the answer. If the website doesn't pass SQL statements securely, the user can inject additional parameters.
#
<h3>Testing Injection Vulnerabilities</h3>

