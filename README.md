# Access.log-Analysis-via-python


Analysing the logs for various requirements is a common process that we do during our work. So here are some methods to analyse the access.log making use of python. Access log simply has a list of all inbound requests, formatted to allow you to consume them easily 

## Requirements

- Install python

- Install jupytor-notebook
 ```
  # apt-get update
  # apt-get install python3-pip
  # pip3 install jupyter
  
 ```
- logparser.py module
- Matplotlib

 ```
  # sudo apt-get install python3-matplotlib
 ```

## Prerequisite

  
  - Configure jupytor-notebook
  ```
    # jupyter notebook --generate-config  
    # vi /home/ubuntu/.jupyter/jupyter_notebook_config.py
      enable "c.NotebookApp.allow_root = True"
  ```
  - Create a directory "python-log"
  ```
    # mkdir python-log
  ```
  
  - The logparser.py module and access.log should be placed in the working directory "python-log".


## Usage

```
import logparser

logFile = open("access.log","r")

for logLine in logFile:
      
  print(logparser.parser(logLine))            
  print()
    
  break
```

At first we should import the logparser module in order to process the access.log propperly. Here the access log is converted to a dictionary with keys as various feilds and value as its corresponding values using the logparser module by iterating through the log contets.

![logparser](https://user-images.githubusercontent.com/94472101/152866726-4854e187-72d6-45c7-9968-c1d7d7628b41.png)

## Arranging the Access log

Here we will arrange the access.log into a dictionary which is written inside tripple quote for our sake of convenience.

```
"""
{ 
  'host': '157.48.153.185', 
  'identity': '-', 
  'user': '-', 
  'time': '19/Dec/2020:14:08:08 +0100', 
  'request': 'GET /favicon.ico HTTP/1.1', 
  'status': '404', 
  'size': '217', 
  'referer': 'http://www.almhuette-raith.at/apache-log/access.log', 
  'agent': 'Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) 
         Chrome/87.0.4280.88 Safari/537.36'
  
}
"""
```

## Counting the Hits Per IP basis

```

import logparser

logFile = open("access.log","r")

ipCounter = {}


for logLine in logFile:
    
  logParts = logparser.parser(logLine)
  
  host = logParts["host"]

  if host not in ipCounter:
        
    ipCounter[host] = 1
    
  else:
    
    ipCounter[host] = ipCounter[host] + 1         ## ip is listed in the dict ipCounter ##
    

for ip in ipCounter:                              ## calculating the ip count ##
    
  freq = ipCounter[ip] 

  if freq >= 1000:
        
    print('{:18} -  {}'.format(ip,freq))
```
Output

![hitsperip](https://user-images.githubusercontent.com/94472101/152868774-46c55325-3fc9-4d1f-bb56-87d0f57d49e8.png)


Initially the logfile is set to readable format and an empty dictionary "ipCounter" is created.

## Counting Hits Per Day

```
import logparser

logFile = open("access.log","r")

hitCounter = {}


for logLine in logFile:
    
  logParts = logparser.parser(logLine)
  
  date = logParts['time'][0:11]
  
  if date not in hitCounter:
    
    hitCounter[date] = 1
    
  else:
    
    hitCounter[date] = hitCounter[date] + 1
    
    
for date in hitCounter:
    
  freq = hitCounter[date]
  
  print("{} - {}".format(date,freq))
```
Output

![hitsperday](https://user-images.githubusercontent.com/94472101/152870114-0cbccc49-fcb9-400d-8b0b-e77c699c3b1e.png)


## Counting the number of 404 Status Per day

```
import logparser

logFile = open("access.log","r")

hitCounter = {}
httpStatus = "404"

for logLine in logFile:
    
  logParts = logparser.parser(logLine)
  
  date = logParts['time'][0:11]
  status = logParts['status']
    
  if status == httpStatus:
    
    if date not in hitCounter:
    
      hitCounter[date] = 1
    
    else:
    
      hitCounter[date] = hitCounter[date] + 1
    
    
for date in hitCounter:
    
  freq = hitCounter[date]
  
  print("{} - {}".format(date,freq))
```
Output

![404 status](https://user-images.githubusercontent.com/94472101/152871286-602ce66d-147a-4bfb-b4a1-3e17219407b0.png)

## Counting the 404 path and its frequency

```
import logparser

logFile = open("access.log","r")

freqcounter = {}
httpStatus = "404"

for logLine in logFile:

  logparts = logparser.parser(logLine)

  path = logparts['request'].split()[1]
  status = logparts['status']
  
  if status == "404":
    
    if path not in freqcounter:
        
      freqcounter[path] = 1
    
    else:
        
      freqcounter[path] = freqcounter[path] + 1

for path in freqcounter:

  freq = freqcounter[path]
    
  if freq >= 10:
    
    print("Path: {:60} - Freq: {}".format(path,freq))
    
```
Output

![404path](https://user-images.githubusercontent.com/94472101/152871356-b2b8ff31-7890-4a5f-8521-c62b09d1e8d1.png)



## Counting hits per hour on a particular day

```
import logparser



logFile = open("access.log","r")

freqCounter = {} # {  '19/Dec/2020':{ 14:1 } }


for logLine in logFile:
    
  logParts = logparser.parser(logLine)

  date = logParts["time"][0:11]
  hour = logParts["time"][12:14]

  if date == '19/Dec/2020':

    if date not in freqCounter:
        
      freqCounter[date] = { hour:1 }
  
    else:
    
      if hour not in freqCounter[date]:
       
        freqCounter[date][hour] = 1
        
      else:
        
        freqCounter[date][hour] = freqCounter[date][hour] + 1
    
print(freqCounter)    
```
Output

![hitsperhour](https://user-images.githubusercontent.com/94472101/152871044-83354de5-e458-4b19-9491-2ae34351e9e0.png)


## Counting hits per hour for some days

```
import logparser



logFile = open("access.log","r")

freqCounter = {} # {  '19/Dec/2020':{ 14:1 } }


for logLine in logFile:
    
  logParts = logparser.parser(logLine)

  date = logParts["time"][0:11]
  hour = logParts["time"][12:14]



  if date not in freqCounter:
        
    freqCounter[date] = { hour:1 }
  
  else:
    
    if hour not in freqCounter[date]:
       
      freqCounter[date][hour] = 1
        
    else:
        
      freqCounter[date][hour] = freqCounter[date][hour] + 1
    
    
for date in freqCounter:
  

  print(date)
  print()

  hourDict = freqCounter[date]

  for hour in hourDict:
        
     freq = hourDict[hour]
     
     print('  {:2} - {}'.format(hour,freq))
    
  print()
```
Output

![log](https://user-images.githubusercontent.com/94472101/152872473-4bf5171c-6f1b-4fcc-a565-55e38879d403.png)

![log2](https://user-images.githubusercontent.com/94472101/152872516-94d97b27-f32d-4162-97ad-27eec3e07fa6.png)

![log3](https://user-images.githubusercontent.com/94472101/152872558-87a94de3-e9bf-4944-a4d9-95fdf5815164.png)


