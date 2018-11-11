```python
#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#
## Purpose: scrape data files for the RA Data Project
# http://www.jasmiths.com/
#
## Author: Andrew Smith (jas3@uchicago.edu)
#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#
import os
import getpass
import time
import shutil
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import Select
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.desired_capabilities import DesiredCapabilities
from selenium.common.exceptions import NoSuchElementException, TimeoutException, NoSuchFrameException, WebDriverException

# we want to create a class that is an object defined as ChromeDriver
class ChromeDriver():
    # an __init__ method takes in all arguments that are needed to feed into the class
    # including the argument self which is used to refer to the instance of the object, ChromeDriver,
    # within the class itself

    def __init__ (self, file_folder, chromedriver_path, files, data_url):
        self.file_folder = file_folder
        self.chromedriver_path = chromedriver_path
        self.files = files
        self.data_url = data_url
        # Setting wait time, search engine in use
        self.waiting = 15
        self.engine = 'chrome'
        # setup() is a constructor function
        # we need to call it in order to clean the folder of files
        # as well as initiate the chromedriver
        self.setup()

    def setup(self):
        os.chdir(self.file_folder)

        # Removing files from directory (if necessary)
        
        for f in self.files:
            folder = self.file_folder
            filepath = os.path.join(self.file_folder, f)

            if os.path.exists(filepath):
                os.remove(filepath)
        # Setting downloading preferences
        chromeOptions = Options()
        prefs = {"download.default_directory" : self.file_folder}
        chromeOptions.add_experimental_option("prefs",prefs)

        self.driver = webdriver.Chrome(executable_path = self.chromedriver_path, chrome_options = chromeOptions)

    # get_data() is called on any instance of the object itself in order to actually scrape
    # all relevant data and put it into the right folders 
    def get_data(self):
        # Opening
        print('Opening Chrome')
        self.driver.get(self.data_url)

        # Downloading data
        print("Downloading Data Files")
        for i in range(len(self.files)):
            # Clicking on the download link
            WebDriverWait(self.driver,self.waiting).until(EC.element_to_be_clickable((By.XPATH,"""/html/body/main/div/ol/li[{}]/a""".format(i+1)))).click()

            # We wait to perform another task so that we have ample time to download the files
            time.sleep(self.waiting)


```



The following script instantiates a ChromeDriver object and scrapes within the class given the path and filename arguments specific to your machine and task at hand.

```python
import os
import getpass
from ChromeDriver import ChromeDriver

# the following if statement 
if getpass.getuser()=="nadialucas":
    file_folder = r"/Users/nadialucas/Documents/EPIC/2018/orientation_sessions/txt"
    # copy the path after typing "which chromedriver" into your command-line
    chromedriver_path = r"/Users/nadialucas/anaconda/bin/chromedriver" 
files = ["demographics.txt","house_age.txt","house_chars1.txt","house_chars2.txt","house_type.txt"]
data_url = "http://www.jasmiths.com/data_project"
driverObj = ChromeDriver(file_folder, chromedriver_path, files, data_url)
driverObj.get_data()
```