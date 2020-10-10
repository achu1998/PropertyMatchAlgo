
#@Property Match  
#!/usr/bin/env python  
\# coding: utf-8  
  
\# In\[1\]:  
  
  
import mysql.connector  
  
  
\# In\[2\]:  
  
  
#Getting connection to DB  
  
mydb = mysql.connector.connect(  
    host="localhost", user="root", passwd="", database="property")  
  
print("Database object: ")  
print(mydb)  
print("\\n")  
ans = set()  
  
  
\# In\[3\]:  
  
  
#Getting input and checking for valid inputs. If input is not valid, get input again  
#inputs - id, latitude,longitude, minimum price, maximum price, minimum no. of bed  
#maximum no. of bed, minimum no. of bath,maximum no. of bath  
#either min or max value should be present  
  
iden = input("Enter id: ")  
  
while not iden:  
    print("Invalid input, Enter again!")  
    iden = input("Enter id: ")  
       
latitude = input("Enter latitude: ")  
  
while not latitude:  
    print("Invalid input, Enter again!")  
    lat = input("Enter latitude: ")  
  
longitude = input("Enter Longitude: ")  
  
while not longitude:  
    print("Invalid input, Enter again!")  
    lon = input("Enter Longitude: ")  
   
  
minprice = input("Enter Minimum price: ")  
maxprice = input("Enter Maximum price: ")  
  
while maxprice <= minprice or (not maxprice and not minprice):  
    print("Invalid input, Enter again!")  
    minprice = input("Enter Minimum price: ")  
    maxprice = input("Enter Maximum price: ")  
        
minbed = input("Enter Minimum bed: ")  
maxbed = input("Enter Maximum bed: ")  
  
while maxbed <= minbed or (not minbed and not maxbed):  
    print("Invalid input, Enter again!")  
    minbed = input("Enter Minimum bed: ")  
    maxbed = input("Enter Maximum bed: ")  
         
minbath = input("Enter Minimum bath: ")  
maxbath = input("Enter Maximum bath: ")  
  
while maxbath <= minbath or (not minbath and not maxbath):  
    print("Invalid input, Enter again!")  
    minbath = input("Enter Minimum bath: ")  
    maxbath = input("Enter Maximum bath: ")  
  
  
\# In\[4\]:  
  
  
#For property and requirement to be considered a valid match, distance should be within 10 miles,  
#the budget is +/- 25%, bedroom and bathroom should be +/- 2.  
#Getting all the properties which matches the condition budget +/- 25% and adding to set  
  
cursor = mydb.cursor()  
if minprice and maxprice:  
    cursor.execute(  
        "select id,lat,lon,price,bed,bath from props where price >= " +  
        minprice + " and price <= " + maxprice + ";")  
else:  
    if not minprice:  
        budget = int(int(maxprice) \* 25 / 100)  
        cursor.execute(  
            "select id,lat,lon,price,bed,bath from props where price >= " +  
            str(int(maxprice) - budget) + " and price <= " +  
            str(int(maxprice) + budget) + ";")  
    else:  
        budget = int(int(minprice) \* 25 / 100)  
        cursor.execute(  
            "select id,lat,lon,price,bed,bath from props where price >= " +  
            str(int(minprice) - budget) + " and price <= " +  
            str(int(minprice) + budget) + ";")  
price = cursor.fetchall()  
for i in price:  
    ans.add(i)  
  
  
\# In\[5\]:  
  
  
#Getting all the properties which matches the condition bedroom +/- 2 and adding to set  
  
if minbed and maxbed:  
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
  
  
\# In\[6\]:  
  
  
#Getting all the properties which matches the condition bathroom +/- 2 and adding to set  
  
if minbath and maxbath:  
    cursor.execute("select id,lat,lon,price,bed,bath from props where bed >= "  
                   + minbath + " and bed <= " + maxbath + ";")  
else:  
    if not minbed:  
        cursor.execute(  
            "select id,lat,lon,price,bed,bath from props where bed >= " +  
            str(int(maxbath) - 2) + " and price <= " + str(int(maxbath) + 2) +  
            ";")  
    else:  
        cursor.execute(  
            "select id,lat,lon,price,bed,bath from props where bed >= " +  
            str(int(minbath) - 2) + " and price <= " + str(int(minbath) + 2) +  
            ";")  
bath = cursor.fetchall()  
for i in bath:  
    ans.add(i)  
  
  
\# In\[7\]:  
  
  
#Getting all the properties which matches the condition distance < 10 miles  
  
