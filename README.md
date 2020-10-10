import mysql.connector \#Getting connection to DB

mydb = mysql.connector.connect( host="localhost", user="root", passwd="", database="property")

print("Database object: ") print(mydb) print("") ans = set()
