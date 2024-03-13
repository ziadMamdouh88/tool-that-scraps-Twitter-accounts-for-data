# tool-that-scraps-Twitter-accounts-for-data
Python code to create a tool that scraps Twitter accounts for data.
#Imports the Selenium WebDriver, which is essential for automating web browser interaction from Python.
#By: Used to locate elements within a webpage. In this script, it helps find tweet elements.
#sleep: Pauses the execution of the script for a specified amount of time, allowing web pages to load.
#re: Provides regular expression matching operations. It's used to search for the stock symbol within the text of Posts.
#Keys: in a Selenium script is used to import the Keys
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from time import sleep
from selenium.webdriver.common.keys import Keys
import re

#This code initializes the Firefox WebDriver using GeckoDriver
driver = webdriver.Firefox()
wait = WebDriverWait(driver, 10)

#Parameters: The function accepts a list of Twitter account URLs (accounts), a stock symbol (ticker), and a time interval in minutes (interval_minutes).
def scrape_twitter(accounts, ticker, interval_minutes):
    ticker_mentions = 0
    pattern = re.compile(f"\\{ticker}\\b", re.IGNORECASE)

   for account_url in accounts:
        driver.get(account_url)
        sleep(3)  # Initial sleep for the page to load
          # Simple scrolling to load more tweets
          body = driver.find_element(By.CSS_SELECTOR, 'body')
          for _ in range(3):  # Adjust the range as needed
            body.send_keys(Keys.PAGE_DOWN)
            sleep(1)  # Short sleep between scrolls
      # the xpath link: //a[@role="link" and @dir="ltr"]
          posts = driver.find_elements(By.XPATH, '//a[@role="link" and @dir="ltr"]')
          for tweet in posts:
            if pattern.search(tweet.text):
                ticker_mentions += 1

  return ticker_mentions


# Example usage with specific accounts:
accounts = [
    'https://twitter.com/Mr_Derivatives',
    'https://twitter.com/warrior_0719',
    'https://twitter.com/ChartingProdigy',
    'https://twitter.com/allstarcharts',
    'https://twitter.com/yuriymatso',
    'https://twitter.com/TriggerTrades',
    'https://twitter.com/AdamMancini4',
    'https://twitter.com/CordovaTrades',
    'https://twitter.com/Barchart',
    'https://twitter.com/RoyLMattox'
]
ticker = "$TSLA"  # Stock symbol to look for
interval_minutes = 15  # How often to scrape, in minutes

# Call the function and output the result
mentions = scrape_twitter(accounts, ticker, interval_minutes)
print(f"{ticker} was mentioned {mentions} times in the last {interval_minutes} minutes.")

# to close the browser
driver.quit()

