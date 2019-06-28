
## Northwind Co: Company Sales Analysis
Flatiron Module 2 Project: Paul Woody

![Paul](Paul.jpeg)

# Project Overview:
For this project, we'll be exploring the data provided by our client, Northwind, containing companywide sales data for a range of products.  Our goal is to derive the significance of discounts on overall sales as well as to provide the client with additional business recommendations based on the results of EDA. 

To complete our analysis, we will follow the OSEMN Data Science process as outlined [here](http://www.dataists.com/tag/osemn/).


## Provided ERD for Northwind Co:
![Northwind ERD](Northwind_ERD.png)


# Obtain / Load Data:

```python
from sqlalchemy import inspect
inspector = inspect(engine)

# Viewing Table Names to compare with ERD:
print(inspector.get_table_names())
```
    ['Category', 'Customer', 'CustomerCustomerDemo', 'CustomerDemographic', 'Employee', 'EmployeeTerritory', 'Order', 'OrderDetail', 'Product', 'Region', 'Shipper', 'Supplier', 'Territory']


As demonstrated by the output of table names above, there are many discrepancies between the Table/Column names in the provided ERD and in the actual database. In order to successfully scrub/explore the dataset, we must first verify the table/column names in our database, as well as account for missing values and improperly stored dtypes.


```python
# Using above function to iterate over all tables:
tables = inspector.get_table_names()
for table in tables:
    print(get_column_info(table))
```
```
Sample output:    
    
    ------------------------------------------------------------------------
    Table Name: OrderDetail, Primary Key: ['Id']
    ------------------------------------------------------------------------
    
    Column Name: Id, Column Type: VARCHAR(8000)
    Column Name: OrderId, Column Type: INTEGER
    Column Name: ProductId, Column Type: INTEGER
    Column Name: UnitPrice, Column Type: DECIMAL
    Column Name: Quantity, Column Type: INTEGER
    Column Name: Discount, Column Type: FLOAT
    None
```    


# Pre-Process/Scrub Data

Now that the dataset has been loaded, we should familiarize ourselves with Northwind and what they do as a company prior to forming specific inquiries and queries. For each table in the Northwind Database, we'll preview the contents, properly account for null values, and ensure that column data is stored in the proper format prior to hypothesis testing.

## Category Table Scrubbing:


```python
# Viewing Category Table:
df_category.head(3)
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Id</th>
      <th>CategoryName</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Beverages</td>
      <td>Soft drinks, coffees, teas, beers, and ales</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Condiments</td>
      <td>Sweet and savory sauces, relishes, spreads, an...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Confections</td>
      <td>Desserts, candies, and sweet breads</td>
    </tr>
  </tbody>
</table>
</div>

```python
# Visualizing Category Names:
df_category.groupby(['CategoryName']).head(10)
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Id</th>
      <th>CategoryName</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Beverages</td>
      <td>Soft drinks, coffees, teas, beers, and ales</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Condiments</td>
      <td>Sweet and savory sauces, relishes, spreads, an...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Confections</td>
      <td>Desserts, candies, and sweet breads</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Dairy Products</td>
      <td>Cheeses</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Grains/Cereals</td>
      <td>Breads, crackers, pasta, and cereal</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>Meat/Poultry</td>
      <td>Prepared meats</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>Produce</td>
      <td>Dried fruit and bean curd</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8</td>
      <td>Seafood</td>
      <td>Seaweed and fish</td>
    </tr>
  </tbody>
</table>
</div>



Based on the contents of the Category Table, we can reasonably conclude that Northwind is a Food & Beverage Distributor. We may choose to explore sales perfomance by Category in our exploratory data analysis, but for now, let's get a better idea of the products sold by Northwind.


### Product Table Scrubbing:

Now that we have a broad idea of what Northwind does, let's peruse the products they sell.


```python
# Reviewing Product DataFrame:
df_product.head(3)
```


<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Id</th>
      <th>ProductName</th>
      <th>SupplierId</th>
      <th>CategoryId</th>
      <th>QuantityPerUnit</th>
      <th>UnitPrice</th>
      <th>UnitsInStock</th>
      <th>UnitsOnOrder</th>
      <th>ReorderLevel</th>
      <th>Discontinued</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Chai</td>
      <td>1</td>
      <td>1</td>
      <td>10 boxes x 20 bags</td>
      <td>18.0</td>
      <td>39</td>
      <td>0</td>
      <td>10</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Chang</td>
      <td>1</td>
      <td>1</td>
      <td>24 - 12 oz bottles</td>
      <td>19.0</td>
      <td>17</td>
      <td>40</td>
      <td>25</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Aniseed Syrup</td>
      <td>1</td>
      <td>2</td>
      <td>12 - 550 ml bottles</td>
      <td>10.0</td>
      <td>13</td>
      <td>70</td>
      <td>25</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>


Northwind carries a total of 77 products.

For the purposes of our analysis, we will ignore the presence of Discontinued Products, including them in our analysis. Should we find that specific Discontinued products perform as good as or better than other products in the dataset, we may recommend that Northwind choose to restock these products.

In a future project, Northwind may also consider investigating the sales performance of specific products on a regional or national level to maximize product performance by region, reduce shipping costs, and reduce shrink through supply chain optimization.

### Customer Table Scrubbing:

After defining what it is Northwind does, we should next explore Northwind's customer dataset. Is Northwind a local distributor? Where are Northwind's customers located? Is Northwind's target demographic clear?

Reviewing the data to answer some of the questions above should give us insight into potential pitfalls faced by Northwind as well as to guide the scope of our inquiry. Based on differences in regional sales performance, it may be prudent to prioritize expansion in a particular region, change marketing strategies (such as discounts) in specific regions, or hire additional employees.


```python
# Viewing Customer DataFrame:
df_customer.head(3)
```


<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Id</th>
      <th>CompanyName</th>
      <th>ContactName</th>
      <th>ContactTitle</th>
      <th>Address</th>
      <th>City</th>
      <th>Region</th>
      <th>PostalCode</th>
      <th>Country</th>
      <th>Phone</th>
      <th>Fax</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ALFKI</td>
      <td>Alfreds Futterkiste</td>
      <td>Maria Anders</td>
      <td>Sales Representative</td>
      <td>Obere Str. 57</td>
      <td>Berlin</td>
      <td>Western Europe</td>
      <td>12209</td>
      <td>Germany</td>
      <td>030-0074321</td>
      <td>030-0076545</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ANATR</td>
      <td>Ana Trujillo Emparedados y helados</td>
      <td>Ana Trujillo</td>
      <td>Owner</td>
      <td>Avda. de la Constitución 2222</td>
      <td>México D.F.</td>
      <td>Central America</td>
      <td>05021</td>
      <td>Mexico</td>
      <td>(5) 555-4729</td>
      <td>(5) 555-3745</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ANTON</td>
      <td>Antonio Moreno Taquería</td>
      <td>Antonio Moreno</td>
      <td>Owner</td>
      <td>Mataderos  2312</td>
      <td>México D.F.</td>
      <td>Central America</td>
      <td>05023</td>
      <td>Mexico</td>
      <td>(5) 555-3932</td>
      <td>None</td>
    </tr>
  </tbody>
</table>
</div>


```python
df_customer_demo = df_customer.groupby(['Region'])['CompanyName'].count()
df_customer_demo.head()
```




    Region
    British Isles       8
    Central America     5
    Eastern Europe      1
    North America      16
    Northern Europe     4
    Name: CompanyName, dtype: int64




```python
df_customer_demo_country = df_customer.groupby(['Country'])['CompanyName'].count()
df_customer_demo_country
```

    Country
    Argentina       3
    Austria         2
    Belgium         2
    Brazil          9
    Canada          3
    Denmark         2
    Finland         2
    France         11
    Germany        11
    Ireland         1
    Italy           3
    Mexico          5
    Norway          1
    Poland          1
    Portugal        2
    Spain           5
    Sweden          2
    Switzerland     2
    UK              7
    USA            13
    Venezuela       4
    Name: CompanyName, dtype: int64



Based on their customer demographics, it is evident that Northwind operates internationally, with customers in 21 different countries. The USA, France, and Germany contain the highest number of Northwind customers, with 13, 11, and 11 respectively. 

Regionally, North American contains the highest number of Northwind customers with a total of 16. Should we choose to evaluate sales performance at a national level, we should ensure that sample sizes are large enough to provide meaningful analysis. For this reason, it may be prudent to first explore trends at the regional level.

Should we choose to explore regional sales performance in our EDA, we may also consider grouping smaller 'sub-regions' such as Northern/Eastern Europe to increase sample sizes.

Should Northwind continue to expand in some of the "smaller" countries with less customers, Northwind may wish to investigate sales performance at a National level in a future experiment.


### Employee Table Scrubbing:


```python
# Reviewing Employee DataFrame:
df_employee.head(3)
```


<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Id</th>
      <th>LastName</th>
      <th>FirstName</th>
      <th>Title</th>
      <th>TitleOfCourtesy</th>
      <th>BirthDate</th>
      <th>HireDate</th>
      <th>Address</th>
      <th>City</th>
      <th>Region</th>
      <th>PostalCode</th>
      <th>Country</th>
      <th>HomePhone</th>
      <th>Extension</th>
      <th>Photo</th>
      <th>Notes</th>
      <th>ReportsTo</th>
      <th>PhotoPath</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Davolio</td>
      <td>Nancy</td>
      <td>Sales Representative</td>
      <td>Ms.</td>
      <td>1980-12-08</td>
      <td>2024-05-01</td>
      <td>507 - 20th Ave. E. Apt. 2A</td>
      <td>Seattle</td>
      <td>North America</td>
      <td>98122</td>
      <td>USA</td>
      <td>(206) 555-9857</td>
      <td>5467</td>
      <td>None</td>
      <td>Education includes a BA in psychology from Col...</td>
      <td>2.0</td>
      <td>http://accweb/emmployees/davolio.bmp</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Fuller</td>
      <td>Andrew</td>
      <td>Vice President, Sales</td>
      <td>Dr.</td>
      <td>1984-02-19</td>
      <td>2024-08-14</td>
      <td>908 W. Capital Way</td>
      <td>Tacoma</td>
      <td>North America</td>
      <td>98401</td>
      <td>USA</td>
      <td>(206) 555-9482</td>
      <td>3457</td>
      <td>None</td>
      <td>Andrew received his BTS commercial in 1974 and...</td>
      <td>NaN</td>
      <td>http://accweb/emmployees/fuller.bmp</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Leverling</td>
      <td>Janet</td>
      <td>Sales Representative</td>
      <td>Ms.</td>
      <td>1995-08-30</td>
      <td>2024-04-01</td>
      <td>722 Moss Bay Blvd.</td>
      <td>Kirkland</td>
      <td>North America</td>
      <td>98033</td>
      <td>USA</td>
      <td>(206) 555-3412</td>
      <td>3355</td>
      <td>None</td>
      <td>Janet has a BS degree in chemistry from Boston...</td>
      <td>2.0</td>
      <td>http://accweb/emmployees/leverling.bmp</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_employee_names = df_employee.groupby(['FirstName', 'LastName'])['FirstName', 'LastName', 'Region', 'Country', 'City', 'HireDate']
df_employee_names.head(10)
```


<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>FirstName</th>
      <th>LastName</th>
      <th>Region</th>
      <th>Country</th>
      <th>City</th>
      <th>HireDate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Nancy</td>
      <td>Davolio</td>
      <td>North America</td>
      <td>USA</td>
      <td>Seattle</td>
      <td>2024-05-01</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Andrew</td>
      <td>Fuller</td>
      <td>North America</td>
      <td>USA</td>
      <td>Tacoma</td>
      <td>2024-08-14</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Janet</td>
      <td>Leverling</td>
      <td>North America</td>
      <td>USA</td>
      <td>Kirkland</td>
      <td>2024-04-01</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Margaret</td>
      <td>Peacock</td>
      <td>North America</td>
      <td>USA</td>
      <td>Redmond</td>
      <td>2025-05-03</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Steven</td>
      <td>Buchanan</td>
      <td>British Isles</td>
      <td>UK</td>
      <td>London</td>
      <td>2025-10-17</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Michael</td>
      <td>Suyama</td>
      <td>British Isles</td>
      <td>UK</td>
      <td>London</td>
      <td>2025-10-17</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Robert</td>
      <td>King</td>
      <td>British Isles</td>
      <td>UK</td>
      <td>London</td>
      <td>2026-01-02</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Laura</td>
      <td>Callahan</td>
      <td>North America</td>
      <td>USA</td>
      <td>Seattle</td>
      <td>2026-03-05</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Anne</td>
      <td>Dodsworth</td>
      <td>British Isles</td>
      <td>UK</td>
      <td>London</td>
      <td>2026-11-15</td>
    </tr>
  </tbody>
</table>
</div>



As seen above, Northwind has a total of 9 employees and offices in the Seattle area and London. In our EDA, we may choose to compare sales performance between the Seattle and London offices. However, from the data above, we can determine that employees are making international sales and are likely assigned a specific territory.


### Territory, EmployeeTerritory, and Region Table Scrubbing:

```python
# Creating a new query to identify employee:territory/region pairs:
df_employee_terr_pairs = pd.read_sql_query("""SELECT e.LastName, t.TerritoryDescription, r.RegionDescription \
            FROM [Employee] e \
            INNER JOIN [EmployeeTerritory] et on et.EmployeeId = e.Id \
            INNER JOIN [Territory] t on t.Id = et.TerritoryId \
            INNER JOIN [Region] r on r.Id = t.RegionId
            GROUP BY e.LastName
            ORDER BY r.RegionDescription ASC;""", engine)
df_employee_terr_pairs.head(10)
```

<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>LastName</th>
      <th>TerritoryDescription</th>
      <th>RegionDescription</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Buchanan</td>
      <td>Fairport</td>
      <td>Eastern</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Davolio</td>
      <td>Neward</td>
      <td>Eastern</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Fuller</td>
      <td>Louisville</td>
      <td>Eastern</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Peacock</td>
      <td>Cary</td>
      <td>Eastern</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Callahan</td>
      <td>Racine</td>
      <td>Northern</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Dodsworth</td>
      <td>Minneapolis</td>
      <td>Northern</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Leverling</td>
      <td>Tampa</td>
      <td>Southern</td>
    </tr>
    <tr>
      <th>7</th>
      <td>King</td>
      <td>Santa Cruz</td>
      <td>Western</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Suyama</td>
      <td>Seattle</td>
      <td>Western</td>
    </tr>
  </tbody>
</table>
</div>



Based on the results of the query above, we can observe that the Eastern region is assigned the most sales team members. In our EDA, we might choose to investigate regional sales performance as a measure of average sales per employee or average monthly sales by region. Through this analysis, we could identify higher-performing regions (even if total revenue is lower) that could be ideal targets for expansion or develop data driven marketing strategies to encourage rapid growth.

However, following this line of inquiry may be more productive in a future experiment, as the territories and regions are not currently clearly defined in the dataset.


### Order, OrderDetail Table Scrubbing:

```python
df_order.ShipRegion.unique()
```

    array(['Western Europe', 'South America', 'Central America',
           'North America', 'Northern Europe', 'Scandinavia',
           'Southern Europe', 'British Isles', 'Eastern Europe'], dtype=object)


By reviewing the unique values for the ShipRegion column, we can determine that ShipRegion maps to the same region descriptions from the Region table, signifying that this data can be used to review regional sales.

Let's briefly review this association:


```python
df_region_preview = pd.read_sql_query("""SELECT o.ShipRegion, \
            SUM(od.Quantity * od.UnitPrice * (1-od.Discount)) TotalRev \
            FROM [OrderDetail] od \
            INNER JOIN [Order] o on o.Id = od.OrderId \
            GROUP BY o.ShipRegion \
            ORDER BY TotalRev DESC""", engine)
df_region_preview.head(10)
```

  
<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ShipRegion</th>
      <th>TotalRev</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Western Europe</td>
      <td>505164.3085</td>
    </tr>
    <tr>
      <th>1</th>
      <td>North America</td>
      <td>295780.9005</td>
    </tr>
    <tr>
      <th>2</th>
      <td>South America</td>
      <td>171855.5055</td>
    </tr>
    <tr>
      <th>3</th>
      <td>British Isles</td>
      <td>108951.2150</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Northern Europe</td>
      <td>87156.1625</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Southern Europe</td>
      <td>45225.7175</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Scandinavia</td>
      <td>24545.2025</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Central America</td>
      <td>23582.0775</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Eastern Europe</td>
      <td>3531.9500</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Plotting to compare regional sales totals:
sns.set_style('whitegrid')
sns.color_codes=True
fig, ax = plt.subplots()

sns.barplot(x=df_region_preview.ShipRegion, y=df_region_preview.TotalRev)

ax.set_title('Total Revenue ($) by Region', fontsize=13, fontweight='semibold')
ax.set_xlabel('')
plt.xticks(rotation=45, ha='right')

fig.savefig('TotalRevbyRegion.png', bbox_inches='tight')
plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_62_0.png)


Should we choose to explore revenue by region further in our EDA, we should likely group smaller regions such as Central Europe to a new "Europe" region in order to increase our sample population and receive results that are more likely to be statistically significant.

However, it should be noted that economy and cultural preference are likely factored into the region categorization implemented by Northwind, and may play a role in consumer purchase behavior. In a future experiment (with additional customers/sales in the "smaller" regions' dataset), Northwind could analyze the performance of these smaller regions individually.


### Shipper, Supplier Table Scrubbing:


```python
# Reviewing Shipper DataFrame:
df_shipper.head()
```

<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Id</th>
      <th>CompanyName</th>
      <th>Phone</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Speedy Express</td>
      <td>(503) 555-9831</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>United Package</td>
      <td>(503) 555-3199</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Federal Shipping</td>
      <td>(503) 555-9931</td>
    </tr>
  </tbody>
</table>
</div>


```python
# Reviewing Supplier DataFrame
df_supplier.head(3)
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Id</th>
      <th>CompanyName</th>
      <th>ContactName</th>
      <th>ContactTitle</th>
      <th>Address</th>
      <th>City</th>
      <th>Region</th>
      <th>PostalCode</th>
      <th>Country</th>
      <th>Phone</th>
      <th>Fax</th>
      <th>HomePage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Exotic Liquids</td>
      <td>Charlotte Cooper</td>
      <td>Purchasing Manager</td>
      <td>49 Gilbert St.</td>
      <td>London</td>
      <td>British Isles</td>
      <td>EC1 4SD</td>
      <td>UK</td>
      <td>(171) 555-2222</td>
      <td>None</td>
      <td>None</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>New Orleans Cajun Delights</td>
      <td>Shelley Burke</td>
      <td>Order Administrator</td>
      <td>P.O. Box 78934</td>
      <td>New Orleans</td>
      <td>North America</td>
      <td>70117</td>
      <td>USA</td>
      <td>(100) 555-4822</td>
      <td>None</td>
      <td>#CAJUN.HTM#</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Grandma Kelly's Homestead</td>
      <td>Regina Murphy</td>
      <td>Sales Representative</td>
      <td>707 Oxford Rd.</td>
      <td>Ann Arbor</td>
      <td>North America</td>
      <td>48104</td>
      <td>USA</td>
      <td>(313) 555-5735</td>
      <td>(313) 555-3349</td>
      <td>None</td>
    </tr>
  </tbody>
</table>
</div>


While the data above does allow us to examine the links and shape of Northwind's supply chain, there is no data in the dataset related to sales prices/shipping rates.

While we could examine differences in product quantities shipped or purchases made through Northwind's suppliers/shippers, these comparisons lack the context of cost.

In a future experiment, Northwind might consider adding their expenditures with partner companies to the database so that their efficiency can be evaluated with greater confidence.


# Data Exploration/Analysis:

## Do discounts have a statistically significant effect on the number of products customers order? If so, at what level(s) of discount?

### Do discounts have a statistically significant effect on the number of products customers order?
```
* $H_{0}:$ Discount has no effect on the number of products ordered.
* $H_{a}:$ Discount does have an effect on number of products ordered.
* $\alpha$ = 0.05
```

```python
# Visualizing Number of Orders and Average Order Size by hue DiscBool:
fig, axes = plt.subplots(2, 1, figsize=(12,5))
sns.set_style('whitegrid')

# Grouping DataFrame for visualization:
df_discount_basic.groupby(['DiscBool'])['Quantity'].count().plot(kind='barh', ax=axes[0], color=['#E78AC3', '#A6D854'])
df_discount_basic.groupby(['DiscBool'])['Quantity'].mean().plot(kind='barh', ax=axes[1], color=['#E78AC3', '#A6D854'])

# Subplot 1 Labels:
axes[0].set_title('Number of Orders', fontweight='bold', fontsize=13)
axes[0].set_xlabel('Orders', fontweight='semibold')
axes[0].set_ylabel('')
axes[0].set_yticklabels(['No Discount', 'Discount'], fontweight='semibold')

#Subplot 2 Labels:
axes[1].set_title('Average Order Size', fontweight='bold', fontsize=13)
axes[1].set_xlabel('Orders', fontweight='semibold')
axes[1].set_ylabel('')
axes[1].set_yticklabels(['No Discount', 'Discount'], fontweight='semibold')
fig.subplots_adjust(hspace=.75)

fig.savefig('Order_numbersandsize_discbool.png', bbox_inches='tight')
plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_79_0.png)


As seen above, while there are less orders made for discounted items, the average order size when purchasing discounted items is larger. In order to determine the effect of any discount to sales, we will determine whether the differences illustrated above are stastically significant.

*Note: This initial inquiry does not factor in item price or discount level.*


```python
# Visualizing Distribution of orders with/without discounts:
sns.set(rc={'figure.figsize':(10,5)})
sns.set_style('whitegrid')

ax = sns.distplot(df_discount_bool.Quantity, label='Discount', color='#E78AC3')
ax = sns.distplot(df_nodiscount_bool.Quantity, label='No Discount', color='#A6D854')
ax = sns.kdeplot(discount_KDE, label='Discount KDE')
ax = sns.kdeplot(no_discount_KDE, label='No Discount KDE')
ax.set_xlim(-15, 145)
ax.set_title('Distribution of Order Quantity', fontsize=14, fontweight='bold')
ax.legend(loc=1, fontsize='medium');
```


![png](Mod%202%20Project_files/Mod%202%20Project_86_0.png)



```python
# Investigating Distribution:
stats.probplot(df_discount_bool.Quantity, plot = plt), 
stats.probplot(df_nodiscount_bool.Quantity, plot = plt),
plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_87_0.png)



