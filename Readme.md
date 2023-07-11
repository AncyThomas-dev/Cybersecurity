#Ancy Thomas - Willdan Interview

##Hello, and thank you for taking the time to interview me today!
![WilldanLogo.png](https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Images/WilldanLogo.png)








###You can view a copy of my resume [HERE](https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Ancy_Thomas_Resume.pdf)

##Project 1
###Database Management

In this bootcamp project, I deployed a container set and managed the database. 

---

1. First I deployed the container set using Docker Compose:

    - `cd /home/sysadmin/Cybersecurity-Lesson-Plans/14-Web_Dev/deploying_databases` directory. 

    - Ran `docker-compose up`.

    
2. Found the MySQL credentials in the `/home/sysadmin/Cybersecurity-Lesson-Plans/14-Web_Dev/deploying_databases` compose file.

    - I used the credentials in the `docker-compose.yml` file:

      ```YAML
      MYSQL_USER: admin
      MYSQL_PASSWORD: 123456
      ```

3. Entered an interactive bash session in the database's container. The container name was also be found in the compose file:

    - Ran `docker exec -it activitydb bash` to enter an interactive bash session in the MySQL container.

4. Within the interactive bash session, used the credentials found earlier to enter a MySQL session using the ` goodcorp` MySQL database.

    - Entered `mysql -u admin -p123456 -D ` goodcorp ` to enter a MySQL session.

5. Created a basic `SELECT` query to find all of the employee table entries in the ` goodcorp ` database.

    - While in the MySQL session, entered `SELECT * FROM employees;` to retrieve all of the entries in the employees table.

6. Using the given information, added the following new user to the employee directory:

    - **First name**: Fran
    - **Last name**: Frappucino
    - **Email Address**: ffrappucino@goodcorp.net
    - **Department**: Finance

     Entered the following query to add the new Minion employee:

   ```SQL
    INSERT INTO employees (firstname, lastname, email, department)  
    VALUES ('Fran', 'Frappucino', 'ffrappucino@goodcorp.net', 'Finance');
    ```
![InsertQuery.png] (https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Images/SQL/InsertQuery.png)
7. Created a modified `SELECT` query to find all employees in the Research and Development department.

      `SELECT * FROM employees WHERE Department = 'Research and Development';`
![SelectQuery.png] (https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Images/SQL/SelectQuery.png)

8. Created a `DELETE` query to remove the entry for Andrew:

    - Run the following query to remove Andrew via his `Id`:

       `DELETE FROM employees where Id='1';`

    - Then re-run the `SELECT *` query to see the new `employees` table:

1       `SELECT * FROM employees;`
![DeleteQuery.png] (https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Images/SQL/DeleteQuery.png)
   

      




