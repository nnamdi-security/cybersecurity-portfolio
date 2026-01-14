# Applying filters to SQL queries

## Project description

My organization is working to make their system more secure. It is my job to ensure the system is safe, investigate all potential security issues, and update employee computers as needed. The following steps provide examples of how I used SQL with filters to perform security-related tasks.

## Steps Performed

1. Retrieve after hours failed login attempts

There was a potential security incident that occurred after business hours (after 6pm). All after hours login attempts that failed need to be investigated.

The following code demonstrates how I created a SQL query to filter for failed login attempts that occurred after business hours.

### Screenshot
![Failed Login](../screenshots/failed-login.png)

The first part of the screenshot is my query, and the second part is a portion of the output. This query filters for failed login attempts that occurred after 18:00. First, I started by selecting all data from the "log_in_attempts" table. Then, I used a **WHERE** clause with an **AND** operator to filter my results to output only login attempts that occurred after 18:00 and were unsuccessful. 

The first condition is "login_time > '18:00'", which filters for the login attempts that occurred after 18:00. The second condition is "success = 0", which filters for the failed login attempts. 

2. Retrieve login attempts on specific dates

To retrieve all login attempts that occurred on 2022-05-09 and the day before- 2022-05-08, I used the highlighted command in the screenshot

### Screenshot
![Login Attempt](../screenshots/login-attempt.png)
The output shows that there were 75 login attempts on these days

3. Retrieve login attempts outside of Mexico

Now, my team is investigating logins that did not originate in Mexico, so I used the below command in the screenshot

### Screenshot
![Outside Mexico](../screenshots/mexico.png)
The output showed that 144 login attempts originate from outside.

4. Retrieve employees in Marketing

My team is now updating employee machines, and I need to obtain the information about employees in the 'Marketing' department who are located in all offices in the East building (such as East-170 or East-320)

### Screenshot
![Marketing](../screenshots/marketing.png)
The result shows there are 7 employees in the marketing department in the East building

5. Retrieve employees in Finance or Sales

Now, my team needs to perform a different update to the computers of all employees in the Finance or the Sales department, and I need to locate information on these employees so I use the following commands

### Screenshot
![Finance or Sales](../screenshots/finance-sales.png)

6. Retrieve all employees not in IT

My team needs to make one more update. This update was already made to employee computers in the Information Technology department. The team needs information about employees who are not in that department.

### Screenshot
![IT](../screenshots/it.png)

## Summary
I applied filters to SQL queries to get specific information on login attempts and employee machines. I used two different tables, log_in_attempts and employees. I used the AND, OR, and NOT operators to filter for the specific information needed for each task. I also used LIKE and the percentage sign (%) wildcard to filter for patterns.