```python
variance_ratio(a=df_discount_bool.Quantity, b=df_nodiscount_bool.Quantity)
```




    1.4001218425014454



Based on the results above, we can see that there is some amount of positive skew present in the distribution of order sizes. However, based on the sample population sizes, we can accept the distribution as normal.

Because the variables are independent, distributed normally, but do not show homogeneity in variance, we will use a Welch's two-tailed t-test to test the assertions made by our hypothesis.

```python
# 2 tailed T:
stats.ttest_ind(df_discount_bool.Quantity, df_nodiscount_bool.Quantity, equal_var=False)
```




    Ttest_indResult(statistic=6.511219067380875, pvalue=1.0051255540843165e-10)



#### Do discounts have a statistically significant effect on the number of products customers order?
```
* $H_{0}:$ Discount has no effect on the number of products ordered.
* $H_{a}:$ Discount does have an effect on number of products ordered.
* $\alpha$ = 0.05
```

The resulting p-value of our two-tailed Welch's t-test is 1.00e$^{-10}$.

p < 0.05

**Discounts do have a statistically significant effect on the number of products customers order.**

The resulting p-value is less than our alpha value of 0.05. Therefore, we reject the null hypothesis that there is no difference between average order size for orders with and without discounts. Since we've confirmed that discounts do have a statistically significant effect on the number of products customers order, we can explore the difference in effect at each level of discount.

