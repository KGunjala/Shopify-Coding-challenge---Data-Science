# Shopify-Coding-challenge : Data-Science
 
  
***
# PART - A
***

## Check the .ipynb file included in this repository for the methods used to arrive at our results
    
    
### Data obtained from the following google sheet :
https://docs.google.com/spreadsheets/d/16i38oonuX1y1g7C_UAmiK9GkY7cS-64DfiDMNiR41LM/edit#gid=0
 
 
 
### Background : 
On Shopify, we have exactly 100 sneaker shops, and each of these shops sells only one model of shoe. We want to do some analysis of the average order value (AOV). When we look at orders data over a 30 day window, we naively calculate an AOV of $3145.13. Given that we know these shops are selling sneakers, a relatively affordable item, something seems wrong with our analysis. 
 
 
### Question 1 :
What could be going wrong with this calculation. Think about a better way to evaluate this data

### Answer:
The data consists of outliers, as is evident from the unnaturally large difference between the 3rd quartile (390.00) and the max value (704000).
As we know, mean is highly sensitive to the presence of outliers. In our distribution, **the presence of such large outliers is disproportionately inflating the mean.**
 
This is the reason why the Average Order Value is highly inflated and doesn't make much sense.

***
- How to deal with these outliers then?
***
 
Outliers usually indicate some anomaly in the data collection process. However, it is also possible for these values to occur naturally. Deleting these values from the data might result in some interesting patterns being lost.
**In our case, most of the outliers originate from the stores bearing the shop ids 78 and 42.**
**The customer bearing the user id of 607 has also indulged in very peculiar transactions (all of the max order amounts originate from this user).**
**These behaviours are worth looking into. Hence, the outliers were persisted with.**
 
***
- How to overcome this problem then?
***
 
We can use the other measures of central tendency - **Mode and Median**. These measures see little to no change due to the presence of outliers.
 
 
  
   
### Question 2 :
What metric should we choose for this data?
 
### Answer :
We should proceed with the **Mode** of the Order Amount. The Mode represents the most frequent value in our distribution.
In terms of the problem statement, **a rather basic inference would be to assume that the mode denotes the revenue that is most likely to be generated from a given order.**
Focusing on this amount and trying to get customers to order more than this amount would be the first step in increasing the revenue generated.
 
The **mode** for this distribution is **153**.
The **median** for this distribution is **284**.
 
  
Our choice for using the mode is justified by the fact that the most number of observations have an order amount ranging between 100$ and 200$ (1765), which is where our mode lies.
Whereas, the interval 200$ to 300$ for order amount has 850 observations, which is where our median lies. The median is simply the value that divides our distribution in such a manner that equal number of observations lie above and below this value. In our case, **the mode is more helpful in analysing the order amount generated from customers**.
 
  
   
### Question 3 :
What is the value of the chosen metric?
 
### Answer :
The Value of mode, which is the chosen metric is **153$**. It would be ideal to come up with a plan to get customers to spend more than this value for each order they place in order to increase revenue.
 
  
   
***
# PART B
***

 
  
### Question 1:
How many orders were shipped by Speedy Express in total?
 
### Answer:
SELECT COUNT( * ) FROM Orders

***
Output:
**196**
***

 
 
### Question 2:
b.	What is the last name of the employee with the most orders?
 
### Answer:
SELECT E.EmployeeID AS EmployeeID, COUNT( * ) AS TotalOrders, E.LastName AS Employee_LastName 
FROM Orders O INNER JOIN Employees E
ON O.EmployeeID = E.EmployeeID
GROUP BY O.EmployeeID 
HAVING COUNT( * ) = ( SELECT MAX(TotalOrders) FROM ( 
						 SELECT EmployeeID, COUNT( * ) TotalOrders 
                                                 FROM Orders 
                                                 GROUP BY EmployeeID
                                                 ));

***
Output:
**Peacock**
***

 
  
### Question 3:
c.	What product was ordered the most by customers in Germany?
 
### Answer :
SELECT Od.ProductID ProductID, P.ProductName ProductName, COUNT( * ) NumOfOrders
FROM Customers C INNER JOIN Orders O ON C.CustomerID = O.CustomerID
                 INNER JOIN Orderdetails Od ON Od.OrderID = O.OrderID
                 INNER JOIN Products P ON Od.ProductID = P.ProductID
WHERE C.Country = 'Germany'
GROUP BY Od.ProductID, P.ProductName
HAVING COUNT( * ) = (SELECT MAX(tcount) FROM (
                          SELECT Od1.ProductID, COUNT( * ) tcount FROM
                          Customers C1 INNER JOIN Orders O1 ON C1.CustomerID = O1.CustomerID
                          INNER JOIN Orderdetails Od1 ON Od1.OrderID = O1.OrderID
                          INNER JOIN Products P1 ON Od1.ProductID = P1.ProductID
                          WHERE C1.Country = 'Germany'
                          GROUP BY Od1.ProductID
                        ))
***
Output:
**Gorgonzola Telino**
***

