---
layout: post
title:  "Creating My First Data Extractor in Python"
date:   2021-09-22
tags: [Python, Data Engineering]
category: [Data]
---

# Data Extraction with SAP HANA

My work typically entails creating a number of calculated views and data sets using SAP HANA and HANA Studio. However, extracting data into a flat file was not always straight forward in HANA Studio. In an effor to learn some Python I set to creating a simple data extractor from HANA.

Using SAP's [hdbcli](https://pypi.org/project/hdbcli/) this project sets to make it easy to extract data via SQL statements passed through and attempt to automate as many pieces as possible. 

In addition to `hdbcli` a few other requirements are set in [requirements.txt](https://github.com/GD-L/hana-exporter/blob/main/code/requirements.txt). Making use of: `tkinter`, `pandas`, `sys`, `getpass`, `dotenv`, and `os`.

The script can be set with a few paramaters:

1) `.env` file to provide a default Server and Port:
    ```
    SERVER={$SERVER}
    PORT={$PORT}
    ```
2) `main()` can be manually configured with login information ([See Code](https://github.com/GD-L/hana-exporter/blob/main/code/pipeline.py#L52)):
    ```
    server = ""
    port = ""
    user_name = ""
    password = ""
    ```

The overall operation takes the flow of:

1) Login - Tests for default environment values, including server, port, username, and password. These can be fixed, `.env` values, or input using argument flags, e.g. `-u` or `--username`. 
2) Input - A `.sql` or `.txt` file with SQL code can be used or a query can be entered manually
3) Output - Results can be printed to the screen or saved to a flat-file (e.g. `.csv`).

Full code below:

``` python
import argparse
import sys
from getpass import getpass
from dotenv import load_dotenv
import os

import pandas as pd
from hdbcli import dbapi

# importing all files  from tkinter
from tkinter import * 
from tkinter import ttk
  
# import only asksaveasfile from filedialog
# which is used to save file in any extension
from tkinter import filedialog

load_dotenv()
default_url = os.getenv("SERVER")
default_port = os.getenv("PORT")


def connection(server,port,username,password):
    conn = dbapi.connect(address = args.server, port = args.port, user=args.username, password = args.password)
    print("\n Connected  to " + args.server + " \n")
    return(conn)

def execute_query(query,conn):
    data = pd.read_sql_query(query,conn)
    #print(data.head(100))
    return(data)

def write_csv(data):
    files = [("CSV", '*.csv'),
            ("All Files","*.*")]
    file_path = filedialog.asksaveasfilename(filetypes=files)
    data.to_csv(file_path, index = False)

def read_input():
    files = [("Text","*.txt"),("SQL","*.sql"),("All Files","*.*")]
    file_path = filedialog.askopenfilename(filetypes = files)
    read_file = open(file_path,"r")
    sql_input = read_file.read()
    return sql_input, file_path; 



def main(args):
    """
    To automate this script then fill in the values for server, username, etc
    You will be prompted for any values set to ""

    Server and username can be entered on the command line as well.

    """
    
    server = ""
    port = ""
    user_name = ""
    password = ""
    
    root = Tk()
    root.withdraw()

    print(args)

    if server != "":
        args.server = server
    
    if port != "":
        args.port = port

    if user_name !="":
        args.username = user_name
    elif args.username == None:
        args.username = input("Enter Username: \n")

    if password != "":
        args.password = password
    elif args.password == None:
        args.password = getpass("Enter Password: \n")

    print(args)
    conn = connection(args.server,args.port,args.username,args.password)
    source = input("Would you like to read from a file? (y/n) \n")
    if source.upper() == "Y":
        sql_query, file_path = read_input()
        print("Reading file "+str(file_path))
    else: sql_query = input("Enter SQL Query:")

    data = execute_query(sql_query, conn)

    print_or_export = input("Would you like to export data (y/n)\n")
    if print_or_export.upper() == 'Y':
        write_csv(data)

    else:
        print(data)

if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument("-s", "--server" , help = "Server URL",default=default_url, dest = "server", type=str)
    parser.add_argument("-p", "--port" , help = "Port",default=default_port, dest = "port")
    parser.add_argument("-u", "--username", help = "Username", dest = "username", type=str)
    parser.add_argument("-P","--Password", help = "Password", dest = "password")
    args = parser.parse_args()
    main(args)
```