### If so, at what level(s) of discount?
```
* $H_{0}:$ The average number of products ordered by customers is the same regardless of discount level.
<br>
* $H_{a}:$ The average number of products ordered by customers is not the same as discount level changes.
<br>
* $\alpha$ = 0.05
```

```python
fig, axes = plt.subplots(2, 1, figsize=(12,5))
sns.set_style('whitegrid')

# Grouping DataFrame for visualization:
df_discount_basic.groupby(['Discount'])['Quantity'].count().plot(kind='barh', ax=axes[0], \
                                    color=['#E78AC3', '#A6D854', '#FFD92F', '#E5C494', '#B3B3B3', '#66C2A5'])
df_discount_basic.groupby(['Discount'])['Quantity'].mean().plot(kind='barh', ax=axes[1], \
                                    color=['#E78AC3', '#A6D854', '#FFD92F', '#E5C494', '#B3B3B3', '#66C2A5'])

# Subplot 1 Labels:
axes[0].set_title('Number of Orders per Discount Level', fontweight='bold', fontsize=13)
axes[0].set_xlabel('Orders', fontweight='semibold')
axes[0].set_ylabel('')
axes[0].set_yticklabels(['No Discount', '0.05', '0.10', '0.15', '0.20', '0.25'], fontweight='semibold')

# Subplot 2 Labels:
axes[1].set_title('Average Order Size per Discount Level', fontweight='bold', fontsize=13)
axes[1].set_xlabel('Orders', fontweight='semibold')
axes[1].set_ylabel('')
axes[1].set_yticklabels(['No Discount', '0.05', '0.10', '0.15', '0.20', '0.25'], fontweight='semibold')
fig.subplots_adjust(hspace=.75);
```


![png](Mod%202%20Project_files/Mod%202%20Project_94_0.png)



```python
sns.set(rc={'figure.figsize':(10,6)})
sns.set_style('whitegrid')

ax = sns.boxenplot(data=df_discount_basic, x=df_discount_basic.Discount, y=df_discount_basic.Quantity, \
                   hue=df_discount_basic.Discount, palette='Set2')
ax = sns.swarmplot(data=df_discount_basic, x=df_discount_basic.Discount, y=df_discount_basic.Quantity, \
                   hue=df_discount_basic.Discount, palette='Set2', alpha=.45)

ax.set_title('Discount vs. Order Quantity', fontsize=14, fontweight='bold')
ax.set_ylim(0)
ax.legend(loc=1, fontsize='small', ncol=2);
```


![png](Mod%202%20Project_files/Mod%202%20Project_95_0.png)


In contrast to our analysis in part 1a., when order quantity is visualized at each discount level we can observe a larger difference in number of orders and a smaller difference in average order size at each individual level of discount.


```python
# Performing Tukey Test to Compare Discount Levels:
tk = pairwise_tukeyhsd(df_discount_tukey.Quantity, df_discount_tukey.Discount, .05)
print(tk)
```

    Multiple Comparison of Means - Tukey HSD,FWER=0.05
    =============================================
    group1 group2 meandiff  lower   upper  reject
    ---------------------------------------------
     0.0    0.05   6.2955   2.0814 10.5097  True 
     0.0    0.1    3.5217  -0.8187  7.8622 False 
     0.0    0.15   6.6669   2.1352 11.1986  True 
     0.0    0.2    5.3096   0.8285  9.7907  True 
     0.0    0.25   6.525    1.954   11.096  True 
     0.05   0.1   -2.7738  -8.4504  2.9028 False 
     0.05   0.15   0.3714  -5.4528  6.1955 False 
     0.05   0.2    -0.986  -6.7708  4.7989 False 
     0.05   0.25   0.2294  -5.6253  6.0842 False 
     0.1    0.15   3.1452   -2.771  9.0613 False 
     0.1    0.2    1.7879  -4.0896  7.6653 False 
     0.1    0.25   3.0033   -2.943  8.9496 False 
     0.15   0.2   -1.3573  -7.3775  4.6628 False 
     0.15   0.25  -0.1419  -6.2292  5.9454 False 
     0.2    0.25   1.2154  -4.8343  7.2652 False 
    ---------------------------------------------


#### If so, at what level(s) of discount?
```
* $H_{0}:$ The number of products ordered by customers is the same regardless of discount level.
<br>
* $H_{a}:$ The number of products ordered by customers is not the same as discount level changes. 
<br>
* $\alpha$ = 0.05
```

Reviewing the results of the Tukey test, we fail to reject the null hypothesis that there is a significant difference between the control group (no discount) and the experimental group discount level 0.10. 

For discount levels of 0.05, 0.15, 0.20, and 0.25, we reject the null hypothesis, indicating that there **is** a significant difference in the number of products ordered between the control group (no discount) and experimental groups with discount levels of 0.05, 0.15, 0.20, and 0.25

**There is no statistically significant difference in number of products ordered between groups with non-zero discount level.**

While valuable, the results obtained from our first inquiry necessitate consideration of item price to give context. Before definining the parameters of our next inquiry, let's review the results obtained thusfar.

**Summation:**
<br>
There is a statistically significant difference in order quantity based on the presence of a discount at any level, but there is not a statistically significant difference between order quantity and discount level at discount level 0.10.

Great! We can safely advise our client that customers order more products when a discount is offered, but that the delta in customer order volume for each discount level is not statistically significant. However, this data doesn't tell the full story. In order to advise the client on whether (and at what level) to offer discounts, we must determine whether the increase in product orders for discounted items produces a statistically significant difference in every company's #1 metric for success, **revenue**. 

Essentially, we need to determine whether or not customers spend more when discounts are present and whether additional spend exists to offset the loss in potential revenue due to discounts. For example, if there is no statistically significant difference in customer spend regardless of whether a 5% or 25% discount is offered, we would likely advise the client to reduce the discount level of most items to 5%.

## Do discounts have a statistically significant effect on customer spend per order? If so, at what level(s) of discount?

### Do discounts have a statistically significant effect on customer spend?
```
* $H_{0}:$ Discounts do not have a statistically significant effect customer spend.
<br>
* $H_{a}:$ Discounts do have a statistically significant effect (whether positive or negative) on customer spend.
<br>
* $\alpha$ = 0.05
```

```python
df_discount_spend = pd.read_sql_query("""SELECT od.OrderId, od.UnitPrice, od.Discount, od.Quantity, \
                    od.Quantity * od.UnitPrice * (1-od.Discount) OrderPrice \
                    FROM [OrderDetail] od \
                    INNER JOIN Product p on od.ProductId = p.ID
                    GROUP BY od.OrderId;""", engine)
print(len(df_discount_spend))
df_discount_spend.head(3)
```

<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>OrderId</th>
      <th>UnitPrice</th>
      <th>Discount</th>
      <th>Quantity</th>
      <th>OrderPrice</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10248</td>
      <td>34.8</td>
      <td>0.00</td>
      <td>5</td>
      <td>174.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10249</td>
      <td>42.4</td>
      <td>0.00</td>
      <td>40</td>
      <td>1696.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10250</td>
      <td>16.8</td>
      <td>0.15</td>
      <td>15</td>
      <td>214.2</td>
    </tr>
  </tbody>
</table>
</div>


```python
# Visualizing Order Price by hue DiscBool:
sns.set_style('whitegrid')
fig, axes = plt.subplots(2, 1, figsize=(12,5))

# Grouping DataFrame for visualization:
df_discount_spend.groupby(['DiscBool'])['OrderPrice'].sum().plot(kind='barh', ax=axes[0], color=['#E78AC3', '#A6D854'])
df_discount_spend.groupby(['DiscBool'])['OrderPrice'].mean().plot(kind='barh', ax=axes[1], color=['#E78AC3', '#A6D854'])

# Subplot 1 Labels:
axes[0].set_title('Total Order Revenue', fontweight='bold', fontsize=13)
axes[0].set_xlabel('Revenue ($)', fontweight='semibold')
axes[0].set_ylabel('')
axes[0].set_yticklabels(['No Discount', 'Discount'])

#Subplot 2 Labels:
axes[1].set_title('Average Order Cost', fontweight='bold', fontsize=13)
axes[1].set_xlabel('Revenue ($)', fontweight='semibold')
axes[1].set_ylabel('')
axes[1].set_yticklabels(['No Discount', 'Discount'])
fig.subplots_adjust(hspace=.75)

fig.savefig('Avg_and_tot_order_rev.png', bbox_inches='tight')
plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_110_0.png)


Based on the visualization above, we can determine that Northwind generates more total revenue from sales of non-discounted items. However, the average order cost for orders containing discounts is greater than those without discounts. To determine whether this difference is statistically significant, we should first review the distribution of Order Price for orders with and without discount(s).


```python
# Visualizing order price frequency by hue Discount:
sns.set_style('whitegrid')
fig, ax = plt.subplots(figsize=(7,10.5))

