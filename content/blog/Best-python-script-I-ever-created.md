---
title: "Best Python Script I Ever Created"
date: 2019-04-11T12:35:43+05:30
---
After my first semester exams in college (Computer Science Engineering, of course!) was over, we got the results.

Fortunately, I got good marks. I eagerly wanted to know what my rank was.

But, I didn’t want to ask each and every person to about their score. We studied Python in our first semester (But I knew python before it) .

So, I thought like to make a python script to scrape all the grades and CGPA of my classmates.

The script only needs the starting Register Number and Ending Register Number. It will scrape all of it and put it in a Spreadsheet.

You can see the spread sheet in the below screenshot. (Names are blurred for privacy reasons.

![image](https://i.postimg.cc/1ynS3XQb/sheet.png)

It was the best python script I have ever written.

I open sourced it in github. See the embedded version.

```python
# Created by Selva
# Made with ❤ in Selvasoft
 
from bs4 import BeautifulSoup
import requests
 
""" Python Program to get all students grades with CGPA.
 Note: Install BeautifulSoup Module and Requests Module
 before running this program!!!"""
 
csv_file = open("CGPA_Provided_by_Selva.csv","w")
# This is example Reg. No. College code is fake :P
reg_start = 123417103001 # Enter starting Reg. number
reg_end = 123417103027 # Ending Reg. number
# Found the Database URL after a long time. :) 
url = "http://aucoe.annauniv.edu/cgi-bin/result/cgrade.pl?regno="
 
def write_csv(list1):
 # Function to write in CSV
 for word in list1:
 csv_file.write(str(word)+",")
 csv_file.write("\n")
# Write the Table Titles
write_csv(['Name','Phy Lab','Chemistry','Python','EG','Py Lab','English','Maths','Physics','CGPA'])
 
def calculate_cgpa(mylist):
 # CGPA Calculator Algorithm
 # Took hours to make :P
 points = [0, 2, 3, 3, 4, 2, 4, 4, 3]
 credit = {"O": 10, "A+": 9, "A": 8, 'B+': 7, 'B': 6}
 total_credits = 0
 my_credits = 0
 for i in range(1, 9):
 if credit.__contains__(mylist[i]):
 my_credits = my_credits + (credit[mylist[i]] * points[i])
 total_credits = total_credits + (points[i] * 10)
 try:
 cgpa = my_credits / total_credits * 10
 except ZeroDivisionError:
	# Occurs when someone is absent in all subjects.
 return 0
 return round(cgpa, 2)
 
for i in range(reg_start,reg_end+1):
 data_list = []
 soup = requests.get(url+str(i))
 # Idk what to name the variable :P
 selva = BeautifulSoup(soup.text,"html.parser")
 td = selva.find_all("td")
 tables = td[1].find_all("table")
 strongs = tables[0].find_all("strong")
 table_2 = tables[1].find_all("strong")
 no = 4
 name = strongs[1].string
 data_list.append(name)
 # Print progress with name
 print("Completed for "+name)
 while no < len(table_2):
 print(table_2[no].string)
 data_list.append(table_2[no].string)
 no = no + 3
 data_list.append(calculate_cgpa(data_list))
 write_csv(data_list)
 
csv_file.close()
```

You can see the code also in this <a href="https://github.com/Peratchiselvan/PythonProjects/blob/master/mark.py" target="_blank">github link</a>.

And the most Important thing is I got the lowest mark in my python exam. The irony...

Regards,

Selva.
