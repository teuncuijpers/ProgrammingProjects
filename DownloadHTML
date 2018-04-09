#download and save to db full website html

import requests
import dataset
from time import sleep

db = dataset.connect("sqlite:///Data.db")
table1 = db['Table1']

fails = []
for counter, site in enumerate(table1): 

    try: 
        #grab info from db
        url = site['url']
        Id = site['id']
        page = requests.get(url)#load website
        html = page.text #this is the full html file that can be processed with bs
        table1.update(dict(id = Id, html = html), ['id']) # put that in the db
        #counter to see where we are.
        print(counter)
        #wait for a sec to possibly avoid rate limiting by server. Makes this script horribly slow though
        sleep(1)
        
    except: 
        #make and print a list of all the instances where the script did not work
        fails.append(site['id'])
        print('FAIL')
        

print(fails) #print all pages for which it did not work
