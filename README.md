# Property match

### Objective

Find all the properties which matches the criteria and has match percentage greater than 40.

### Problem Statement
Write an algorithm to match these properties and search criteria such that each match has a match percentage.

### Base Condition

* For property and requirement to be considered a valid match, distance should be within 10 miles, the budget is +/- 25%, bedroom and bathroom should be +/- 2.

### Algorithm

#### Extracting the Properties that matches the base condition 

* Getting all the properties which matches the condition budget +/- 25% and adding to set

```if minprice and maxprice:
    cursor.execute(
        "select id,lat,lon,price,bed,bath from props where price >= " +
        minprice + " and price <= " + maxprice + ";")
else:
    if not minprice:
        budget = int(int(maxprice) * 25 / 100)
        cursor.execute(
            "select id,lat,lon,price,bed,bath from props where price >= " +
            str(int(maxprice) - budget) + " and price <= " +
            str(int(maxprice) + budget) + ";")
    else:
        budget = int(int(minprice) * 25 / 100)
        cursor.execute(
            "select id,lat,lon,price,bed,bath from props where price >= " +
            str(int(minprice) - budget) + " and price <= " +
            str(int(minprice) + budget) + ";")
price = cursor.fetchall()
for i in price:
    ans.add(i)
```  
   ##### Note - Both minimum and maximum price can be given by user or any one
    
* Getting all the properties which matches the condition bedroom +/- 2 and adding to set

```if minbed and maxbed:
    cursor.execute("select id,lat,lon,price,bed,bath from props where bed >= "
                   + minbed + " and bed <= " + maxbed + ";")
else:
    if not minbed:
        cursor.execute(
            "select id,lat,lon,price,bed,bath from props where bed >= " +
            str(int(maxbed) - 2) + " and price <= " + str(int(maxbed) + 2) +
            ";")
    else:
        cursor.execute(
            "select id,lat,lon,price,bed,bath from props where bed >= " +
            str(int(minbed) - 2) + " and price <= " + str(int(minbed) + 2) +
            ";")
bed = cursor.fetchall()
for i in bed:
    ans.add(i)
```    
    
##### Note - Both minimum and maximum bedroom can be given by user or any one
    
* Getting all the properties which matches the condition distance < 10 miles

```cursor.execute(
    "select id,lat,lon,price,bed,bath,(3956 *acos(cos(radians(" + latitude +
    ")) * cos(radians(lat)) * cos(radians(lon) - radians(" + longitude +
    ")) + sin(radians(" + latitude +
    ")) * sin(radians(lat )))) AS distance  from props having distance < 10")
distance = cursor.fetchall()```

*`Note - radius is multiplied by 3956 to get miles`
      
*match the output that matches the condition distance < 10 miles with properties persent at set.
*final contains all the valid matches

```final = []
for i in distance:
    for j in ans:
        if i[0] == j[0]:
            final.append(i)
```
            
  ##### Note - final array has list of properties that matches the base condition
     
* calculate percentage matches for all the properties in final.
* Output must be properties with match percentage > 40   

##### If the distance is within 2 miles, distance contribution for the match percentage is fully 30% else it depends on the distance
  ```if int(i[6]) <= 2:
        per += 30
    else:
        per += 30 - (int(i[6]) - 2) * (30 / 8)```
        
*`If the budget is within min and max budget, the budget contribution for the match percentage is full 30%. If min or max is not given, +/- 10% budget is a full 30% match.`
  
  ```if minprice and maxprice:
        if int(i[3]) >= int(minprice) and int(i[3]) <= int(maxprice):
            per += 30
        else:
            if int(i[3]) < int(minprice):
                per += (int(i[3]) / int(minprice)) * 100 * 0.3
            elif int(i[3]) > int(maxprice):
                per += (int(maxprice) / int(i[3])) * 100 * 0.3
    else:
        if not minprice:
            budget = int(maxprice) * 10 / 100
            if int(i[3]) >= int(maxprice) - budget and int(
                    i[3]) <= int(maxprice) + budget:
                per += 30
            else:
                if int(i[3]) < int(maxprice) - budget:
                    per += (int(i[3]) / int(maxprice) - budget) * 100 * 0.3
                elif int(i[3]) > int(maxprice) + budget:
                    per += (int(maxprice) + budget / int(i[3])) * 100 * 0.3
        else:
            budget = int(minprice) * 10 / 100
            if int(i[3]) >= int(minprice) - budget and int(
                    i[3]) <= int(minprice) + budget:
                per += 30
            else:
                if int(i[3]) < int(minprice) - budget:
                    per += (int(i[3]) / int(minprice) + budget) * 100 * 0.3
                elif int(i[3]) > int(minprice) + budget:
                    per += (int(minprice) + budget / int(i[3])) * 100 * 0.3
 ```
                    
##### If the bedroom and bathroom fall between min and max, each will contribute a full 20%. If min or max is not given, the match percentage varies according to the value.
```if minbed and maxbed:
        if int(i[4]) >= int(minbed) and int(i[4]) <= int(maxbed):
            per += 20
        else:
            if int(i[4]) < int(minbed):
                per += (int(i[4]) / int(minbed)) * 100 * 0.2
            elif int(i[4]) > int(maxbed):
                per += (int(maxbed) / int(i[4])) * 100 * 0.2
    else:
        if not minbed:
            if int(i[4]) < int(maxbed):
                per += (int(i[4]) / int(maxbed)) * 100 * 0.2
            elif int(i[4]) > int(maxbed):
                per += (int(maxbed) / int(i[4])) * 100 * 0.2
        else:
            if int(i[4]) < int(minbed):
                per += (int(i[4]) / int(minbed)) * 100 * 0.2
            elif int(i[4]) > int(minbed):
                per += (int(minbed) / int(i[4])) * 100 * 0.2
    if minbath and maxbath:
        if int(i[5]) >= int(minbath) and int(i[5]) <= int(maxbath):
            per += 20
        else:
            if int(i[5]) < int(minbath):
                per += (int(i[5]) / int(minbath)) * 100 * 0.2
            elif int(i[5]) > int(maxbath):
                per += (int(maxbath) / int(i[5])) * 100 * 0.2
    else:
        if not minbath:
            if int(i[5]) < int(maxbath):
                per += (int(i[5]) / int(maxbath)) * 100 * 0.2
            elif int(i[5]) > int(maxbath):
                per += (int(maxbath) / int(i[5])) * 100 * 0.2
        else:
            if int(i[5]) < int(minbath):
                per += (int(i[5]) / int(minbath)) * 100 * 0.2
            elif int(i[5]) > int(minbath):
                per += (int(minbath) / int(i[5])) * 100 * 0.2
 ```
                

#### Scaling large no. of data

* to know all the property within the radius
  * `we can use s2 library with radius as input and filter the properties within the given radius on the map`
 
* whenever new property is added
  * `data is send through web application firewall for security purpose and Load balancer`
  * `the location will be marked on the map`
 
* need of websockets 
  * `for asynchronous way of sending and recieving data`

* Can have a supply service
  * `supply service get the demand from the user and matches with the list of properties within the radius`
  
* db should be always available
  * `Database should be heavily readable/writeable`


### Result

#### Property Details with match percentage

* `(1, 54.0, 20.0, 1500, 2, 2, 0.0) match percentage:  83.33333333333333`
* `(3, 54.0, 20.0, 1750, 10, 10, 0.0) match percentage:  96.66666666666667`


  
