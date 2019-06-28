
## Northwind Co: Company Sales Analysis
Flatiron Module 2 Project: 
<br>
Paul Woody
<br>
*Annotated code can be found [here](https://github.com/pawoody/dsc-2-final-project-online-ds-ft-041519/blob/master/Mod%202%20Project.ipynb).*

![Paul](Paul.jpeg)

# Project Overview:
For this project, we'll be exploring the data provided by our client, Northwind, containing companywide sales data for a range of products.  Our goal is to derive the significance of discounts on overall sales as well as to provide the client with additional business recommendations based on the results of EDA. 

To complete our analysis, we will follow the OSEMN Data Science process as outlined [here](http://www.dataists.com/tag/osemn/).


## Provided ERD for Northwind Co:
![Northwind ERD](Northwind_ERD.png)


# Obtain / Load Data:

Let's verify the accuracy of the provided ERD:

```
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



Regions:

    Region
    British Isles       8
    Central America     5
    Eastern Europe      1
    North America      16
    Northern Europe     4
    Name: CompanyName, dtype: int64




Countries:

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

![png](Mod%202%20Project_files/Mod%202%20Project_62_0.png)


Should we choose to explore revenue by region further in our EDA, we should likely group smaller regions such as Central Europe to a new "Europe" region in order to increase our sample population and receive results that are more likely to be statistically significant.

However, it should be noted that economy and cultural preference are likely factored into the region categorization implemented by Northwind, and may play a role in consumer purchase behavior. In a future experiment (with additional customers/sales in the "smaller" regions' dataset), Northwind could analyze the performance of these smaller regions individually.


### Shipper, Supplier Table Scrubbing:

Shippers:
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

Suppliers:
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

## 1. Do discounts have a statistically significant effect on the number of products customers order? If so, at what level(s) of discount?

### 1a. Do discounts have a statistically significant effect on the number of products customers order?

* $H_{0}:$ Discount has no effect on the number of products ordered.
* $H_{a}:$ Discount does have an effect on number of products ordered.
* $\alpha$ = 0.05


![png](Mod%202%20Project_files/Mod%202%20Project_79_0.png)


As seen above, while there are less orders made for discounted items, the average order size when purchasing discounted items is larger. In order to determine the effect of any discount to sales, we will determine whether the differences illustrated above are stastically significant.

*Note: This initial inquiry does not factor in item price or discount level.*



![png](Mod%202%20Project_files/Mod%202%20Project_86_0.png)



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



#### 1a. Do discounts have a statistically significant effect on the number of products customers order?

* $H_{0}:$ Discount has no effect on the number of products ordered.
* $H_{a}:$ Discount does have an effect on number of products ordered.
* $\alpha$ = 0.05


The resulting p-value of our two-tailed Welch's t-test is 1.00e$^{-10}$.

p < 0.05

**Discounts do have a statistically significant effect on the number of products customers order.**

The resulting p-value is less than our alpha value of 0.05. Therefore, we reject the null hypothesis that there is no difference between average order size for orders with and without discounts. Since we've confirmed that discounts do have a statistically significant effect on the number of products customers order, we can explore the difference in effect at each level of discount.

### 1b. If so, at what level(s) of discount?

* $H_{0}:$ The average number of products ordered by customers is the same regardless of discount level.
* $H_{a}:$ The average number of products ordered by customers is not the same as discount level changes.
* $\alpha$ = 0.05


![png](Mod%202%20Project_files/Mod%202%20Project_94_0.png)



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


#### 1b. If so, at what level(s) of discount?


* $H_{0}:$ The number of products ordered by customers is the same regardless of discount level.
* $H_{a}:$ The number of products ordered by customers is not the same as discount level changes. 
* $\alpha$ = 0.05
<br>

Reviewing the results of the Tukey test, we fail to reject the null hypothesis that there is a significant difference between the control group (no discount) and the experimental group discount level 0.10. 

For discount levels of 0.05, 0.15, 0.20, and 0.25, we reject the null hypothesis, indicating that there **is** a significant difference in the number of products ordered between the control group (no discount) and experimental groups with discount levels of 0.05, 0.15, 0.20, and 0.25

**There is no statistically significant difference in number of products ordered between groups with non-zero discount level.**

While valuable, the results obtained from our first inquiry necessitate consideration of item price to give context. Before definining the parameters of our next inquiry, let's review the results obtained thusfar.

**Summation:**
<br>
There is a statistically significant difference in order quantity based on the presence of a discount at any level, but there is not a statistically significant difference between order quantity and discount level at discount level 0.10.

Great! We can safely advise our client that customers order more products when a discount is offered, but that the delta in customer order volume for each discount level is not statistically significant. However, this data doesn't tell the full story. In order to advise the client on whether (and at what level) to offer discounts, we must determine whether the increase in product orders for discounted items produces a statistically significant difference in every company's #1 metric for success, **revenue**. 

Essentially, we need to determine whether or not customers spend more when discounts are present and whether additional spend exists to offset the loss in potential revenue due to discounts. For example, if there is no statistically significant difference in customer spend regardless of whether a 5% or 25% discount is offered, we would likely advise the client to reduce the discount level of most items to 5%.

## 2. Do discounts have a statistically significant effect on customer spend per order? If so, at what level(s) of discount?

### 2a. Do discounts have a statistically significant effect on customer spend?

* $H_{0}:$ Discounts do not have a statistically significant effect customer spend.
* $H_{a}:$ Discounts do have a statistically significant effect (whether positive or negative) on customer spend.
* $\alpha$ = 0.05

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



![png](Mod%202%20Project_files/Mod%202%20Project_110_0.png)


Based on the visualization above, we can determine that Northwind generates more total revenue from sales of non-discounted items. However, the average order cost for orders containing discounts is greater than those without discounts. To determine whether this difference is statistically significant, we should first review the distribution of Order Price for orders with and without discount(s).


![png](Mod%202%20Project_files/Mod%202%20Project_117_0.png)



![png](Mod%202%20Project_files/Mod%202%20Project_118_0.png)


The visualization of Distribution of Order Revenue is positively skewed. Before determining the test we'll use to test our hypothesis, let's first visualize and compare the shapes of the distributions above in separate plots.


![png](Mod%202%20Project_files/Mod%202%20Project_120_0.png)


![png](Mod%202%20Project_files/Mod%202%20Project_121_0.png)


A visual examination of the distribution of Order Revenue suggests that we should transform the data to fit a normal distribution before modeling and hypothesis testing.


![png](Mod%202%20Project_files/Mod%202%20Project_128_0.png)



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


#### 2a. Do discounts have a statistically significant effect on average customer spend (order price)?


* $H_{0}:$ Discounts do not have a statistically significant effect on average customer spend.
* $H_{a}:$ Discounts do have a statistically significant effect (whether positive or negative) on average customer spend.
* $\alpha$ = 0.05


The resulting p-value of our two-tailed t-test is 0.07.

Because p > 0.05 we fail to reject the null hypothesis that states that discount (as a boolean) does not have a statistically significant effect on customer spend.

### 2b. At what level(s) of discount is there a significant effect on average customer spend?

* $H_{0}:$ Customer order price is the same regardless of discount level.
* $H_{a}:$ Customer order price is not the same as discount level changes.
* $\alpha$ = 0.05


![png](Mod%202%20Project_files/Mod%202%20Project_136_0.png)



![png](Mod%202%20Project_files/Mod%202%20Project_139_0.png)


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
</table><br/><br/>


```python
# ANOVA test:
sm.stats.anova_lm(lm, typ=2)
```


<div>

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



## 3. Do discounts have a statistically significant effect on Order Quantity by Category? If so, at what level(s) of discount?
*Note:  Product categories include 'Beverages', 'Condiments', 'Confections', 'Dairy Products', 'Grains/Cereals', 'Meat/Poultry', 'Produce', and 'Seafood'. 

### 3a. Do discounts have a statistically significant effect on the number of products customers order by category?

* $H_{0}:$ Discounts do not have a statistically significant effect on number of products customers order by category.
* $H_{a}:$ Discounts either increase or decrease the number of products customers order by category.
* $\alpha$ = 0.05


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


![png](Mod%202%20Project_files/Mod%202%20Project_159_0.png)


Comparing with/without discounts applied:

![png](Mod%202%20Project_files/Mod%202%20Project_160_0.png)


![png](Mod%202%20Project_files/Mod%202%20Project_161_0.png)


Because we're seeking to determine whether the means of two independent groups are significantly different, we'd like to conduct an Independent two tailed t-test.  However, we must first ensure that the populations meet the assumptions of normality and homogeneity of variance required. 

As seen above, the distribution for Order Quantities with and without a Discount are positively skewed. In order to conduct a two-tailed t-test, we should first normalize both distributions.


![png](Mod%202%20Project_files/Mod%202%20Project_164_0.png)


As seen above, log scaling the values for Order Quantity increased the distributions' normality. Next, we'll compare sample population variances and create a QQ plot of the distribution shape for Order Quantity with and without a discount.  

```python
variance_ratio(a=df_log_discount_category_spend_disc.LogOrderQuantity, b=df_log_discount_category_spend_nodisc.LogOrderQuantity)
```

    0.9030998215987438


![png](Mod%202%20Project_files/Mod%202%20Project_168_0.png)


Great! With confirmation of normal distribution and homogeneity of variance in each population, the data meets the assumptions required to conduct an independent t-test.


```python
# Using a two-tailed t-test to compare distributions above:
stats.ttest_ind(df_log_discount_category_spend_disc.LogOrderQuantity, df_log_discount_category_spend_nodisc.LogOrderQuantity)
```
    Ttest_indResult(statistic=6.920700463008245, pvalue=5.91313388186685e-12)

Because p < 0.05, we reject the null hypothesis that there is no significant difference in Order quantity for orders with and without a discount.


![png](Mod%202%20Project_files/Mod%202%20Project_174_0.png)


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


## 4. Is there a statistically significant difference in performance of Sales Employees at Northwind?

### 4a. Is total all-time revenue generated equal across all employees?

* $H_{0}:$ There is no difference between all-time revenue generated across all employees.
* $H_{a}:$ There is a difference between all-time revenue generated across all employees.
* $\alpha$ = 0.05


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


![png](Mod%202%20Project_files/Mod%202%20Project_184_0.png)


Ranking the employees in the Sales Team by revenue generated (TotalRev) is only a sigificant measure given the context of how long each employee has worked for Northwind. However, as seen above, the hire date for all employees does not match the period from 2012-2014 in which this data was recorded.

For this reason, it's likely more productive to view differences in 2014 YTD revenue generated for each employee and YoY revenue generated by Northwind as a whole. Also worth mentioning is that there are 3 basic "levels" of employee seniority (Sales Rep, Manager, VP) included. Because we do not have information regarding employee salary as well as other KPIs these employees may be accountable for, a direct comparison of revenue generated is likely not fully representitive of employee performance. 


![png](Mod%202%20Project_files/Mod%202%20Project_187_0.png)



![png](Mod%202%20Project_files/Mod%202%20Project_188_0.png)



![png](Mod%202%20Project_files/Mod%202%20Project_192_0.png)


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


### 4a. Is total all-time revenue generated equal across all employees?

* $H_{0}:$ There is no difference between all-time revenue generated across all employees.
<br>
* $H_{a}:$ There is a difference between all-time revenue generated across all employees.
<br>
* $\alpha$ = 0.05


Based on the results of the Tukey Test above, we fail to reject the null hypothesis that there is a statistically significant difference between total all-time revenue generated for any 2 employees.

However, we still don't have details on how long each employee has worked at Northwind. Let's repeat a similar test to determine investigate relationships between average sales per employee in 2014.

### 4b. Is average Order Revenue equal across all employees in 2014?

* $H_{0}:$ There is no difference in average Order Revenue generated across all employees in 2014.
* $H_{a}:$ There is a difference in average Order Revenue generated across all employees in 2014.
* $\alpha$ = 0.05

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


## 5. YoY Revenue:

### 5a. Is Q1 Order Revenue for 2014 significantly greater than Q1 Order Revenue from 2013?

* $H_{0}:$ Average Order Revenue for Q1 2014 is not greater than that of 2013.
* $H_{a}:$ Average Order Revenue for Q1 2014 is greater than that of 2013.
* $\alpha$ = 0.05


*Note: As we're seeking to determine whether one value is greater than the other, we'll use a one-tailed test.*

```
    2014-05-06

    Month
    01     94222.1105
    02     99415.2875
    03    104854.1550
    04    123798.6825
    05     18333.6305
    Name: OrderPrice, dtype: float64
```

As shown above, the last recorded sale in the dataset ocurred on 2014-05-06. Because this range represents less than than 25% of the days in the Month of May, a direct comparison of revenue between May of 2013/2014 is not possible.

Instead, we'll compare the revenue from Q1 2014 with Q1 2013.


![png](Mod%202%20Project_files/Mod%202%20Project_226_0.png)


As shown above, Northwind's 2014 Q1 monthly revenue is much greater than that of 2013. However, it's also important to note that there were 632 orders placed in Q1 of 2014 while there were only 322 orders placed in Q1 of 2014.

For this reason, we'll compare the average Order Revenue from each sample.


![png](Mod%202%20Project_files/Mod%202%20Project_228_0.png)


![png](Mod%202%20Project_files/Mod%202%20Project_229_0.png)


![png](Mod%202%20Project_files/Mod%202%20Project_230_0.png)


![png](Mod%202%20Project_files/Mod%202%20Project_234_0.png)


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



#### 5a. Is Average Q1 Order Revenue for 2014 significantly greater than Average Q1 Order Revenue from 2013?

* $H_{0}:$ Average Order Revenue for Q1 2014 is not greater than that of 2013.
* $H_{a}:$ Average Order Revenue for Q1 2014 is greater than that of 2013.
* $\alpha$ = 0.05


The p-value = 0.04. Because our p-value < 0.05, we reject the null hypothesis that Average Order Revenue for Q1 2014 is not greater than that of 2013.

With this data, we're able to share the good news with confidence, not only have Northwind's number of Orders doubled in a YoY comparison between 2013 and 2014, but the average Order Revenue has also increased by a stastically significant amount.
