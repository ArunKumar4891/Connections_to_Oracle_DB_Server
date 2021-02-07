# Connections_to_Oracle_DB_Server  

please follow the below steps connecting to oracle DB server from application servers

- Once logged into Linux server, install python 3.6 or above
- install PIP for downloading/installing libraries
- check for the connectivity (like network, firewall, port, ping) from python installed server( application/destination server) to Oracle DB server (target server)
- perform the below python coding and save the script as .py file (example: connect.py)
- run the script the get the output in python (python3# connect.py)


Please follow the below steps installation Python 

# Python Installation in Redhat Linux: 

# Start by making sure your system is up-to-date:
yum update

# Compilers and related tools:
yum groupinstall -y "development tools"

# Libraries needed during compilation to enable all features of Python:
yum install -y zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel expat-devel

# If you are on a clean "minimal" install of CentOS you also need the wget tool:
yum install -y wget

# Python 3.6.3:
tar xf Python-3.6.3.tar.xz
cd Python-3.6.3
./configure --prefix=/usr/local --enable-shared LDFLAGS="-Wl,-rpath /usr/local/lib"
make && make altinstall

# Strip the Python 3.6 binary:
strip /usr/local/lib/libpython3.6m.so.1.0

# Then execute it using Python 2.7 and/or Python 3.6:  (download the get-pip.py from https://bootstrap.pypa.io/get-pip.py)
python3.6 get-pip.py

# yum info python34 pythonu
Loaded plugins: product-id, search-disabled-repos, security, subscription-manager
Installed Packages
Name        : python34
Arch        : x86_64
Version     : 3.4.10
Release     : 4.el6
Size        : 31 k
Repo        : installed
From repo   : epel
Summary     : Version 3 of the Python programming language aka Python 3000
URL         : http://www.python.org/
License     : Python
Description : Python 3 is a new version of the language that is incompatible with the 2.x
            : line of releases. The language is mostly the same, but many details, especially
            : how built-in objects like dictionaries and strings work, have changed
            : considerably, and a lot of deprecated features have finally been removed.

Available Packages
Name        : python34
Arch        : i686
Version     : 3.4.10
Release     : 4.el6
Size        : 51 k
Repo        : epel
Summary     : Version 3 of the Python programming language aka Python 3000
URL         : http://www.python.org/
License     : Python
Description : Python 3 is a new version of the language that is incompatible with the 2.x
            : line of releases. The language is mostly the same, but many details, especially
            : how built-in objects like dictionaries and strings work, have changed
            : considerably, and a lot of deprecated features have finally been removed.


# yum install epel-release
Loaded plugins: product-id, search-disabled-repos, security, subscription-manager
Setting up Install Process
Package epel-release-6-8.noarch already installed and latest version
Nothing to do


# yum install python-pip
Loaded plugins: product-id, search-disabled-repos, security, subscription-manager
Setting up Install Process
Package python-pip-7.1.0-2.el6.noarch already installed and latest version
Nothing to do




# connection to Oracle Database server 

# connect from Application server (RedHat Linux server) to Oracle TDW DB server (RedHat Linux server) through python.

import cx_Oracle
import pandas as pd
import datetime

try:
    conn = cx_Oracle.connect("<UserName>", "<password>", "<DataBase Name>:1621/<table name")   # conect to Oracle DB server
    
    """
    UserName : oracle TDW DB server username, 
    password: oracle TDW DB server password, 
    ServerName : "DB name :Port#/<table name")
    
    """

except Exception as err:
    print("Exception error while creating the connection", err)   # if connection failed, it will print as exception error

else:
    try:
        cur = conn.cursor()
        maxdate = str(datetime.datetime.today() - datetime.timedelta(days=30))

        query_timestamp ="1" + maxdate[0:2] + maxdate[5:7] + maxdate[8:10] + maxdate[11:13] + maxdate[14:16] + maxdate[17:19] + maxdate[20:23]

        sql =  """SELECT distinct "<ColumnName1>" FROM "<tablename>" """ + """ where 'Timestamp'> '""" + query_timestamp + """' and "<Columname1" in ('Server1, Server2, Server3, Server4, etc...') and "CPU_ID"='-1' """
        print(sql)
        result= cur.execute(sql)
        colList = [tuple[0] for tuple in result.description]
        row = cur.fetchall()
        rawdata= pd.DataFrame.from_records(row,columns=colList).copy()
        rawdata.to_csv('TDW_Output_Hourly.csv', sep = ",")
        #print(row)

        for index, record in enumerate(row):
            print("Index is ", index, ':', record)
    except Exception as err:
        print("Exception error while fetching the records", err)    # if records failed, it will print as exception error

    else:
        print("Completed ")
