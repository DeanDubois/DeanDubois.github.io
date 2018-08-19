## Welcome to Dean Dubois' ePortfolio

### Software Design and Engineering
```markdown
import json
from bson import json_util
import bottle
from bottle import route, run, request, abort
import datetime
import pymongo

from pymongo import MongoClient
from alpha_vantage.timeseries import TimeSeries
from node import 
from databases import 


ts = TimeSeries(key='6HQRIH70OOR9FDN4', output_format='pandas')
names = Null


#RESTFUL Get Stock based on ticker
@route('/getStock', method="GET")
def get_stock():
    try:
        ticker = request.query.ticker
        #return json.dumps(read_one_document({"Ticker": ticker}), default=json_util.default, sort_keys=True, indent=4, separators=(',', ': '))
        data, metadata = ts.get_daily_adjusted(symbol=ticker, outputsize='full')
        d = data.to_dict()
        

        return createGraph(d, ticker, "", "") + data.tail(100).to_html()
    except Exception, ve:
        #return str(ve)
        return "<form> Ticker:<br> <input type=\"text\" name=\"ticker\"><br> <input type=\"submit\" value=\"Submit\"></form><br>No Data Found!</br>"

    
if __name__ == '__main__':
    names = retrievenames()
    #print searchnode("GOOG", a).companyname
    run(host='localhost', port = 8080, debug=True, reloader=True)
```


```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```