# Plotting boxenplot and swarm plot on the same axis:
ax = sns.boxenplot(data=df_discount_spend, x=df_discount_spend.DiscBool, y=df_discount_spend.OrderPrice, palette='Set2')
ax = sns.swarmplot(data=df_discount_spend, x=df_discount_spend.DiscBool, y=df_discount_spend.OrderPrice, hue=df_discount_spend.Discount, palette='Set2', alpha=.75)

# Labeling:
ax.set_title('Discount vs. Order Revenue', fontsize=14, fontweight='bold')
ax.set_ylabel('Order Price', fontweight='semibold')
ax.set_ylim(0, 5500)
ax.set_xlabel('')
ax.set_xticklabels(['No Discount', 'Discount'], fontweight='semibold')

fig.savefig('DiscountvsOrderRevBoxen.png', bbox_inches='tight')
plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_117_0.png)



```python
# Visualizing Distribution of Order Price with/without discounts:
sns.set_style('whitegrid')
fig, ax = plt.subplots(figsize=(8,5))

# Plotting the distribution in order price with/without a discount:
ax = sns.distplot(df_discount_spend_disc.OrderPrice, label='Discount', color='#E78AC3', bins=100,
                 hist_kws={"linewidth": 2, "alpha": .6})
ax = sns.distplot(df_discount_spend_nodisc.OrderPrice, label='No Discount', color='#A6D854', bins=100,
                  hist_kws={"linewidth": 2, "alpha": .3})

# Plotting vlines for sample means:
plt.axvline(np.mean(df_discount_spend_nodisc.OrderPrice), color='#E78AC3', label='Discount Mean')
plt.axvline(np.mean(df_discount_spend_disc.OrderPrice), color='#A6D854', label='No Discount Mean')


# Labeling/Formatting:
ax.set_xlim(-2, 4500)
#ax.set_ylim(0, .00005)
ax.set_title('Distribution of Order Revenue', fontsize=14, fontweight='bold')
ax.set_xlabel('Order Revenue', fontweight='semibold')
ax.legend(loc=1, fontsize='medium');
```


![png](Mod%202%20Project_files/Mod%202%20Project_118_0.png)


The visualization of Distribution of Order Revenue is positively skewed. Before determining the test we'll use to test our hypothesis, let's first visualize and compare the shapes of the distributions above in separate plots.


```python
# Visualizing Distribution of Order Price with/without discounts:
sns.set_style('whitegrid')
fig, axes = plt.subplots(1,2, figsize=(12,5))

# Plotting the same distributions above on separate axes:
sns.distplot(df_discount_spend_disc.OrderPrice, label='Discount', color='#E78AC3', bins=120, ax=axes[0], 
                 hist_kws={"linewidth": 2, "alpha": .6})
sns.distplot(df_discount_spend_nodisc.OrderPrice, label='No Discount', color='#A6D854', bins=200, 
                  hist_kws={"linewidth": 2, "alpha": .3})

# Subplot 1 Labels:
axes[0].set_title('Distribution of Order Revenue (Discount)', fontweight='bold', fontsize=13)
axes[0].set_xlabel('Revenue ($)', fontweight='semibold')
axes[0].set_ylabel('')
axes[0].set_xlim(-2, 3500)

#Subplot 2 Labels:
axes[1].set_title('Distribution of Order Revenue (No Discount)', fontweight='bold', fontsize=13)
axes[1].set_xlabel('Revenue ($)', fontweight='semibold')
axes[1].set_ylabel('')
axes[1].set_xlim(-2, 3500)

fig.subplots_adjust(wspace=.22)

fig.savefig('OrderRev_Distribution_hist_bool.png', bbox_inches='tight')
plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_120_0.png)



```python
# Visualizing Distribution:
fig, ax = plt.subplots()

stats.probplot(df_discount_spend_disc.OrderPrice, plot = plt), 
stats.probplot(df_discount_spend_nodisc.OrderPrice, plot = plt),

ax.set_title('QQ Plot - Order Revenue Distribution', fontsize=13, fontweight='semibold')

plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_121_0.png)


A visual examination of the distribution of Order Revenue suggests that we should transform the data to fit a normal distribution before modeling and hypothesis testing.


```python
# Repeating the visualizations above using new column 'LogOrderPrice':
sns.set_style('whitegrid')
fig, axes = plt.subplots(1,2, figsize=(12,5), sharey=False)

sns.distplot(df_log_discount_spend_disc.LogOrderPrice, label='Discount', color='#E78AC3', bins=30, ax=axes[0], 
                 hist_kws={"linewidth": 2, "alpha": .6})
sns.distplot(df_log_discount_spend_nodisc.LogOrderPrice, label='No Discount', color='#A6D854', bins=30, ax=axes[1], 
                  hist_kws={"linewidth": 2, "alpha": .3})

# Subplot 1 Labels:
axes[0].set_title('Order Revenue (Discount)', fontweight='bold', fontsize=13)
axes[0].set_xlabel('Log Revenue', fontweight='semibold')
axes[0].set_ylabel('')
#axes[0].set_xlim(-1000, 30000)

#Subplot 2 Labels:
axes[1].set_title('Order Revenue (No Discount)', fontweight='bold', fontsize=13)
axes[1].set_xlabel('Log Revenue', fontweight='semibold')
axes[1].set_ylabel('')
#axes[1].set_xlim(-1000, 30000)
fig.subplots_adjust(wspace=.13)

fig.savefig('Log_OrderRev_Distribution_hist_bool.png', bbox_inches='tight')
plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_128_0.png)



```python
# Investigating Normality:
fig,ax = plt.subplots()

stats.probplot(df_log_discount_spend_disc.LogOrderPrice, plot = plt), 
stats.probplot(df_log_discount_spend_nodisc.LogOrderPrice, plot = plt),
ax.set_title('QQ Plot - Log-Scaled Order Revenue Distribution', fontsize=13, fontweight='semibold')

fig.savefig('QQ Plot - Log-Scaled Order Revenue Distribution.png', bbox_inches='tight')

plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_129_0.png)



```python
# Investigating variance homogeneity:
variance_ratio(a=df_log_discount_spend_disc.LogOrderPrice, b=df_log_discount_spend_nodisc.LogOrderPrice)
```




    1.0836127695528326



Great! Log transforming Order Revenue has normalized the data for both distributions. As our variables are independent, distributions of our variables is normal, and the variances are at an acceptable level of homogeneity, we can determine whether the presence of a discount has a statistically significant effect on Order Price using an independent two-tailed t-test.


```python
# Using a two-tailed t-test to compare distributions above:
stats.ttest_ind(df_log_discount_spend_disc.LogOrderPrice, df_log_discount_spend_nodisc.LogOrderPrice)
```




    Ttest_indResult(statistic=1.829039466630245, pvalue=0.06775322267188763)



#### Do discounts have a statistically significant effect on average customer spend (order price)?

```
* $H_{0}:$ Discounts do not have a statistically significant effect on average customer spend.
<br>
* $H_{a}:$ Discounts do have a statistically significant effect (whether positive or negative) on average customer spend.
<br>
* $\alpha$ = 0.05
```

The resulting p-value of our two-tailed t-test is 0.07.

Because p > 0.05 we fail to reject the null hypothesis that states that discount (as a boolean) does not have a statistically significant effect on customer spend.

### At what level(s) of discount is there a significant effect on average customer spend?
```
* $H_{0}:$ Customer order price is the same regardless of discount level.
<br>
* $H_{a}:$ Customer order price is not the same as discount level changes.
<br>
* $\alpha$ = 0.05
```

```python
# Visualizing Order Prices by hue Discount Level:
sns.set_style('whitegrid')
fig, ax = plt.subplots(figsize=(10,6))

ax = sns.boxenplot(data=df_log_discount_spend, x=df_discount_spend.Discount, y=df_discount_spend.OrderPrice, palette='Set2')
ax = sns.swarmplot(data=df_log_discount_spend, x=df_discount_spend.Discount, y=df_discount_spend.OrderPrice, hue=df_discount_spend.Discount, palette='Set2', alpha=.75)

ax.set_title('Discount Level vs. Order Revenue', fontsize=14, fontweight='bold')
ax.set_ylabel('Order Price', fontweight='semibold')
ax.set_ylim(0,6250)
ax.set_xlabel('Discount Level')
ax.set_xticklabels(['No Discount', '0.05', '0.10', '0.15', '0.20', '0.25'], fontweight='semibold')
ax.legend(loc=1, fontsize='medium');
```


![png](Mod%202%20Project_files/Mod%202%20Project_136_0.png)



```python
# Visualizing Distribution of Order Price with/without discounts:
sns.set_style('whitegrid')
fig, ax = plt.subplots(figsize=(10,6))

# Plotting Overlapping distplots for each discount level:
ax = sns.distplot(df_log_ds_nodisc.LogOrderPrice, label='No Discount', color='#E78AC3', bins=30,
                 hist_kws={"linewidth": 2, "alpha":.4})
ax = sns.distplot(df_log_ds_5p.LogOrderPrice, label='5% Discount', color='#A6D854', bins=30,
                  hist_kws={"linewidth": 2, "alpha": .4})
ax = sns.distplot(df_log_ds_10p.LogOrderPrice, label='10% Discount', color='#FFD92F', bins=30,
                  hist_kws={"linewidth": 2, "alpha": .4})
ax = sns.distplot(df_log_ds_15p.LogOrderPrice, label='15% Discount', color='#E5C494', bins=30,
                  hist_kws={"linewidth": 2, "alpha": .4})
ax = sns.distplot(df_log_ds_20p.LogOrderPrice, label='20% Discount', color='#B3B3B3', bins=30,
                  hist_kws={"linewidth": 2, "alpha": .4})
ax = sns.distplot(df_log_ds_25p.LogOrderPrice, label='25% Discount', color='#66C2A5', bins=30,
                  hist_kws={"linewidth": 2, "alpha": .4})

# Adding labels/legend:
ax.set_xlabel('Log Order Price', fontweight='semibold')
ax.set_title('Distribution of Order Price by Discount', fontsize=14, fontweight='bold')
fig.subplots_adjust(top=.85, bottom=.15);
ax.legend(loc=1, fontsize='medium');
```


![png](Mod%202%20Project_files/Mod%202%20Project_139_0.png)



```python
# Investigating Normality:
stats.probplot(df_log_ds_nodisc.LogOrderPrice, plot = plt), 
stats.probplot(df_log_ds_5p.LogOrderPrice, plot = plt),
stats.probplot(df_log_ds_10p.LogOrderPrice, plot = plt),
stats.probplot(df_log_ds_15p.LogOrderPrice, plot = plt),
stats.probplot(df_log_ds_20p.LogOrderPrice, plot = plt),
stats.probplot(df_log_ds_25p.LogOrderPrice, plot = plt)
plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_140_0.png)


Distribution of Order price appears normal across each discount level. We can now fit the data above to a model and use ANOVA/Tukey tests to measure variance between sample populations.


```python
# Printing OLS Regression Results:
lm.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>      <td>LogOrderPrice</td>  <th>  R-squared:         </th> <td>   0.019</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.013</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   3.147</td>
</tr>
<tr>
  <th>Date:</th>             <td>Mon, 24 Jun 2019</td> <th>  Prob (F-statistic):</th>  <td>0.00804</td>
