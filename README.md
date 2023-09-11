# Process Mining: SAP Order to cash process

### To be updated

This repository contains a data analytics project that utilizes process mining methodology to analyse the real-life event log of an order-to-cash process. The dataset contains order requests spaning 3 years from 14th February 2007 to 13th February 2010.

# Project Overview
The goal of this project is to utilize process mining techniques to:
1. Discover the actual order-to-cash process.
2. Identify inefficiencies and bottlenecks in the process.
3. Generate insights to improve process efficiency.

# Methodology
1. Data Collection
2. Data Quality Check
3. Data Transformation
4. Process Discovery
5. Timing Analysis
6. Suggestions for process improvement

# Technologies Used
1. Python
2. Pandas
3. Graphviz library
4. Jupyter Notebook
5. Microsoft PowerBI

# Data collection
From the initial review of the dataset, it contained 82,906 cases and there were initially about 28 start/end activities. The <b>_CASE_KEY</b> column was used as the case ID. The <b>ACTIVITY_EN</b> column was used as the activities and contained the following 28 activities:
1. Create Delivery
2. Goods Issue
3. Create Sales Order Item
4. Create Invoice
5. Change Scheduled date
6. Pro forma invoice
7. Credit Check Release
8. Remove Reason for rejection
9. Create Quotation
10. Delivery Block removed
11. Change Shipping Terms
12. Delivery Block set
13. Change Net Price
14. Set Reason for rejection
15. Delivery Block changed
16. Credit Check Denied
17. Change Route
18. Credit memo
19. Billing Block removed
20. Change Material
21. Billing Block set
22. Change Reason for rejection
23. Invoice cancellation
24. Debit memo
25. Change Customer
26. Billing Block changed
27. Credit memo cancellation
28. Change Delivery Amount

The dataset contains other metadata which would be used in the overall analysis. This analysis is focused on the Process Identification.

# Data quality check and transformation
The event log was reviewed to ensure the data contained were okay to use for process mining. The following was discovered:
1. Python did not automatically identify the date column. This was adjusted.
2. To analyse the end-to-end process, it was important to use completed cases. From initial analysis, we found out that some cases did not have the correct start and end events. It was assumed that this was due to the way the data was extracted from the SAP system. The dataset was extracted for events from 14th February 2007 to 13th February 2010, without considering if these cases starts or ends on these dates. The analysis carried out indicated that there are three posible start events (Create Sales Order Item, Create Delivery and Create Quotation) and one end event (Create Invoice). The eventlog was then filtered for cases which meets this scenario. After this, we were left with 66,299 completed cases.
3. Since the event log was gotten online, no process owner was contacted to gain further understanding of the process and other columns in the dataset. Therefore, if there are manual events in this process, it is not highlighted here.

