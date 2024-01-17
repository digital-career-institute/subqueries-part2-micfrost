## Create Tables
```SQL
CREATE TABLE RepairTechnicians (
    TechnicianID INT PRIMARY KEY,
    FirstName VARCHAR(50),
    LastName VARCHAR(50),
    Email VARCHAR(100),
    Phone VARCHAR(15),
    Address VARCHAR(255),
    Specialization VARCHAR(100),
    Experience INT
);

CREATE TABLE RepairJobs (
    JobID INT PRIMARY KEY,
    TechnicianID INT,
    FOREIGN KEY (TechnicianID) REFERENCES RepairTechnicians(TechnicianID),
    BicycleOwner VARCHAR(100),
    BicycleModel VARCHAR(50),
    RepairDate DATE,
    RepairType VARCHAR(50),
    Cost DECIMAL(10, 2),
    Notes TEXT
);
```
## Insert Records
```SQL
INSERT INTO RepairTechnicians VALUES
(1, 'Bob', 'Wrenchington', 'bob.wrench@example.com', '555-1234', '123 Tool Lane, Nutsville', 'Flat Tires', 5),
(2, 'Alice', 'Spannerstein', 'alice.spanner@example.com', '555-5678', '456 Socket Street, Boltburgh', 'Rusty Chains', 7),
(3, 'Charlie', 'Screwdriverman', 'charlie.screw@example.com', '555-9101', '789 Nut Avenue, Screwsville', 'Broken Gears', 3),
(4, 'Daisy', 'Hammerwoman', 'daisy.hammer@example.com', '555-1122', '987 Nail Road, Hammerstown', 'Loose Brakes', 8),
(5, 'Frank', 'Plierface', 'frank.plier@example.com', '555-3344', '654 Wrench Way, Plierville', 'Squeaky Pedals', 6);


INSERT INTO RepairJobs VALUES
(101, 1, 'John Doe', 'Speedster 5000', '2024-02-15', 'Oil Change', 200.50, 'Replaced oil with WD-40, now it runs like a dream!'),
(102, 2, 'Jane Smith', 'Mountain Climber', '2024-03-10', 'Chain Repair', 105.75, 'Applied love and care to the rusty chain, good as new!'),
(103, 3, 'Sam Johnson', 'City Cruiser', '2024-04-05', 'Gear Adjustment', 30.00, 'Realigned gears; now shifts smoother than butter'),
(104, 4, 'Emma White', 'Vintage Velocipede', '2024-05-20', 'Brake Replacement', 40.25, 'Installed turbo brakes; bicycle now stops on a dime!'),
(105, 5, 'Alex Green', 'Electric E-Bike', '2024-06-15', 'Battery Upgrade', 500.75, 'Installed a flux capacitor for infinite biking power!'),
(106, 5, 'Alex Böue', 'Byby Bike', '2024-06-13', 'Extention', 30.75, 'wow Blink Blink');
```
## Display Tables
```SQL
SELECT * FROM public.RepairTechnicians
SELECT * FROM public.RepairJobs
```
## Task 1: Aggregate Functions and Subqueries
Task: Find the average number of years of experience for technicians who have performed repair jobs on bicycles costing
more than $100. Include the technician's name and the average experience.
```SQL
SELECT rt.FirstName, rt.LastName, ROUND(AVG(rt.Experience),2) AS average_experience 
FROM RepairTechnicians rt
JOIN RepairJobs rj ON rt.TechnicianID = rj.TechnicianID
WHERE rj.cost > 100
GROUP BY rt.FirstName, rt.LastName;
```
## Task 2: Combining Joins and Subqueries
Task: List the names of technicians who have not performed any repair jobs.
```SQL
SELECT rt.FirstName, rt.LastName
FROM RepairTechnicians rt
JOIN RepairJobs rj ON rt.TechnicianID = rj.TechnicianID
WHERE rj.TechnicianID IS NULL;
```
## Task 3: Using EXISTS and IN ( I used HAVING)
Task: Find the technicians who have worked on repairs for bicycles owned by more than one person. Include the technician's name 
and the count of distinct bicycle owners.
```SQL
SELECT rt.FirstName, rt.LastName, COUNT(DISTINCT rj.BicycleOwner) AS OwnerCount
FROM RepairTechnicians rt
JOIN RepairJobs rj ON rt.TechnicianID = rj.TechnicianID
GROUP BY rt.FirstName, rt.LastName
HAVING COUNT(DISTINCT rj.BicycleOwner) >= 2;
```
## Task 4: Subqueries with GROUP BY and HAVING
Task: Identify technicians who have performed repairs on at least 2 bicycles and list the total number of repairs they have conducted.
Include the technician's name and the total number of repairs.
```SQL
SELECT rt.TechnicianID, rt.FirstName, rt.LastName, COUNT(DISTINCT rj.JobID) AS OwnerCount
FROM RepairTechnicians rt
JOIN RepairJobs rj ON rt.TechnicianID = rj.TechnicianID
GROUP BY rt.TechnicianID, rt.FirstName, rt.LastName
HAVING COUNT(DISTINCT rj.JobID) >= 2;
```
## Task 5: Subqueries with AND, OR, and NOT
Task: List the technicians who have performed repairs on bicycles with a cost between $50 and $150 OR have more than 7 years of experience. 
Include the technician's name and the repair cost.
```SQL
SELECT rt.FirstName, rt.LastName, rj.Cost
FROM RepairTechnicians rt
JOIN RepairJobs rj ON rt.TechnicianID = rj.TechnicianID
WHERE (rj.Cost BETWEEN 50 AND 150) OR (rt.Experience > 7);
```
## Task 6: Combining EXISTS and Aggregate Functions
Task: Find technicians who have not performed any repairs on bicycles with a cost exceeding $200. 
Include the technician's name and the total number of such repairs.
```SQL
SELECT rt.FirstName, rt.LastName, COUNT(rj.JobID) AS TotalExpensiveRepairs
FROM RepairTechnicians rt
LEFT JOIN RepairJobs rj ON rt.TechnicianID = rj.TechnicianID
WHERE NOT EXISTS (
    SELECT 1
    FROM RepairJobs rj2
    WHERE rj2.TechnicianID = rt.TechnicianID
    AND rj2.Cost > 200
)
GROUP BY rt.FirstName, rt.LastName;