</tr>
<tr>
  <th>Time:</th>                 <td>17:53:59</td>     <th>  Log-Likelihood:    </th> <td> -1258.4</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   830</td>      <th>  AIC:               </th> <td>   2529.</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   824</td>      <th>  BIC:               </th> <td>   2557.</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     5</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
           <td></td>              <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>           <td>    5.6285</td> <td>    0.048</td> <td>  116.259</td> <td> 0.000</td> <td>    5.533</td> <td>    5.724</td>
</tr>
<tr>
  <th>C(Discount)[T.0.05]</th> <td>    0.5477</td> <td>    0.141</td> <td>    3.890</td> <td> 0.000</td> <td>    0.271</td> <td>    0.824</td>
</tr>
<tr>
  <th>C(Discount)[T.0.1]</th>  <td>   -0.0004</td> <td>    0.154</td> <td>   -0.003</td> <td> 0.998</td> <td>   -0.303</td> <td>    0.302</td>
</tr>
<tr>
  <th>C(Discount)[T.0.15]</th> <td>    0.0394</td> <td>    0.145</td> <td>    0.271</td> <td> 0.786</td> <td>   -0.246</td> <td>    0.325</td>
</tr>
<tr>
  <th>C(Discount)[T.0.2]</th>  <td>   -0.0243</td> <td>    0.151</td> <td>   -0.161</td> <td> 0.872</td> <td>   -0.320</td> <td>    0.272</td>
</tr>
<tr>
  <th>C(Discount)[T.0.25]</th> <td>    0.0996</td> <td>    0.156</td> <td>    0.640</td> <td> 0.522</td> <td>   -0.206</td> <td>    0.405</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 0.238</td> <th>  Durbin-Watson:     </th> <td>   2.080</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.888</td> <th>  Jarque-Bera (JB):  </th> <td>   0.169</td>
</tr>
<tr>
  <th>Skew:</th>          <td>-0.030</td> <th>  Prob(JB):          </th> <td>   0.919</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 3.035</td> <th>  Cond. No.          </th> <td>    4.79</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.




```python
# ANOVA test:
sm.stats.anova_lm(lm, typ=2)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>sum_sq</th>
      <th>df</th>
      <th>F</th>
      <th>PR(&gt;F)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>C(Discount)</th>
      <td>19.250021</td>
      <td>5.0</td>
      <td>3.146751</td>
      <td>0.008038</td>
    </tr>
    <tr>
      <th>Residual</th>
      <td>1008.152010</td>
      <td>824.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



```python
# Performing Tukey Test for Log Revenue by Discount Level:
tk2 = pairwise_tukeyhsd(df_log_discount_spend.LogOrderPrice, df_log_discount_spend.Discount, .05)
print(tk2)
```

    Multiple Comparison of Means - Tukey HSD,FWER=0.05
    =============================================
    group1 group2 meandiff  lower   upper  reject
    ---------------------------------------------
     0.0    0.05   0.5477   0.1455  0.9499  True 
     0.0    0.1   -0.0004  -0.4412  0.4403 False 
     0.0    0.15   0.0394  -0.3761  0.455  False 
     0.0    0.2   -0.0243   -0.455  0.4064 False 
     0.0    0.25   0.0996  -0.3447  0.5438 False 
     0.05   0.1   -0.5481  -1.1118  0.0156 False 
     0.05   0.15  -0.5082  -1.0525  0.036  False 
     0.05   0.2    -0.572  -1.1278 -0.0161  True 
     0.05   0.25  -0.4481  -1.0146  0.1183 False 
     0.1    0.15   0.0399  -0.5335  0.6132 False 
     0.1    0.2   -0.0239  -0.6083  0.5605 False 
     0.1    0.25    0.1    -0.4945  0.6944 False 
     0.15   0.2   -0.0637  -0.6294  0.5019 False 
     0.15   0.25   0.0601  -0.5159  0.6362 False 
     0.2    0.25   0.1239  -0.4632  0.7109 False 
    ---------------------------------------------



## Do discounts have a statistically significant effect on Order Quantity by Category? If so, at what level(s) of discount?
*Note:  Product categories include 'Beverages', 'Condiments', 'Confections', 'Dairy Products', 'Grains/Cereals', 'Meat/Poultry', 'Produce', and 'Seafood'. 

### Do discounts have a statistically significant effect on the number of products customers order by category?
```
* $H_{0}:$ Discounts do not have a statistically significant effect on number of products customers order by category.
<br>
* $H_{a}:$ Discounts either increase or decrease the number of products customers order by category.
<br>
* $\alpha$ = 0.05
```

```python
df_discount_category_spend = pd.read_sql_query("""SELECT p.Id, c.CategoryName, p.ProductName, od.Discount, od.Quantity, p.QuantityPerUnit \
            FROM [Product] p \
            INNER JOIN [OrderDetail] od on od.ProductId = p.Id
            INNER JOIN [Category] c on c.Id = p.CategoryId""", engine)
df_discount_category_spend.head(3)
```

<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Id</th>
      <th>CategoryName</th>
      <th>ProductName</th>
      <th>Discount</th>
      <th>Quantity</th>
      <th>QuantityPerUnit</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>11</td>
      <td>Dairy Products</td>
      <td>Queso Cabrales</td>
      <td>0.0</td>
      <td>12</td>
      <td>1 kg pkg.</td>
    </tr>
    <tr>
      <th>1</th>
      <td>42</td>
      <td>Grains/Cereals</td>
      <td>Singaporean Hokkien Fried Mee</td>
      <td>0.0</td>
      <td>10</td>
      <td>32 - 1 kg pkgs.</td>
    </tr>
    <tr>
      <th>2</th>
      <td>72</td>
      <td>Dairy Products</td>
      <td>Mozzarella di Giovanni</td>
      <td>0.0</td>
      <td>5</td>
      <td>24 - 200 g pkgs.</td>
    </tr>
  </tbody>
</table>
</div>


```python
# Visualizing Distribution of Order Quantity for each Category Name:
sns.set_style('whitegrid')
fig, ax = plt.subplots(figsize=(10,7.5))

# Plotting boxenplot and swarm plot on the same axis:
ax = sns.boxenplot(data=df_discount_category_spend, x=df_discount_category_spend.CategoryName, y=df_discount_category_spend.Quantity, palette='Set2')
ax = sns.swarmplot(data=df_discount_category_spend, x=df_discount_category_spend.CategoryName, y=df_discount_category_spend.Quantity, hue=df_discount_category_spend.DiscBool, palette='Set2', alpha=.75)

# Labeling:
ax.set_title('Discount vs. Order Quantity', fontsize=14, fontweight='bold')
ax.set_ylabel('Order Quantity', fontweight='semibold')
ax.set_ylim(0)
ax.set_xlabel('')

fig.savefig('DiscVsOrderQuant.png', bbox_inches='tight')

plt.xticks(rotation=45, ha='right')
plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_159_0.png)



```python
# Visualizing Distribution of Product Order Size with/without discounts:
sns.set_style('whitegrid')

# Plotting distributions:
ax = sns.distplot(df_discount_category_spend_disc.Quantity, label='Discount', color='#E78AC3')
ax = sns.distplot(df_discount_category_spend_nodisc.Quantity, label='No Discount', color='#A6D854')

# Label/Format:
ax.set_xlim(-2)
ax.set_title('Distribution of Order Quantity', fontsize=14, fontweight='bold')
ax.legend(loc=1, fontsize='medium');
```


![png](Mod%202%20Project_files/Mod%202%20Project_160_0.png)



```python
stats.probplot(df_discount_category_spend_disc.Quantity, plot = plt),
stats.probplot(df_discount_category_spend_nodisc.Quantity, plot = plt),
plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_161_0.png)


Because we're seeking to determine whether the means of two independent groups are significantly different, we'd like to conduct an Independent two tailed t-test.  However, we must first ensure that the populations meet the assumptions of normality and homogeneity of variance required. 

As seen above, the distribution for Order Quantities with and without a Discount are positively skewed. In order to conduct a two-tailed t-test, we should first normalize both distributions.


```python
# Visualizing Number of Orders and Log Number of Orders by DiscBool:
sns.set_style('whitegrid')
fig, axes = plt.subplots(1, 2, figsize=(12,6))
ax.legend()

# Subplot 1:
df_discount_category_spend.groupby(['CategoryName', 'DiscBool'])['Quantity'].hist(ax=axes[0], alpha=.25)
#df_discount_category_spend_nodisc.groupby(['CategoryName'])['Quantity'].sum().plot(kind='barh', ax=axes[0], alpha=.3, color=['blue'])

# Subplot 2:
df_log_discount_category_spend.groupby(['CategoryName', 'DiscBool'])['LogOrderQuantity'].hist(ax=axes[1], alpha=.25)
#df_discount_category_spend_nodisc.groupby(['CategoryName'])['Quantity'].mean().plot(kind='barh', ax=axes[1], alpha=.3, color=['blue'])

# Subplot 1 Labels:
axes[0].set_title('Order Quantity Distribution', fontweight='bold', fontsize=13)
axes[0].set_xlabel('Order Quantity', fontweight='semibold')
axes[0].set_ylabel('')
#axes[0].set_yticklabels(['No Discount', 'Discount'])

#Subplot 2 Labels:
axes[1].set_title('Log Order Quantity Distribution', fontweight='bold', fontsize=13)
axes[1].set_xlabel('Log Order Quantity', fontweight='semibold')
axes[1].set_ylabel('')
#axes[1].set_yticklabels(['No Discount', 'Discount'])
fig.subplots_adjust(hspace=.35);
```


![png](Mod%202%20Project_files/Mod%202%20Project_164_0.png)


As seen above, log scaling the values for Order Quantity increased the distributions' normality. Next, we'll compare sample population variances and create a QQ plot of the distribution shape for Order Quantity with and without a discount.  

```python
variance_ratio(a=df_log_discount_category_spend_disc.LogOrderQuantity, b=df_log_discount_category_spend_nodisc.LogOrderQuantity)
```




    0.9030998215987438




```python
stats.probplot(df_log_discount_category_spend_disc.LogOrderQuantity, plot = plt),
stats.probplot(df_log_discount_category_spend_nodisc.LogOrderQuantity, plot = plt),
plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_168_0.png)


Great! With confirmation of normal distribution and homogeneity of variance in each population, the data meets the assumptions required to conduct an independent t-test.


```python
# Using a two-tailed t-test to compare distributions above:
stats.ttest_ind(df_log_discount_category_spend_disc.LogOrderQuantity, df_log_discount_category_spend_nodisc.LogOrderQuantity)
```




    Ttest_indResult(statistic=6.920700463008245, pvalue=5.91313388186685e-12)



Because p < 0.05, we reject the null hypothesis that there is no significant difference in Order quantity for orders with and without a discount.


