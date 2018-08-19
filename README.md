# Welcome to Dean Dubois' ePortfolio



## Software Design and Engineering
```markdown
import json
from bson import json_util
import bottle
from bottle import route, run, request, abort
import datetime
import pymongo

from pymongo import MongoClient
from alpha_vantage.timeseries import TimeSeries
from node import *
from databases import *

#Key for accessing the stock data
ts = TimeSeries(key='6HQRIH70OOR9FDN4', output_format='pandas')
#Call retrieve names in order to get all of the names of each ticker in the market
names = retrievenames()


#RESTFul search for stock and display data if found
@route('/getStock', method="GET")
def get_stock():
    try:
        #Get the ticker name that was passed in by the user
        ticker = request.query.ticker
        #Retrieve the data for the ticker that the user requested
        data, metadata = ts.get_daily_adjusted(symbol=ticker, outputsize='full')
        d = data.to_dict()
        #Find the full name of the company based on the ticker
        a = searchnode(ticker, names).companyname
        #Return HTML to show the user the results
        return createGraph(d, a, "", "") +"<br>History Of Past 100 Days</br>" +data.tail(100).iloc[::-1].to_html()
    except Exception, ve:
        return "<form> Ticker:<br> <input type=\"text\" name=\"ticker\"><br> <input type=\"submit\" value=\"Submit\"></form><br>No Data Found!\n"+ str(ve) +"</br>"

#Main function called when program started
if __name__ == '__main__':
    run(host='localhost', port = 8080, debug=True, reloader=True)

```

### Software Design and Engineering Narrative
    The first artifact shown above was taken from a final project in a previous course. The goal of this project was to create a RESTful API in which stock information could be requested through a browser and the data would be pulled from a database with fixed information. I chose this item to improve uppon because I knew it was something that could not be used in the real world due to the fact that the stock information was not up to date. 


## Algorithms and Data Structures
```markdown
import urllib
from operator import attrgetter

#Node Class for storing ticker and company name
class node:
    def __init__(self, t, c):
        self.ticker = t
        self.companyname = c
    

#This function is in place to search a given node for the ticker given and then returns that node
def searchnode(search, n):
    
    if(len(n) > 1):
        if n[len(n)/2].ticker == search:
            return n[len(n)/2]
        elif n[len(n)/2].ticker < search:
            return searchnode(search, n[len(n)/2:])
        else:
            return searchnode(search, n[0:len(n)/2])
    else:
        if(n[0].ticker == search):
            return n
        else:
            return node("None", "None")

#This function retrieves files from nasdaq and parses them to find the names of the companies
def retrievenames():
    
    urllib.urlretrieve('ftp://ftp.nasdaqtrader.com/SymbolDirectory/otherlisted.txt', 'other listed')
    urllib.urlretrieve('ftp://ftp.nasdaqtrader.com/SymbolDirectory/nasdaqlisted.txt', 'nasdaq listed')

    f = open("other listed","r")
    nde = []
    f.readline()
    for line in f:
        if "File Creation Time:" not in line:
            tmp = line.split("|")
            nde.append(node(tmp[7].strip("\n").strip("\r"), tmp[1]))

    f.close()
    f = open("nasdaq listed","r")
    f.readline()
    for line in f:
        if "File Creation Time:" not in line:
            tmp = line.split("|")
            nde.append(node(tmp[0], tmp[1]))

    f.close()
    a = sorted(nde, key=attrgetter("ticker"))
    return a

```

### Algorithms and Data Structures Narrative

## Databases
```markdown
#Function that creates a graph that indicates price over time as well as shows the data for the passed 100 days
def createGraph(data, ticker, title, subtitle):
    html = "<form> Ticker:<br> <input type=\"text\" name=\"ticker\"><br> <input type=\"submit\" value=\"Submit\"></form>" + """<head>
                <script type=\"text/javascript\" src=\"https://www.gstatic.com/charts/loader.js\"></script>
                <script type=\"text/javascript\">
                  google.charts.load('current', {'packages':['corechart']});
                  google.charts.setOnLoadCallback(drawChart);

                function drawChart() {

                  var data = new google.visualization.arrayToDataTable([['Date', 'Price'],"""
    datastr = ""
    for a in data:
        if str(a) == '5. adjusted close':
            for b in data[a]:
                datastr = datastr + "[ new Date('" + str(b) + "') , " + str(data[a][b]) + "],"
    datastr = datastr[:-1]
    html = html + datastr + """]);

                  var options = {
                  
                    title: 'Price of """ + ticker + """',
                      
                    
                    width: 900,
                    height: 500,
                    pointSize: 1
                  };

                  var chart = new google.visualization.ScatterChart(document.getElementById('linechart_material'));

                  chart.draw(data, options);
                }
                </script>
              </head>
              <body>
                <div id=\"linechart_material\" style=\"width: 1000px; height: 600px\"></div>
              </body>"""
    return html

```


### Databases Narrative