# Output and Visualisations
## Data overview
![alt text](https://github.com/nkwachiabel/Process-Mining-Order-to-cash-IBM/blob/main/Images/Data%20overview.jpg?raw=true)

This page gives an overview of the dataset. It shows the number of orders, activities and events. It also highlights the net value of these order requests and number of process variants. The clustered column chart at the top right shows the number of orders per year and month. We can see that there were many orders in January 2016 and these orders reduced a lot from July 2017. It is also important to mention here that the TLC Connectivity product started in January 2017.

The pie chart shows the number of orders by product hierarchy. TLC Optical Cables was the most ordered, followed by TLC Optical Fibres. TLC Ground Cables and TLC Connectivity have not been ordered that much. While we have product hierarchy, these products are also broken down into order types. The US-Std. Order had the highest cost of order, followed by US-IC Order.

Regarding activities, the Activities by user types table shows the various activities and broken down into Human/Robot. The activity <b>Schedule Line Rejected</b> has no user type. This is because when an order is rejected, the User_Type column is empty. While the net value of the 21,159 orders are 241.83m, they contain 2,488 rejected orders amounting to $62.57m with more rejects coming from TLC Optical Cables and no reject in TLC Connectivity.

## Process discovery based on event log data
![alt text](https://github.com/nkwachiabel/Process-Mining-Order-to-cash-IBM/blob/main/Images/Process%20details.jpg?raw=true)

This page helps in analysing the process. It contains various filters including product hierarchy, order type and customer.
The gain a proper understanding of the process, the order was broken down into the 4 distinct product hierarchy. In understanding the process, 3 different analysis was carried out; (i) variant analysis, (ii) process flow and (iii) transition matrix.
* <b>Variant analysis</b>: The variant analysis starts by identifying the trace of activities in sequential order that each order request follows. After this, similar traces were grouped into process variants. This analysis, broken down into the various product hierarchy shows the following:
  * TLC Optical Cables with 12,250 orders has 726 variants. Variant 1 accounts for 38.51% of the orders and follows the following sequence Line creation -> Header Block Removed -> LgstCheckOnConfDat Removed -> Delivery -> Good Issue.
  * TLC Optical Fibres with 8,775 orders has 68 variants. Variant 1 accounts for 71% of the orders and follows the following sequence Line creation -> LgstCheckOnConfDat Removed -> Delivery -> Good Issue. (notice that the Header Block Removed activity is not included here)
  * TLC Optical Ground cables with 49 orders has 15 variants. The top 3 variants account for 55.1% of the orders and are all rejected cases. Variant 4 represents the highest variant with completed cases and follows the following sequence Line creation ->  Header Block Removed -> Address missing block set -> LgstCheckOnConfDat Set -> Sched.Line Block Removed -> Address missing block removed -> LgstCheckOnConfDat Removed -> Delivery -> Good Issue
  * TLC Connectivity with 85 orders has 17 variants. The first two variants accounts for 72.94% of the cases. The first variant is associated with Customer 1.
  
* <b>Process flow visualisation</b>: The process flow graph shows the process flow from the start to finish for the filtered product hierarchy. The Graphviz library was used to automatically generate a visual process model based on the event log data. The process starts from Line creation and either ends with Goods Issue or if the order is rejected, Schedule Line Rejected. Looking at the process flow, it appears that when a line is created, some blocks are set automatically for some customers. They include Header block, LgstCheckOnConfDat, and Credit block. These blocks are removed by the various users in the system.

* <b>Transition matrix</b>: This shows how events transition from one activity to another. The row shows the start activity while the columns shows the preceeding activities. The following were noted:

1. <b>Repeated actvities:</b> There are some activities that are done repeatedly.
![alt text](https://github.com/nkwachiabel/Process-Mining-Order-to-cash-IBM/blob/main/Images/Repeated%20activities.jpg?raw=true)

| No. | Activity | Occurrence | Product hierarchy | No. of customers | No. of orders |
| :--- | :--- | :---: | :--- | :---: | :---: |
| 1 | Document released for credit | 11 |TLC Optical Cables | 7 | 11 |
| 2 | Address mising block removed | 5 | TLC Optical Cables | 1 | 1 |
| 3 | CTR Block Removed | 58 | TLC Optical Cables | 5 | 14 |
| 4 | Sched.Line Changed Delivery Date | 476 | TLC Optical Cables | 48 | 379 |

 From the above, these activities are all repeated on the TLC Optical Cables product. Shows that there are potential areas for improvement
 
 2. <b>Gods Issue</b> 
     * Goods were issued despite Address missing block was set 3x. This occured in 3 orders with 3 different customers. Looking at the process, these orders
     * Goods were issued even when the Document blocked for credit activity was set. This occured in 3 different orders from 1 customer (Customer 206). For these 3 cases, the document was released for credit by User51 (Customer service rep) initially in May, but was later blocked for credit in December of the same year by User33 (Master Scheduler) and the Goods were still issued in December by User66 (Customer service rep). This indicates a lack of control in the system.
 3.  <b>Delivery:</b> There were 5 cases where Delivery activity was done even immediately after the Address missing block was set. A further look into these cases showed that the Address missing block was removed before the Good Issue activity was done. This can be because the Delivery activity was an automatic activity.

### Other findings
* <b>Rejected order</b>: The product with the highest number of rejected order is TLC Optical Ground Cables with 57% of the orders rejected. The reason for the rejections were not stated for root cause analysis, while TLC Connectivity has no rejected order. One notable thing about the rejected order is that none of the orders were rejected after delivery was made.

* Some events are carried out by robots in the process. Asides Delivery and Good Issue, robot can create line, remove header block and set LgstCheckOnConfDat in TLC Optical Cables hierarchy

* <b>Process benchmarking</b>
![alt text](https://github.com/nkwachiabel/Process-Mining-Order-to-cash-IBM/blob/main/Images/Process%20benchmarking.jpg?raw=true)

The process benchmarking page helps to compare different process variants using various filters; Order type, Product hierarchy, Delayed order or not. It also contains some metrics such as the median order fulfilment time, on-time order rate, etc. This shows the comparison between the most occuring variants in TLC Optical Cables and TLC Optical Fibres product. We can see that while the TLC optical median fulfilment time for Variant 1 is 60 days, it is 41 days for TLC Optical Fibres. The reason for this as we can see in the above is that despite the fact that the Header Block Removed activity in TLC Optical Cables product is done in less than a day, more time is spent in the LgstCheckOnConfDat Removed activity compared to TLC Optical Fibres product. 


## Timing analysis
![alt text](https://github.com/nkwachiabel/Process-Mining-Order-to-cash-IBM/blob/main/Images/Timing%20analysis.jpg?raw=true)

This analysis was done to analyse the bottlenecks in the process relating to timing. To know how long a process should last, we used the median duration of a case as the performance indicator. In the overall order, the median duration was 40 days. About 49.58% of cases did not meet this time target. When broken down into the various product hierarchy, the median duration were as follows.

| Product hierarchy | Median duration | % of cases greater than median duration | Bottleneck | % of Ontime orders |
| :--- | :---: | :---: | :--- | :---: |
| TLC Optical Cables | 55 days | 49.77% | Address missing Block Removed | 95% |
| TLC Optical Ground Cables | 136 days | 42.86% | Sched.Line Changed Delivery Date | 82% |
| TLC Optical Fibres | 39 days | 49.87% | Header Block Set | 94% |
| TLC Connectivity | 81 days | 49.41% | Sched.Line Changed Delivery Date | 100% |

From the median duration above, this indicates that there is room for improvement to ensure faster delivery/goods issue. The bottleneck regarding address missing block can be addressed by making sure that all customers in the database have an updated address. This activity takes an average of 19 days to be rectified, making this duration longer.

For sched.line changed delivery date, this is not clear if the change is coming from the customer or the company. If coming from the company, this means that the company needs to improve its inventory planning to avoid this from happening.

## Users analysis
![alt text](https://github.com/nkwachiabel/Process-Mining-Order-to-cash-IBM/blob/main/Images/Users%20analysis.jpg?raw=true)

Users were analysed to understand who does what activity in the process. This analysis was focused on the human users and was broken down into the different product hierarchies. The graph at the extreme left shows the users and their roles in the company. This shows that in all the products, there are so many people acting as Customer Service Representatives compared to other roles.
* In TLC Connectivity, there are only two roles; Logistic operator(User12) and Customer service rep (6 users). The segregation of duties shows that the customer service rep can perform all activities while the Logistic operator only performs the Header Block Removed activity. The most active customer service rep is User 20.
* In TLC Optical cables, there are more roles; Material planner (User75), IT HQ Logistic (User74), External fiber sales logistic (User38), D.C. Manager, Corporate credit manager (User57), Accessories manager, credit analyst (User58, User59), customer service manager (User13, User19,User28), logistic operator (User12, User29, User34, User70), customer service rep (21 users), data engineer manager, design engineer, intercompany planner (User21), local IT (User25, User36, User41), master scheduler (User31, User32, User33), production planner (User42), logistic manager (User35, User44), buyer (User49), Cut line team leader (User72) and product manager (User48). No user handles more than two roles. This shows appropriate segregation of duties and no one user has too much workload. There are 8 users where the role == empty. They handle the Delivery, CTR Block removed activities. This analysis shows that there is a user tagged Buyer and the only actvity this user does is to reschedule the delivery date. The customer service rep can do all activities.
* TLC Optical fibres has 4 active roles; NA Fiber Sales and service manager (User16), External fiber sales logistic (User38), customer service representative (User43, User51, User9) and corporate credit manager (User57). Similar to the above, the customer service rep does all activities. The corporate credit manager releases document for credit, external fiber sales logistic handles the sched.line changed delivery date and LgstCheckOnConfDat removed activity. In this product, User9 does majority of the work.
* Finally, TLC Optical ground cables has 8 active roles; master scheduler, logistic operator, external fiber sales logistic, data engineer manager, design engineer, customer service rep, customer service manager. As usual, customer service rep performs all activities, customer service manager does only Line creation, etc.
* The Handover of work between users shows how tasks are handed over between the various roles. For example, in the above, the customer service manager works only with the customer service rep, while the customer service rep handsover work to everyother person except the external fiber sales logistic.


## Customer details
![alt text](https://github.com/nkwachiabel/Process-Mining-Order-to-cash-IBM/blob/main/Images/Customer%20details.jpg?raw=true)

This page shows information relating to a particular customer by using the filter at the top left of the screen.

## Order details
![alt text](https://github.com/nkwachiabel/Process-Mining-Order-to-cash-IBM/blob/main/Images/Order%20details.jpg?raw=true)

This page shows information relating to a particular case by using the filter at the top right of the screen.

## Open orders
![alt text](https://github.com/nkwachiabel/Process-Mining-Order-to-cash-IBM/blob/main/Images/Open%20orders.jpg?raw=true)

This page shows information relating to incomplete cases. These are cases which starts from Line creation but does not end in Good issue or is not rejected. From the eventlog, there are 8,908 uncompleted orders with total value of $1,150.69m. We can further split this uncompleted orders into two categories; Services and Products. 

* The Service category refers to those orders which relates to service delivery. There are two products here; TLC Services and TLC Other. They are a total of 191 orders. The only activity done is Line creation and they have been open for a while. The total net value is $0.11m. For this products, they will remain open until the service contract is ended.
* The Product category to the products identified in the process analysis; Optical cables, Optical fibres, Connectivity and Optical ground cables. They contain 8,717 cases. In some of these cases, the only activity done is the Line creation activity and this is done across over 120 customers.
* The total number of orderd which do not have Line creation as the latest activity is 5,568. LgstCheckOnConfDat Removed is the highest last activity which amounts to 37% of these orders, followed by Delivery (34.63%) and Sched.Line Changed Delivery Date (13%).
* This page can be used to track open orders to monitor cases which are over their expected days.

# Process improvement
Based on the analysis, areas for improvement were identified such as:
* <b>Process redesign</b>: From the process discovery, it can be seen that some controls should be put in place. There are cases where blocks were set but Delivery still occured and goods were issued. Additional controls should be put in place to avoid this.
* <b>Segregation of duties</b>The customer service representatives carry out all the activities. This shows that there is lack of segregation of duties and this should be avoided to mitigate fraud. There are a lot of customer service representatives. One option can be to move some of the customer service representatives who posses the skills to carry out other roles to other departments. For example, despite the fact that customer service reps can do every activity, in TLC connectivity, User20 still does most of the work. Segregating the duties will reduce the burden on User20 to ensure other activities are not taking much time.
* Real-time dashboard: A real-time dashboard should be made available for those open cases to be monitored. This dashboard should include key metrics to ensure that they are not deviating from the expected process and reminders should be sent to process owners to ensure compliance. This would help in reducing processing times and improved overall process efficiency.

# Limitation
No process owner was reached out to confirm the validity of the expected process

# Repository structure
* 'Data/': Contains the data used for analysis
* 'Notebook/': Jupyter notebook detailing the data cleaning, process discovery and analysis
* 'Output/': Includes the PowerBI output and a PDF file

# Contributions
Contributions to this repository are welcome! If you encounter any issues or have suggestions for improvement, please open an issue or submit a pull request.

# Contact
For any questions or inquiries, please contact nkwachiabel@gmail.com
