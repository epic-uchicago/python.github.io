```python
#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#
## Purpose: scrape data files for the RA Data Project
# http://www.jasmiths.com/
#
## Author: Andrew Smith (jas3@uchicago.edu)
#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#
import os
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

path = "/Users/nadialucas/Dropbox/epic_task/rawdata/txt"
os.chdir(path)

# Removing files from directory (if necessary)
files = ["demographics.txt","house_age1.txt","house_age2.txt","house_chars1.txt","house_chars2.txt","sample82.txt"]
for f in files:
    filepath = os.path.join(path, f)

    if os.path.exists(filepath):
        os.remove(filepath)

# Setting wait time, search engine in use
waiting = 10
engine = 'chrome'

# Setting downloading preferences
chromeOptions = Options()
prefs = {"download.default_directory" : path}
chromeOptions.add_experimental_option("prefs",prefs)

# Driver info
chromedriver = "/Users/nadialucas/anaconda/bin/chromedriver"
driver = webdriver.Chrome(executable_path = chromedriver, chrome_options = chromeOptions)

print('Opening Chrome')
driver.get("http://www.jasmiths.com/data_project")

# Downloading data
print("Downloading Data Files")

for i in range(len(files)):
        # Clicking on the download link
        WebDriverWait(driver,waiting).until(EC.element_to_be_clickable((By.XPATH,"""/html/body/main/div/ol/li[{}]/a""".format(i+1)))).click()

        # We wait to perform another task so that we have ample time to download the files
        time.sleep(waiting)


print("Quitting Chrome")
driver.quit()

```