---
title: "Creating Command Line Torrent Searcher"
date: 2019-04-01T13:01:07+05:30
---
Its been difficult to search torrents in the web these days.
So, I planned to create a torrent searching script using python.
First of all, we need asource to scrap the torrents.

I use the following torrent websites:

* <a href="https://thepiratebay.org" target="_blank">The Pirate Bay</a>
* <a href="https://snowfl.com" target="_blank">snowfl</a>
* <a href="https://skytorrents.to/" target="_blank">Skytorrents</a>

# Review
The Pirate Bay proxies are banned pretty quickly. So, its waste to use them.

Snowfl is great since that website itself scraps torrents from tpb and 1337x etc...
But there is no way to provide search arguments in the url. 

( Like ```https://google.com/?q=search+term``` )

Next option is skytorrents. Darn, It even got rss to search. I planned to use it.

# Creation
The base url for rss search is ```https://skytorrents.to/rss?search=search+term```

I tried to parse it using <a href="https://pypi.org/project/feedparser/" target="_blank">feedparser</a> library in python.

F**k, the seeders and leechers in the rss feed are in the torznab attribute 
{{<highlight xml "hl_lines=5-6" >}}
<torznab:attr name="category" value="movie"/>
<torznab:attr name="category" value="1080"/>
<torznab:attr name="category" value="YiFY"/>
<torznab:attr name="type" value="video"/>
<torznab:attr name="seeders" value="635"/>
<torznab:attr name="peers" value="39"/>
<torznab:attr name="infohash" value="e2e457b2e77128cd20fafd0837bbdb9a4d543578"/>
<torznab:attr name="minimumratio" value="1.0"/>
<torznab:attr name="minimumseedtime" value="172800"/>
{{</highlight>}}

which are not parsed by feedparser library.

Sh*t, now I am back to <a href="https://pypi.org/project/beautifulsoup4/" target="_blank">beautiful Soup</a> library to parse the html.
Wait for it.....
When I tried to parse, I came to know that beautifulsoup also supports parsing the xml(rss). Nice.
Now I wrote a script to parse them and display in the terminal.
The search argument is parsed using argparse library.

The output is sh*ty,
So, I used <a href="https://pypi.org/project/terminaltables/" target="_blank">terminal tables</a> library to get the clean output with the tables.

# Output
![output](https://i.ibb.co/d5gxSZf/photo-2019-04-01-13-42-19.jpg)

The script is given below.

{{<highlight python>}}
# Created by Selva <selva@selvasoft.in>
# © Selva
import requests
import argparse
import sys
from bs4 import BeautifulSoup
from terminaltables import SingleTable
from textwrap import wrap

baseURL = 'https://www.skytorrents.to/rss?search='

# Using in-built argparse library to parse the command line arguments 
parser = argparse.ArgumentParser(description="Search torrents from the command line easily.")
parser.add_argument('-i','--items',default=5,help='Number of items to display ( default: 5 )')
parser.add_argument('searchTerm',type=str,help='Search term to search for torrent')
args = parser.parse_args()
# Replacing the blank space(" ") with "+", since the URL doesnt have any blank space
searchTerm = args.searchTerm.replace(' ','+')
num = args.items
r = requests.get(baseURL+searchTerm)
soup = BeautifulSoup(r.text,'xml')
items = soup.find_all('item')
if len(items)==0:
    print('Item not found!!!')
    sys.exit()

def convertSize(size):
    size = round(size / 1024,2)
    suffix = 'KB'
    if size > 1000:
        size = round(size / 1024,2)
        suffix = 'MB'
    if size > 1000:
        size = round(size / 1024,2)
        suffix = 'GB'    
    if size > 1000:
        size = round(size / 1024,2)
        suffix = 'TB'
    return ( str(size) + ' ' + suffix )

list1 = [ ]
mgntList = [ ]
list1.append(['Index','Title','Date Added','Size','Seeders','Leechers'])
for i in range(int(num)):
    item = items[i]
    tempList = [ ]
    tempList.append(str(i+1))
    tempList.append(item.title.text)
    mgntList.append(item.magneturl.text)
    tempList.append(item.pubDate.text[5:16])
    tempList.append(convertSize(int(item.size.text)))
    seeders = item.find(attrs={'name':'seeders'})
    tempList.append(seeders['value'])
    peers = item.find(attrs={'name':'peers'})
    tempList.append(peers['value'])
    list1.append(tempList)


cuteTable = SingleTable(list1)
max_width = cuteTable.column_max_width(1)
for i in range(1,len(list1)):
    row = list1[i]
    wrappedString = '\n'.join(wrap(row[1], max_width))
    cuteTable.table_data[i][1] = wrappedString +'\n'
    
print(cuteTable.table)
choice = int(input('\nEnter the index--->'))
print('\nThe magnet link is :\n')
print(mgntList[choice])
{{</highlight>}}

Any doubts? Contact me. ( Social Links are in the home page )
[Homepage](https://selvasoft.in)


