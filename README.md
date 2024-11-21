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

![image](https://github.com/user-attachments/assets/a106bf7b-8775-4fb1-a4d4-0f9b268f947f)

Something to note is in the URL, it calls an sql.php script with parameters for the database, table, and position in the table.

![image](https://github.com/user-attachments/assets/a666ae97-34ed-4e1e-8b6f-98a268e8e206)
#
<h3>Assessing Website Functionality</h3>

Before testing the site it's important to know website operates

This is the store's website, which runs on the Apache server that I started earlier.
![image](https://github.com/user-attachments/assets/a384d4d9-268d-4815-a163-42022ea53dbe)

Clicking on the catalog button, I can see the separate categories and the products that correspond to them. In this case, monitors and the monitor for sale. 
![image](https://github.com/user-attachments/assets/ece3ef09-964c-4292-8e8a-db570022f247)
- In the URL, I see that it contains 'catagory=1', and hovering over the 'smart TV's' button shows me that the url for it contains 'category=2'. This is a Direct Object Reference, and it essentially gives a user access to the query made to the database from the url.
- The intended behavior of the website is to list only one product category at a time, depending on which category the user wants to see.
- The website works by making a query in the database and then outputing the answer. If the website doesn't pass SQL statements securely, the user can inject additional parameters.
#
<h3>Testing Injection Vulnerabilities</h3>

Since the query is visible in url and can be manipulated, I'll try to dump all the contents of the database with a simple injection technique: OR 1 = 1

After modifying the  query in the URL, the website will ask the database to return 'category=2 OR 1=1', and now instead of just monitors, the page displays information for smart TVs, laptops, and all the other product categories.
![image](https://github.com/user-attachments/assets/f36ca156-cdeb-4aeb-a69f-876d96d1e77d)
- Essentially the query returns the four columns from each product in the products table, where the product category is n or where 1 equals 1. Since 1 = 1 is always true it will output all the products, regardless of the category.

<h3>Solution to Direct Object Reference</h3>
A Direct Object Reference is possible when a user can manipulate the url to specify an object. In this case, the url only allows us to modify the category number but I was able to input my own SQL statement to manipulate the query and have the website behave incorrectly. 
- In this case, I wouldn't consider this an Insecure Direct Object Reference (IDOR), since I wasn't able to access another user's account for example. But it allowed me to get the website to behave incorrectly which is just the tip of the iceberg.

Mitigation techniques for Direct Object References and IDOR include:
- Access control checks and overall better coding practices
#
<h3>Testing Authentication Vulnerabilities</h3>

Now I will try to log onto the site, without knowing the password to a user account

First I'll try to log in as 'kevin' and use an incorrect password.
The website says I used an invalid username or password. 

![image](https://github.com/user-attachments/assets/886c9da2-791f-4a08-9249-7a3bb62c92d0)
- This is actually good because if I were an attacker going at this blind, and it only said 'incorrect password; then I would know that the username 'kevin' exists and I would only need to guess the password.

More importantly, this website allows me to see the query it made to the database, while this is not realistic, every website queries a database, so the following attack can still occur.

![image](https://github.com/user-attachments/assets/51019b82-184e-4601-bf5f-a9eb62f9905c)

Now I will try to manipulate the query, to sign in successfully. I will sign in as 'kevin' but in the password field I will input: x' or 'x'='x

![image](https://github.com/user-attachments/assets/9e8d031c-9322-4435-8041-6b3ebbc9dedd)
- To show the input I made the password field visible, by going into the inspect element and changing the HTML element type from password to text.

By manipulating the SQL query, I was able to successfully login as 'kevin', without Kevin's actual password
![image](https://github.com/user-attachments/assets/44e7e7dc-5208-4d4c-9887-1a67d520a3fb)

Looking at the query made to the database we can see how this works. By using apostrophes, I'm able to manipulate the query. This is because the query will be run with its own opening and closing apostrophes, as seen in the first attempt, so I need to ensure that the entire statement isn’t enclosed in one long string. 
- Essentially, I can input apostrophes to open and close my own SQL statement.

![image](https://github.com/user-attachments/assets/768f83d4-e2af-4d63-afa8-00135829d32b)
- This query is similar to the query that I previously used to dump all the contents of the database. 

This query exploits an always true condition, of 'x'='x'. So I told the database to log in as 'kevin', with the password 'x' or 'x'='x', which is always true, which means the password is a true statement.
- Since 'password is a true statement' applies to all users it actually logs in as the first user in the table, which is Laura Anderson, who also has Admin privileges.


Kevin's actual password is !Pass1234

![image](https://github.com/user-attachments/assets/51139e0e-21e8-43cf-b3e8-a644f25fdb6e)

<h3>Solution to SQL Injection</h3>
SQL Injection allows an attacker to manipulate the query sent to the database, in this case, it was able to give me access to an account with admin privileges.

There are many ways to defend against SQL Injection, including: 
- Input validation: Ensures that user input is syntactically and semantically correct.
- Input Sanitization: The process of cleaning or transforming user input to eliminate or neutralize potentially harmful data.
- Parameterized Queries: SQL queries that use placeholders for user input instead of directly embedding the input into the query, thus separating the SQL query structure from the data elements.

In this case, the query takes advantage of the default apostrophe formatting and lack of input sanitization.
#
**Summary: In this lab, I was able to test website vulnerabilities and see the importance of secure coding practices. As well as, how the structure of the underlying database relates to the way I'm able to exploit the vulnerabilities.**
