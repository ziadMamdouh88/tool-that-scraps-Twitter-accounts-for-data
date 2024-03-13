Twitter Stock Symbol Scraper using Selenium.

This script uses Selenium to automate the process of scraping Twitter for mentions of specific stock symbols
in tweets from a list of Twitter account URLs. It is designed to be run periodically to track mentions
of stock symbols over time.

Author:Ziad Mamdouh
Date: 13/3/2023
"""

import re
from time import sleep
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import WebDriverWait

def initialize_webdriver():
    """
    Initializes and returns a Firefox WebDriver.
    Make sure GeckoDriver is installed and available in the system path.
    """
    return webdriver.Firefox()

def scrape_twitter(accounts, ticker, interval_minutes):
    """
    Scrapes Twitter accounts for mentions of a specific stock symbol.
    Parameters:
    - accounts: List of Twitter account URLs to scrape.
    - ticker: The stock symbol to search for, formatted as "$SYMB".
    - interval_minutes: The time interval in minutes for scraping sessions.
    Returns:
    - The number of times the stock symbol was mentioned across the accounts.
    """
    driver = initialize_webdriver()
    wait = WebDriverWait(driver, 10)
    ticker_mentions = 0
    pattern = re.compile(f"\\{ticker}\\b", re.IGNORECASE)
    for account_url in accounts:
        driver.get(account_url)
        sleep(3)  # Wait for the initial page load.
        # Scroll the page to load more tweets.
        body = driver.find_element(By.CSS_SELECTOR, 'body')
        for _ in range(3):  # Adjust the range as needed for deeper scrolling.
            body.send_keys(Keys.PAGE_DOWN)
            sleep(1)
            # Find all posts with the specified XPath.
        posts = driver.find_elements(By.XPATH, '//a[@role="link" and @dir="ltr"]')
        for post in posts:
            if pattern.search(post.text):
                ticker_mentions += 1
    return ticker_mentions


    # Example usage:
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