```python
# Visualizing Distribution of Product Order Size with/without discounts:
sns.set_style('whitegrid')

# Creating Gridspec:
plt.figure(figsize=(10,10))
ax1 = plt.subplot2grid((4, 2), (0, 0))
ax2 = plt.subplot2grid((4, 2), (1, 0))
ax3 = plt.subplot2grid((4, 2), (2, 0))
ax4 = plt.subplot2grid((4, 2), (3, 0))
ax5 = plt.subplot2grid((4, 2), (0, 1))
ax6 = plt.subplot2grid((4, 2), (1, 1))
ax7 = plt.subplot2grid((4, 2), (2, 1))
ax8 = plt.subplot2grid((4, 2), (3, 1))

        

# Subplot 1:
ax1.hist(df_discount_category_spend_dairy_disc.Quantity, label='Discount', color='#E78AC3', alpha=.5, bins=30, density=True)
ax1.hist(df_discount_category_spend_dairy_nodisc.Quantity, label='No Discount', color='#A6D854', alpha=.5, bins=30, density=True)
ax1.set_title('Distribution of Order Quantity: Dairy', fontweight='bold')
#axes[0,0].legend(loc=1, fontsize='medium');

# Subplot 2:
ax2.hist(df_discount_category_spend_grain_disc.Quantity, label='Discount', color='#E78AC3', alpha=.5, bins=30, density=True)
ax2.hist(df_discount_category_spend_grain_nodisc.Quantity, label='No Discount', color='#A6D854', alpha=.5, bins=30, density=True)
ax2.set_title("Distribution of Order Quantity: Grains", fontsize=12, fontweight='bold')

# Subplot 3:
ax3.hist(df_discount_category_spend_produce_disc.Quantity, label='Discount', color='#E78AC3', alpha=.5, bins=30, density=True)
ax3.hist(df_discount_category_spend_produce_nodisc.Quantity, label='No Discount', color='#A6D854', alpha=.5, bins=30, density=True)
ax3.set_title("Distribution of Order Quantity: Produce", fontsize=12, fontweight='bold')

# Subplot 4:
ax4.hist(df_discount_category_spend_seafood_disc.Quantity, label='Discount', color='#E78AC3', alpha=.5, bins=30, density=True)
ax4.hist(df_discount_category_spend_seafood_nodisc.Quantity, label='No Discount', color='#A6D854', alpha=.5, bins=30, density=True)
ax4.set_title("Distribution of Order Quantity: Seafood", fontsize=12, fontweight='bold')

# Subplot 5:
ax5.hist(df_discount_category_spend_condiments_disc.Quantity, label='Discount', color='#E78AC3', alpha=.5, bins=30, density=True)
ax5.hist(df_discount_category_spend_condiments_nodisc.Quantity, label='No Discount', color='#A6D854', alpha=.5, bins=30, density=True)
ax5.set_title("Distribution of Order Quantity: Condiments", fontsize=12, fontweight='bold')

# Subplot 6:
ax6.hist(df_discount_category_spend_confections_disc.Quantity, label='Discount', color='#E78AC3', alpha=.5, bins=30, density=True)
ax6.hist(df_discount_category_spend_confections_nodisc.Quantity, label='No Discount', color='#A6D854', alpha=.5, bins=30, density=True)
ax6.set_title("Distribution of Order Quantity: Confections", fontsize=12, fontweight='bold')

# Subplot 7:
ax7.hist(df_discount_category_spend_bev_disc.Quantity, label='Discount', color='#E78AC3', alpha=.5, bins=30, density=True)
ax7.hist(df_discount_category_spend_bev_nodisc.Quantity, label='No Discount', color='#A6D854', alpha=.5, bins=30, density=True)
ax7.set_title("Distribution of Order Quantity: Beverages", fontsize=12, fontweight='bold')

# Subplot 8:
ax8.hist(df_discount_category_spend_meat_disc.Quantity, label='Discount', color='#E78AC3', alpha=.5, bins=30, density=True)
ax8.hist(df_discount_category_spend_meat_nodisc.Quantity, label='No Discount', color='#A6D854', alpha=.5, bins=30, density=True)
ax8.set_title("Distribution of Order Quantity: Meat/Poultry", fontsize=12, fontweight='bold')
plt.tight_layout()
```


![png](Mod%202%20Project_files/Mod%202%20Project_174_0.png)



```python
# Visualizing Distribution of Order Quantity with/without discounts:
sns.set_style('whitegrid')
fig, ax = plt.subplots(figsize=(10,5))

# Plotting the distribution in order quantity with/without a discount:
ax = sns.distplot(df_discount_category_spend_dairy_disc.Quantity, label='Dairy Discount', color='#E78AC3', bins=150,
                 hist_kws={"linewidth": 2, "alpha": .6})
ax = sns.distplot(df_discount_category_spend_dairy_nodisc.Quantity, label='Dairy No Discount', color='#A6D854', bins=150,
                  hist_kws={"linewidth": 2, "alpha": .3})

ax = sns.distplot(df_discount_category_spend_grain_disc.Quantity, label='Grains Discount', color='#E78AC3', bins=150,
                 hist_kws={"linewidth": 2, "alpha": .6})
ax = sns.distplot(df_discount_category_spend_grain_nodisc.Quantity, label='Grains No Discount', color='#A6D854', bins=150,
                  hist_kws={"linewidth": 2, "alpha": .3})

ax = sns.distplot(df_discount_category_spend_produce_disc.Quantity, label='Produce Discount', color='#E78AC3', bins=150,
                 hist_kws={"linewidth": 2, "alpha": .6})
ax = sns.distplot(df_discount_category_spend_produce_nodisc.Quantity, label='Produce No Discount', color='#A6D854', bins=150,
                  hist_kws={"linewidth": 2, "alpha": .3})

ax = sns.distplot(df_discount_category_spend_seafood_disc.Quantity, label='Seafood Discount', color='#E78AC3', bins=150,
                 hist_kws={"linewidth": 2, "alpha": .6})
ax = sns.distplot(df_discount_category_spend_seafood_nodisc.Quantity, label='Seafood No Discount', color='#A6D854', bins=150,
                  hist_kws={"linewidth": 2, "alpha": .3})

ax = sns.distplot(df_discount_category_spend_condiments_disc.Quantity, label='Condiments Discount', color='#E78AC3', bins=150,
                 hist_kws={"linewidth": 2, "alpha": .6})
ax = sns.distplot(df_discount_category_spend_condiments_nodisc.Quantity, label='Condiments No Discount', color='#A6D854', bins=150,
                  hist_kws={"linewidth": 2, "alpha": .3})

ax = sns.distplot(df_discount_category_spend_confections_disc.Quantity, label='Confections Discount', color='#E78AC3', bins=150,
                 hist_kws={"linewidth": 2, "alpha": .6})
ax = sns.distplot(df_discount_category_spend_confections_nodisc.Quantity, label='Confections No Discount', color='#A6D854', bins=150,
                  hist_kws={"linewidth": 2, "alpha": .3})

ax = sns.distplot(df_discount_category_spend_bev_disc.Quantity, label='Beverages Discount', color='#E78AC3', bins=150,
                 hist_kws={"linewidth": 2, "alpha": .6})
ax = sns.distplot(df_discount_category_spend_bev_nodisc.Quantity, label='Beverages No Discount', color='#A6D854', bins=150,
                  hist_kws={"linewidth": 2, "alpha": .3})

ax = sns.distplot(df_discount_category_spend_meat_disc.Quantity, label='Meat/Poultry Discount', color='#E78AC3', bins=150,
                 hist_kws={"linewidth": 2, "alpha": .6})
ax = sns.distplot(df_discount_category_spend_meat_nodisc.Quantity, label='Meat/Poultry No Discount', color='#A6D854', bins=150,
                  hist_kws={"linewidth": 2, "alpha": .3})

# Labeling/Formatting:
ax.set_xlim(-10, 65)
ax.set_ylim(0, .26)
ax.set_title('Distribution of Order Quantity by Category', fontsize=14, fontweight='bold')
ax.set_xlabel('Order Quantity')
ax.legend(loc=(1.05,.5), fontsize='medium', ncol=2);
```


![png](Mod%202%20Project_files/Mod%202%20Project_175_0.png)



```python
tk3 = pairwise_tukeyhsd(df_log_discount_category_spend.Quantity, df_log_discount_category_spend.CategoryName, .05)
print(tk3)
```

         Multiple Comparison of Means - Tukey HSD,FWER=0.05     
    ============================================================
        group1         group2     meandiff  lower  upper  reject
    ------------------------------------------------------------
      Beverages      Condiments    1.0431  -3.8296 5.9159 False 
      Beverages     Confections    0.2102  -4.0656 4.4859 False 
      Beverages    Dairy Products  1.4662  -2.7021 5.6346 False 
      Beverages    Grains/Cereals -0.2094  -5.2426 4.8237 False 
      Beverages     Meat/Poultry   0.6776   -4.567 5.9222 False 
      Beverages       Produce     -1.4533  -7.1915 4.2849 False 
      Beverages       Seafood     -0.1916  -4.4817 4.0985 False 
      Condiments    Confections    -0.833  -5.8859 4.2199 False 
      Condiments   Dairy Products  0.4231  -4.5393 5.3854 False 
      Condiments   Grains/Cereals -1.2526  -6.9607 4.4555 False 
      Condiments    Meat/Poultry  -0.3655  -6.2609 5.5298 False 
      Condiments      Produce     -2.4965  -8.8349 3.842  False 
      Condiments      Seafood     -1.2348  -6.2998 3.8302 False 
     Confections   Dairy Products  1.2561  -3.1216 5.6337 False 
     Confections   Grains/Cereals -0.4196  -5.6274 4.7882 False 
     Confections    Meat/Poultry   0.4675  -4.9449 5.8799 False 
     Confections      Produce     -1.6635  -7.5554 4.2285 False 
     Confections      Seafood     -0.4018  -4.8955 4.0919 False 
    Dairy Products Grains/Cereals -1.6757  -6.7956 3.4443 False 
    Dairy Products  Meat/Poultry  -0.7886  -6.1165 4.5393 False 
    Dairy Products    Produce     -2.9195   -8.734 2.8949 False 
    Dairy Products    Seafood     -1.6578  -6.0494 2.7337 False 
    Grains/Cereals  Meat/Poultry   0.8871  -5.1416 6.9157 False 
    Grains/Cereals    Produce     -1.2439  -7.7065 5.2188 False 
    Grains/Cereals    Seafood      0.0178  -5.2017 5.2374 False 
     Meat/Poultry     Produce     -2.1309  -8.7595 4.4977 False 
     Meat/Poultry     Seafood     -0.8692  -6.2929 4.5545 False 
       Produce        Seafood      1.2617  -4.6406 7.164  False 
    ------------------------------------------------------------


## Is there a statistically significant difference in performance of Sales Employees at Northwind?