cursor.execute(  
    "select id,lat,lon,price,bed,bath,(3956 \*acos(cos(radians(" + latitude +  
    ")) \* cos(radians(lat)) \* cos(radians(lon) - radians(" + longitude +  
    ")) + sin(radians(" + latitude +  
    ")) \* sin(radians(lat )))) AS distance  from props having distance < 10")  
distance = cursor.fetchall()  
for i in distance:  
    print(i)  
  
  
\# In\[8\]:  
  
  
#match the output that matches the condition distance < 10 miles with properties persent at set.  
#final contains all the valid matches  
  
final = \[\]  
for i in distance:  
    for j in ans:  
        if i\[0\] == j\[0\]:  
            final.append(i)  
  
  
\# In\[10\]:  
  
  
#calculate percentage matches for all the properties in final.  
#Output must be properties with match percentage > 40  
  
for i in final:  
    per = 0  
    if int(i\[6\]) <= 2:  
        per += 30  
    else:  
        per += 30 - (int(i\[6\]) - 2) \* (30 / 8)  
  
    if minprice and maxprice:  
        if int(i\[3\]) >= int(minprice) and int(i\[3\]) <= int(maxprice):  
            per += 30  
        else:  
            if int(i\[3\]) < int(minprice):  
                per += (int(i\[3\]) / int(minprice)) \* 100 \* 0.3  
            elif int(i\[3\]) > int(maxprice):  
                per += (int(maxprice) / int(i\[3\])) \* 100 \* 0.3  
    else:  
        if not minprice:  
            budget = int(maxprice) \* 10 / 100  
            if int(i\[3\]) >= int(maxprice) - budget and int(  
                    i\[3\]) <= int(maxprice) + budget:  
                per += 30  
            else:  
                if int(i\[3\]) < int(maxprice) - budget:  
                    per += (int(i\[3\]) / int(maxprice) - budget) \* 100 \* 0.3  
                elif int(i\[3\]) > int(maxprice) + budget:  
                    per += (int(maxprice) + budget / int(i\[3\])) \* 100 \* 0.3  
        else:  
            budget = int(minprice) \* 10 / 100  
            if int(i\[3\]) >= int(minprice) - budget and int(  
                    i\[3\]) <= int(minprice) + budget:  
                per += 30  
            else:  
                if int(i\[3\]) < int(minprice) - budget:  
                    per += (int(i\[3\]) / int(minprice) + budget) \* 100 \* 0.3  
                elif int(i\[3\]) > int(minprice) + budget:  
                    per += (int(minprice) + budget / int(i\[3\])) \* 100 \* 0.3  
  
    if minbed and maxbed:  
        if int(i\[4\]) >= int(minbed) and int(i\[4\]) <= int(maxbed):  
            per += 20  
        else:  
            if int(i\[4\]) < int(minbed):  
                per += (int(i\[4\]) / int(minbed)) \* 100 \* 0.2  
            elif int(i\[4\]) > int(maxbed):  
                per += (int(maxbed) / int(i\[4\])) \* 100 \* 0.2  
    else:  
        if not minbed:  
            if int(i\[4\]) < int(maxbed):  
                per += (int(i\[4\]) / int(maxbed)) \* 100 \* 0.2  
            elif int(i\[4\]) > int(maxbed):  
                per += (int(maxbed) / int(i\[4\])) \* 100 \* 0.2  
        else:  
            if int(i\[4\]) < int(minbed):  
                per += (int(i\[4\]) / int(minbed)) \* 100 \* 0.2  
            elif int(i\[4\]) > int(minbed):  
                per += (int(minbed) / int(i\[4\])) \* 100 \* 0.2  
    if minbath and maxbath:  
        if int(i\[5\]) >= int(minbath) and int(i\[5\]) <= int(maxbath):  
            per += 20  
        else:  
            if int(i\[5\]) < int(minbath):  
                per += (int(i\[5\]) / int(minbath)) \* 100 \* 0.2  
            elif int(i\[5\]) > int(maxbath):  
                per += (int(maxbath) / int(i\[5\])) \* 100 \* 0.2  
    else:  
        if not minbath:  
            if int(i\[5\]) < int(maxbath):  
                per += (int(i\[5\]) / int(maxbath)) \* 100 \* 0.2  
            elif int(i\[5\]) > int(maxbath):  
                per += (int(maxbath) / int(i\[5\])) \* 100 \* 0.2  
        else:  
            if int(i\[5\]) < int(minbath):  
                per += (int(i\[5\]) / int(minbath)) \* 100 \* 0.2  
            elif int(i\[5\]) > int(minbath):  
                per += (int(minbath) / int(i\[5\])) \* 100 \* 0.2  
    if per > 40:  
        print(i, end=" ")  
        print("match percentage: ", per)  
  
  
\# In\[ \]:
