# Employee Database Management System with MongoDB

A comprehensive Python-based employee database system using MongoDB for data storage and complex querying. This project demonstrates advanced MongoDB operations, data generation, and query optimization techniques.

## Project Overview

This repository contains a complete employee database management system that:
- Generates 10,000 realistic employee records using Python Faker
- Implements complex MongoDB queries with proper error handling
- Provides statistical analysis and data insights
- Demonstrates MongoDB best practices and optimization techniques

## Table of Contents

- [Features](#features)
- [Technologies Used](#technologies-used)
- [Installation](#installation)
- [Data Generation](#data-generation)
- [MongoDB Queries](#mongodb-queries)
- [Usage Examples](#usage-examples)
- [Query Optimization](#query-optimization)
- [Sample Outputs](#sample-outputs)
- [Export Options](#export-options)
- [Contributing](#contributing)

## Features

### Data Generation
- **10,000 realistic employee records** with proper data relationships
- **Department-specific positions and skills** (Engineering, Marketing, Sales, etc.)
- **Salary correlation** with position levels and experience
- **Realistic date relationships** (hire dates, promotions)
- **Geographic distribution** across US states
- **Performance ratings** with normal distribution

### Advanced MongoDB Queries
- **High Performers Analysis** - Complex filtering with multiple conditions
- **Experience-Based Filtering** - Range queries with salary constraints  
- **Salary Outlier Detection** - NOT operations and data aggregation
- **Recent Hires Analysis** - Date calculations and tenure metrics

### Production-Ready Features
- **Comprehensive error handling** for database operations
- **Statistical analysis** with pandas integration
- **Query optimization** with index recommendations
- **Multiple export formats** (JSON, CSV, Excel)
- **Professional logging** and debugging tools

## Technologies Used

- **Python 3.13+**
- **MongoDB 4.4+**
- **PyMongo** - MongoDB driver for Python
- **Pandas** - Data analysis and manipulation
- **Faker** - Realistic test data generation
- **BSON** - MongoDB ObjectId handling
- **Jupyter Notebook** - Interactive development environment

## Installation

### Prerequisites
- Python 3.8 or higher
- MongoDB Community Server
- Git

### Setup Instructions

1. **Clone the repository**
   ```bash
   git clone https://github.com/datasavvysarah/mongodb_employee_datapipeline.git
   cd employee-database-mongodb
   ```

2. **Create virtual environment**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Start MongoDB**
   ```bash
   # On macOS with Homebrew
   brew services start mongodb-community
   
   # On Windows
   net start MongoDB
   
   # On Linux
   sudo systemctl start mongod
   ```

5. **Verify installation**
   ```bash
   python -c "import pymongo; print('MongoDB connection successful!')"
   ```


## Data Generation

### Employee Record Structure
```python
{
    "_id": ObjectId("..."),
    "employee_id": "EMP001",
    "first_name": "John",
    "last_name": "Doe",
    "email": "john.doe@company.com",
    "department": "Engineering",
    "position": "Senior Developer",
    "salary": 95000,
    "years_experience": 8,
    "performance_rating": 4.2,
    "skills": ["Python", "JavaScript", "MongoDB", "Docker"],
    "hire_date": "2020-03-15",
    "last_promotion": "2022-06-01",
    "is_remote": true,
    "address": {
        "city": "San Francisco",
        "state": "CA",
        "zip_code": "94102"
    }
}
```

### Key Data Features
- **10 departments** with realistic position hierarchies
- **Salary ranges** correlated with experience and position level
- **120+ skills** distributed by department specialization
- **Realistic relationships** between hire dates and promotions
- **Geographic diversity** across 20 US states

## MongoDB Queries

### Query 1: High Performers
**Objective**: Find employees with performance rating ≥ 4.0 AND salary > $80,000

```python
query = {
    "$and": [
        {"performance_rating": {"$gte": 4.0}},  # Inclusive rating
        {"salary": {"$gt": 80000}}              # Exclusive salary
    ]
}
```

**Operators Explained**:
- `$and`: Explicit multiple condition combination
- `$gte`: Greater than or equal (inclusive threshold)
- `$gt`: Greater than (exclusive threshold)

### Query 2: Experience-Based Filtering
**Objective**: Find employees with 5-10 years experience earning $70K-$120K

```python
query = {
    "$and": [
        {"years_experience": {"$gte": 5, "$lte": 10}},  # Inclusive range
        {"salary": {"$gte": 70000, "$lte": 120000}}     # Inclusive range
    ]
}
```

**Operators Explained**:
- `$gte` & `$lte`: Creates inclusive ranges for both criteria
- Range optimization for compound filtering

### Query 3: Salary Outliers
**Objective**: Find employees outside $60K-$100K salary range

```python
pipeline = [
    {
        "$match": {
            "$or": [
                {"salary": {"$lt": 60000}},    # Below range
                {"salary": {"$gt": 100000}}    # Above range
            ]
        }
    },
    {
        "$project": {
            "full_name": {"$concat": ["$first_name", " ", "$last_name"]},
            "salary": 1,
            "years_experience": 1
        }
    }
]
```

**Operators Explained**:
- `$or`: Logical OR for range exclusion
- `$lt` & `$gt`: Less than and greater than for boundaries
- `$concat`: String concatenation in aggregation pipeline

### Query 4: Recent High Performers
**Objective**: Find employees hired in last 2 years with rating > 3.5

```python
pipeline = [
    {
        "$match": {
            "$and": [
                {"hire_date": {"$gte": "2023-08-13"}},
                {"performance_rating": {"$gt": 3.5}}
            ]
        }
    },
    {
        "$project": {
            "full_name": {"$concat": ["$first_name", " ", "$last_name"]},
            "tenure_months": {
                "$divide": [
                    {"$subtract": [{"$now": {}}, "$hire_date_obj"]},
                    1000 * 60 * 60 * 24 * 30.44
                ]
            },
            "annual_salary": "$salary"
        }
    }
]
```

**Operators Explained**:
- `$dateFromString`: Converts string dates to MongoDB dates
- `$subtract` & `$divide`: Mathematical operations for tenure calculation
- `$now`: Current timestamp for relative date calculations

## Usage Examples

### Basic Usage
```python
from database_queries.query_functions import EmployeeQueryManager

# Initialize query manager
query_manager = EmployeeQueryManager()

# Execute individual queries
high_performers = query_manager.find_high_performers()
experienced_workers = query_manager.find_experienced_mid_earners()
salary_outliers = query_manager.find_salary_outliers()
recent_hires = query_manager.find_recent_high_performers()

# Execute all queries at once
all_results = query_manager.run_all_queries()
```

### Data Generation
```python
from data_generation.employee_faker import generate_employee_database

# Generate 10,000 employee records
employee_db = generate_employee_database(10000)

# Insert into MongoDB
from pymongo import MongoClient
client = MongoClient('mongodb://localhost:27017/')
db = client['company_db']
collection = db['employees']
collection.insert_many(employee_db)
```

### Export Data
```python
import pandas as pd
import json

# Export to different formats
df = pd.json_normalize(employee_db)

# Excel export
df.to_excel('employees.xlsx', index=False)

# CSV export
df.to_csv('employees.csv', index=False)

# JSON export
with open('employees.json', 'w') as f:
    json.dump(employee_db, f, indent=2, default=str)
```

## Query Optimization

### Recommended Indexes
```javascript
// Create indexes for optimal query performance
db.employees.createIndex({"performance_rating": 1, "salary": 1})      // Query 1
db.employees.createIndex({"years_experience": 1, "salary": 1})        // Query 2  
db.employees.createIndex({"salary": 1})                              // Query 3
db.employees.createIndex({"hire_date": -1, "performance_rating": 1}) // Query 4
```

### Performance Tips
- **Use compound indexes** that match query patterns
- **Put most selective fields first** in compound indexes
- **Always use projection** to limit returned data
- **Prefer aggregation pipeline** for complex transformations
- **Use `explain()`** to analyze query performance

## Sample Outputs

### High Performers Query Results
```
Found 847 high performers
Average salary: $108,542.33
Average rating: 4.4

Sample Results:
   first_name last_name    department  salary  performance_rating
   Sarah      Johnson      Engineering  112000              4.3
   Michael    Davis        Sales        95500               4.1
   Jennifer   Wilson       Product      128000              4.7
```

### Salary Outliers Analysis
```
Found 3,247 employees outside $60K-$100K range
High earners (>$100K): 1,523
Lower earners (<$60K): 1,724

Sample Results:
   full_name          salary  years_experience
   David Rodriguez    145000                12
   Lisa Thompson       38000                 1
   Robert Kim         156000                15
```

## Data Insights

- **Total Employees**: 10,000
- **Average Salary**: $82,456
- **Remote Workers**: 4,200 (42%)
- **Top Department**: Engineering (1,847 employees)
- **Performance Distribution**: Normal curve around 3.5 rating

## Export Options

The system supports multiple export formats:

### JSON Export
```python
with open('employees.json', 'w') as f:
    json.dump(employee_db, f, indent=2, default=str)
```

### Excel Export
```python
df.to_excel('employees.xlsx', index=False)
```

### CSV Export
```python
df.to_csv('employees.csv', index=False)
```

### MongoDB Export
```bash
mongoexport --db company_db --collection employees --out employees.json --pretty
```

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

⭐ **Star this repository** if you find it helpful!

**Built with ❤️ using Python and MongoDB**

Student: Sarah Uko

Course: AltSchool Data Engineering Baraka 2025

Date: 14th August, 2025.