### Is total all-time revenue generated equal across all employees?
```
* $H_{0}:$ There is no difference between all-time revenue generated across all employees.
<br>
* $H_{a}:$ There is a difference between all-time revenue generated across all employees.
<br>
* $\alpha$ = 0.05
```

```python
df_employee_sales_overview = pd.read_sql_query("""SELECT e.Id, e.LastName, e.Title, e.HireDate, \
            SUM(od.Quantity * od.UnitPrice * (1-od.Discount)) TotalRev\
            FROM [OrderDetail] od \
            INNER JOIN [Order] o on o.Id = od.OrderId \
            INNER JOIN [Employee] e on e.Id = o.EmployeeId \
            GROUP BY e.LastName \
            ORDER BY e.LastName DESC""", engine)
print(len(df_employee_sales_overview))
df_employee_sales_overview.head(50)
```
    9





<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Id</th>
      <th>LastName</th>
      <th>Title</th>
      <th>HireDate</th>
      <th>TotalRev</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6</td>
      <td>Suyama</td>
      <td>Sales Representative</td>
      <td>2025-10-17</td>
      <td>73913.1295</td>
    </tr>
    <tr>
      <th>1</th>
      <td>4</td>
      <td>Peacock</td>
      <td>Sales Representative</td>
      <td>2025-05-03</td>
      <td>232890.8460</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Leverling</td>
      <td>Sales Representative</td>
      <td>2024-04-01</td>
      <td>202812.8430</td>
    </tr>
    <tr>
      <th>3</th>
      <td>7</td>
      <td>King</td>
      <td>Sales Representative</td>
      <td>2026-01-02</td>
      <td>124568.2350</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>Fuller</td>
      <td>Vice President, Sales</td>
      <td>2024-08-14</td>
      <td>166537.7550</td>
    </tr>
    <tr>
      <th>5</th>
      <td>9</td>
      <td>Dodsworth</td>
      <td>Sales Representative</td>
      <td>2026-11-15</td>
      <td>77308.0665</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1</td>
      <td>Davolio</td>
      <td>Sales Representative</td>
      <td>2024-05-01</td>
      <td>192107.6045</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8</td>
      <td>Callahan</td>
      <td>Inside Sales Coordinator</td>
      <td>2026-03-05</td>
      <td>126862.2775</td>
    </tr>
    <tr>
      <th>8</th>
      <td>5</td>
      <td>Buchanan</td>
      <td>Sales Manager</td>
      <td>2025-10-17</td>
      <td>68792.2825</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Plotting to compare sales totals by Employee:
sns.set_style('whitegrid')
sns.set_color_codes='Set2'

fig, ax = plt.subplots()

sns.barplot(x=df_employee_sales_overview.LastName, y=df_employee_sales_overview.TotalRev)

ax.set_title('Total Revenue by Employee - All Time', fontsize=13, fontweight='semibold')
ax.set_xlabel('')
ax.set_ylabel('Total Revenue ($)', fontweight='semibold')
plt.xticks(rotation=45, ha='right', fontweight='semibold')

fig.savefig('TotalRevenueByEmp.png', bbox_inches='tight')
plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_184_0.png)


Ranking the employees in the Sales Team by revenue generated (TotalRev) is only a sigificant measure given the context of how long each employee has worked for Northwind. However, as seen above, the hire date for all employees does not match the period from 2012-2014 in which this data was recorded.

For this reason, it's likely more productive to view differences in 2014 YTD revenue generated for each employee and YoY revenue generated by Northwind as a whole. Also worth mentioning is that there are 3 basic "levels" of employee seniority (Sales Rep, Manager, VP) included. Because we do not have information regarding employee salary as well as other KPIs these employees may be accountable for, a direct comparison of revenue generated is likely not fully representitive of employee performance. 


```python
fig, ax = plt.subplots(figsize=(6.5,10))
sns.set_style('whitegrid')
sns.set_palette('Set2')

df_employee_sales.groupby('LastName')['TotalRev'].plot(kind='hist', alpha=.25, bins=30, ax=ax, density=True)

ax.set_title('Distribution in Order Revenue by Employee')
ax.set_xlabel('Order Revenue')
ax.set_xlim(-2, 4000)
ax.legend(ncol=1, fontsize='small');
```


![png](Mod%202%20Project_files/Mod%202%20Project_187_0.png)



```python
# Visualizing Distribution:
stats.probplot(df_employee_sales.TotalRev, plot = plt),
plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_188_0.png)



```python
fig, ax = plt.subplots(figsize=(6.5,10))
sns.set_style('whitegrid')
sns.set_palette('Set2')

df_log_employee_sales.groupby('LastName')['LogTotalRev'].plot(kind='hist', alpha=.25, bins=30, ax=ax, density=True)

#ax.set_xticklabels(df_log_employee_sales.LastName)
ax.set_xlabel('Log Order Price')
ax.legend(ncol=1, fontsize='small');
#plt.xticks(rotation=90);
```


![png](Mod%202%20Project_files/Mod%202%20Project_192_0.png)



```python
# Visualizing Distribution:
stats.probplot(df_log_employee_sales.LogTotalRev, plot = plt),
plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_193_0.png)



```python
tk4 = pairwise_tukeyhsd(df_log_employee_sales.LogTotalRev, df_log_employee_sales.LastName, .05)
print(tk4)
```

    Multiple Comparison of Means - Tukey HSD,FWER=0.05
    ==================================================
      group1    group2  meandiff  lower  upper  reject
    --------------------------------------------------
     Buchanan  Callahan -0.1002  -0.5005 0.3001 False 
     Buchanan  Davolio  -0.2029  -0.5876 0.1818 False 
     Buchanan Dodsworth  0.0082  -0.4728 0.4892 False 
     Buchanan   Fuller   0.0059  -0.3993 0.4111 False 
     Buchanan    King    0.0313  -0.3977 0.4602 False 
     Buchanan Leverling -0.0034  -0.3917 0.385  False 
     Buchanan  Peacock  -0.0883  -0.4642 0.2876 False 
     Buchanan   Suyama   -0.251   -0.684 0.182  False 
     Callahan  Davolio  -0.1027   -0.398 0.1926 False 
     Callahan Dodsworth  0.1084  -0.3046 0.5214 False 
     Callahan   Fuller   0.1061  -0.2154 0.4276 False 
     Callahan    King    0.1315  -0.2195 0.4825 False 
     Callahan Leverling  0.0968  -0.2032 0.3969 False 
     Callahan  Peacock   0.0119  -0.2719 0.2956 False 
     Callahan   Suyama  -0.1508  -0.5068 0.2051 False 
     Davolio  Dodsworth  0.2111  -0.1868 0.609  False 
     Davolio    Fuller   0.2088  -0.0931 0.5107 False 
     Davolio     King    0.2341   -0.099 0.5672 False 
     Davolio  Leverling  0.1995  -0.0793 0.4784 False 
     Davolio   Peacock   0.1145  -0.1467 0.3758 False 
     Davolio    Suyama  -0.0481  -0.3864 0.2902 False 
    Dodsworth   Fuller  -0.0023  -0.4201 0.4154 False 
    Dodsworth    King    0.023   -0.4178 0.4638 False 
    Dodsworth Leverling -0.0116   -0.413 0.3898 False 
    Dodsworth  Peacock  -0.0966   -0.486 0.2928 False 
    Dodsworth   Suyama  -0.2592   -0.704 0.1855 False 
      Fuller     King    0.0253  -0.3312 0.3819 False 
      Fuller  Leverling -0.0093  -0.3158 0.2972 False 
      Fuller   Peacock  -0.0943  -0.3848 0.1963 False 
      Fuller    Suyama  -0.2569  -0.6183 0.1045 False 
       King   Leverling -0.0346  -0.3719 0.3027 False 
       King    Peacock  -0.1196  -0.4425 0.2033 False 
       King     Suyama  -0.2823  -0.6701 0.1056 False 
    Leverling  Peacock   -0.085  -0.3516 0.1816 False 
    Leverling   Suyama  -0.2477  -0.5901 0.0948 False 
     Peacock    Suyama  -0.1627  -0.4909 0.1656 False 
    --------------------------------------------------


### Is total all-time revenue generated equal across all employees?
```
* $H_{0}:$ There is no difference between all-time revenue generated across all employees.
<br>
* $H_{a}:$ There is a difference between all-time revenue generated across all employees.
<br>
* $\alpha$ = 0.05
```

Based on the results of the Tukey Test above, we fail to reject the null hypothesis that there is a statistically significant difference between total all-time revenue generated for any 2 employees.

However, we still don't have details on how long each employee has worked at Northwind. Let's repeat a similar test to determine investigate relationships between average sales per employee in 2014.

### Is average Order Revenue equal across all employees in 2014?
```
* $H_{0}:$ There is no difference in average Order Revenue generated across all employees in 2014.
<br>
* $H_{a}:$ There is a difference in average Order Revenue generated across all employees in 2014.
<br>
* $\alpha$ = 0.05
```

```python
df_employee_sales_2013 = pd.read_sql_query("""SELECT e.LastName, \
            SUM((od.Quantity * od.UnitPrice * (1-od.Discount))) OrderTotal, 
            COUNT(od.Id) OrderCount
            FROM [OrderDetail] od \
            INNER JOIN [Order] o on o.Id = od.OrderId \
            INNER JOIN [Employee] e on e.Id = o.EmployeeId
            WHERE o.OrderDate LIKE "2013%"
            GROUP BY e.LastName \
            ORDER BY OrderTotal DESC;""", engine)

df_employee_sales_2013.head(10)
```


<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>LastName</th>
      <th>OrderTotal</th>
      <th>OrderCount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Peacock</td>
      <td>128809.7910</td>
      <td>218</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Leverling</td>
      <td>108026.1555</td>
      <td>184</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Davolio</td>
      <td>93148.0775</td>
      <td>156</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Fuller</td>
      <td>70444.1400</td>
      <td>102</td>
    </tr>
    <tr>
      <th>4</th>
      <td>King</td>
      <td>60471.1950</td>
      <td>91</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Callahan</td>
      <td>56032.6150</td>
      <td>124</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Suyama</td>
      <td>43126.3695</td>
      <td>86</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Buchanan</td>
      <td>30716.4675</td>
      <td>53</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Dodsworth</td>
      <td>26310.3925</td>
      <td>45</td>
    </tr>
  </tbody>
</table>
</div>



```python
# Plotting to compare 2014 sales totals by Employee:
sns.set_style('whitegrid')
sns.set_color_codes='Set2'

fig, ax = plt.subplots()

sns.barplot(x=df_employee_sales_2014_overview.LastName, y=df_employee_sales_2014_overview.OrderTotal)

ax.set_title('Total Revenue by Employee - 2014', fontsize=13, fontweight='semibold')
ax.set_xlabel('')
ax.set_ylabel('Total Revenue ($)', fontweight='semibold')
plt.xticks(rotation=45, ha='right', fontweight='semibold')

fig.savefig('TotalRevenueByEmp2014.png', bbox_inches='tight')
plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_205_0.png)



```python
tk5 = pairwise_tukeyhsd(df_employee_sales_2014.OrderTotal, df_employee_sales_2014.LastName, 0.05)
print(tk5)


#pairwise_tukeyhsd(df_log_discount_category_spend.Quantity, df_log_discount_category_spend.CategoryName, .05)
```

        Multiple Comparison of Means - Tukey HSD,FWER=0.05   
    =========================================================
      group1    group2   meandiff   lower      upper   reject
    ---------------------------------------------------------
     Buchanan  Callahan  26.2871   -735.675   788.2492 False 
     Buchanan  Davolio   -1.1629  -731.9164   729.5907 False 
     Buchanan Dodsworth  361.3336 -495.9851  1218.6522 False 
     Buchanan   Fuller   218.6609 -529.3949   966.7167 False 
     Buchanan    King    282.2013 -529.3059  1093.7085 False 
     Buchanan Leverling  282.2837 -471.1653  1035.7327 False 
     Buchanan  Peacock   -16.633  -758.8513   725.5853 False 
     Buchanan   Suyama  -210.7553 -1076.7163  655.2056 False 
     Callahan  Davolio    -27.45  -575.0753   520.1753 False 
     Callahan Dodsworth  335.0464 -372.6879  1042.7808 False 
     Callahan   Fuller   192.3738 -378.1349   762.8825 False 
     Callahan    King    255.9142 -395.5738   907.4022 False 
     Callahan Leverling  255.9966 -321.5656   833.5588 False 
     Callahan  Peacock   -42.9201 -605.7528   519.9126 False 
     Callahan   Suyama  -237.0424 -955.2214   481.1365 False 
     Davolio  Dodsworth  362.4964 -311.5233  1036.5161 False 
     Davolio    Fuller   219.8238 -308.2811   747.9286 False 
     Davolio     King    283.3641 -331.3319   898.0602 False 
     Davolio  Leverling  283.4466 -252.2704   819.1635 False 
     Davolio   Peacock   -15.4701 -535.2733   504.333  False 
     Davolio    Suyama  -209.5925  -894.571   475.3861 False 
    Dodsworth   Fuller  -142.6726  -835.413   550.0677 False 
    Dodsworth    King    -79.1323 -839.9513   681.6868 False 
    Dodsworth Leverling  -79.0498 -777.6106   619.5109 False 
    Dodsworth  Peacock  -377.9666 -1064.3992  308.4661 False 
    Dodsworth   Suyama  -572.0889 -1390.7403  246.5625 False 
      Fuller     King    63.5404  -571.6272   698.708  False 
      Fuller  Leverling  63.6228  -495.4651   622.7108 False 
      Fuller   Peacock  -235.2939 -779.1523   308.5645 False 
      Fuller    Suyama  -429.4162 -1132.8239  273.9914 False 
       King   Leverling   0.0824  -641.4281   641.593  False 
       King    Peacock  -298.8343 -927.1164   329.4478 False 
       King     Suyama  -492.9566 -1263.5011  277.5879 False 
    Leverling  Peacock  -298.9167 -850.1697   252.3363 False 
    Leverling   Suyama   -493.039 -1202.1795  216.1014 False 
     Peacock    Suyama  -194.1223 -891.3187   503.0741 False 
    ---------------------------------------------------------


## YoY Revenue:

### Is Q1 Order Revenue for 2014 significantly greater than Q1 Order Revenue from 2013?
```
* $H_{0}:$ Average Order Revenue for Q1 2014 is not greater than that of 2013.
<br>
* $H_{a}:$ Average Order Revenue for Q1 2014 is greater than that of 2013.
<br>
* $\alpha$ = 0.05
```

*Note: As we're seeking to determine whether one value is greater than the other, we'll use a one-tailed test.*



```python
# Reviewing Monthly Sales in 2014
print(max(df_revenue_2014.OrderDate)) # Checking date of last order placed
df_revenue_2014.groupby(['Month'])['OrderPrice'].sum()
```

    2014-05-06

    Month
    01     94222.1105
    02     99415.2875
    03    104854.1550
    04    123798.6825
    05     18333.6305
    Name: OrderPrice, dtype: float64



As shown above, the last recorded sale in the dataset ocurred on 2014-05-06. Because this range represents less than than 25% of the days in the Month of May, a direct comparison of revenue between May of 2013/2014 is not possible.

Instead, we'll compare the revenue from Q1 2014 with Q1 2013.


```python
fig, ax = plt.subplots(figsize=(8,4), sharex=False)
sns.set_style('whitegrid')
sns.set_palette('Set2')

a = df_revenue_2013.groupby(['Month'])['OrderPrice'].sum().plot(kind='line', ax=ax, label='2013', marker='o')
b = df_revenue_2014_q1.groupby(['Month'])['OrderPrice'].sum().plot(kind='line', ax=ax, label='2014', marker='o')

ax.set_title('YoY Revenue - 2013 vs. 2014 YTD', fontsize=13, fontweight='semibold')
ticklabels = [datetime.date(1900, month, 12).strftime('%b') for month in range(1,13)]
ax.set_xticks(np.arange(0,12))
ax.set_xlim(0,11)
ax.set_xticklabels(ticklabels) #add monthlabels to the xaxis
ax.set_xlabel('')
ax.set_ylabel('Revenue', fontweight='semibold')

ax.legend(['2013', '2014'], loc=1) #add the column names as legend.

fig.savefig('YoY_Rev_2013v2014.png', bbox_inches='tight')

plt.tight_layout(rect=[0, 0, 0.85, 1])
```


![png](Mod%202%20Project_files/Mod%202%20Project_226_0.png)


As shown above, Northwind's 2014 Q1 monthly revenue is much greater than that of 2013. However, it's also important to note that there were 632 orders placed in Q1 of 2014 while there were only 322 orders placed in Q1 of 2014.

For this reason, we'll compare the average Order Revenue from each sample.


```python
# Visualizing Q1 Sales Data:
sns.set_style('whitegrid')
fig, axes = plt.subplots(2, 1, figsize=(12,5), sharex=True, sharey=False)

# Grouping DataFrame for visualization:
df_revenue_2013_q1.groupby(['Year'])['OrderPrice'].sum().plot(kind='barh', ax=axes[0], color='#E78AC3')
df_revenue_2014_q1.groupby(['Year'])['OrderPrice'].sum().plot(kind='barh', ax=axes[1], color='#A6D854')

# Subplot 1 Labels:
axes[0].set_title('Total Order Revenue - Q1', fontweight='bold', fontsize=13)
axes[0].set_xlabel('Revenue ($)', fontweight='semibold')
axes[0].set_ylabel('')
axes[0].set_yticklabels(['2013'], fontweight='semibold')

#Subplot 2 Labels:
axes[1].set_xlabel('Revenue ($)', fontweight='semibold')
axes[1].set_ylabel('')
axes[1].set_yticklabels(['2014'], fontweight='semibold')
fig.subplots_adjust(hspace=0)

fig.savefig('tot_order_rev_Q1.png', bbox_inches='tight')
plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_228_0.png)



```python
# Visualizing Q1 Sales Data:
sns.set_style('whitegrid')
fig, axes = plt.subplots(2, 1, figsize=(12,5), sharex=True, sharey=False)

# Grouping DataFrame for visualization:
df_revenue_2013_q1.groupby(['Year'])['OrderPrice'].mean().plot(kind='barh', ax=axes[0], color='#E78AC3')
df_revenue_2014_q1.groupby(['Year'])['OrderPrice'].mean().plot(kind='barh', ax=axes[1], color='#A6D854')

# Subplot 1 Labels:
axes[0].set_title('Mean Order Revenue - Q1', fontweight='bold', fontsize=13)
axes[0].set_xlabel('Revenue ($)', fontweight='semibold')
axes[0].set_ylabel('')
axes[0].set_yticklabels(['2013'], fontweight='semibold')

#Subplot 2 Labels:
axes[1].set_xlabel('Revenue ($)', fontweight='semibold')
axes[1].set_ylabel('')
axes[1].set_yticklabels(['2014'], fontweight='semibold')
fig.subplots_adjust(hspace=0)

fig.savefig('Avg_order_rev_q1.png', bbox_inches='tight')
plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_229_0.png)



```python
# Visualizing Distribution of Mean Q1 Order Revenue:
sns.set_style('whitegrid')

# Plotting distributions:
ax = sns.distplot(df_revenue_2013_q1.OrderPrice, label='2013', color='#E78AC3')
ax = sns.distplot(df_revenue_2014_q1.OrderPrice, label='2014', color='#A6D854')

# Label/Format:
ax.set_xlim(0, 4000)
ax.set_xlabel('Order Revenue ($)', fontweight='semibold')
ax.set_title('Distribution of Q1 Revenue Generated per Order', fontsize=14, fontweight='bold')
ax.legend(loc=1, fontsize='medium');
```


![png](Mod%202%20Project_files/Mod%202%20Project_230_0.png)



```python
# Visualizing Distribution of Mean Q1 Order Revenue:
sns.set_style('whitegrid')

# Plotting distributions:
ax = sns.distplot(df_log_revenue_2013_q1.LogTotalRev, label='2013', color='#E78AC3')
ax = sns.distplot(df_log_revenue_2014_q1.LogTotalRev, label='2014', color='#A6D854')

# Label/Format:
ax.set_xlabel('Log Order Revenue ($)', fontweight='semibold')
ax.set_title('Distribution of Log Q1 Order Revenue', fontsize=14, fontweight='bold')
ax.legend(loc=1, fontsize='medium');
```


![png](Mod%202%20Project_files/Mod%202%20Project_234_0.png)



```python
# Visualizing Distribution:
stats.probplot(df_log_revenue_2013_q1.LogTotalRev, plot = plt),
stats.probplot(df_log_revenue_2014_q1.LogTotalRev, plot = plt),

plt.show()
```


![png](Mod%202%20Project_files/Mod%202%20Project_235_0.png)



```python
# Performing a one-tailed t-test by diving pval by 2:
stats.ttest_ind(df_log_revenue_2014_q1.LogTotalRev, df_log_revenue_2013_q1.LogTotalRev)
```




    Ttest_indResult(statistic=1.7014366592724246, pvalue=0.0891875503870743)




```python
pvalue= 0.0891875503870743/2
print(pvalue)
pvalue < .05
```

    0.04459377519353715





    True



#### Is Average Q1 Order Revenue for 2014 significantly greater than Average Q1 Order Revenue from 2013?
```
* $H_{0}:$ Average Order Revenue for Q1 2014 is not greater than that of 2013.
<br>
* $H_{a}:$ Average Order Revenue for Q1 2014 is greater than that of 2013.
<br>
* $\alpha$ = 0.05
```

The p-value = 0.04. Because our p-value < 0.05, we reject the null hypothesis that Average Order Revenue for Q1 2014 is not greater than that of 2013.

With this data, we're able to share the good news with confidence, not only have Northwind's number of Orders doubled in a YoY comparison between 2013 and 2014, but the average Order Revenue has also increased by a stastically significant amount.
