# to-recreate-the-Web-Scraping-Project-
import requests
from bs4 import BeautifulSoup
import sqlite3

# Database setup
def create_database():
    conn = sqlite3.connect('scraped_data.db')
    cursor = conn.cursor()
    
    # Create table
    cursor.execute('''CREATE TABLE IF NOT EXISTS data (
                        id INTEGER PRIMARY KEY AUTOINCREMENT,
                        title TEXT NOT NULL,
                        link TEXT NOT NULL,
                        summary TEXT NOT NULL
                    )''')
    
    conn.commit()
    return conn

# Function to save data into the database
def save_to_database(conn, title, link, summary):
    cursor = conn.cursor()
    cursor.execute("INSERT INTO data (title, link, summary) VALUES (?, ?, ?)", (title, link, summary))
    conn.commit()

# Function to scrape data from a website
def scrape_data(url):
    response = requests.get(url)
    soup = BeautifulSoup(response.text, 'html.parser')
    
    # Example: Assuming the website has articles with title, link, and summary
    articles = soup.find_all('article')
    scraped_data = []

    for article in articles:
        title = article.find('h2').text
        link = article.find('a')['href']
        summary = article.find('p').text
        
        scraped_data.append((title, link, summary))
    
    return scraped_data

# Main function to orchestrate the scraping and saving process
def main():
    url = 'https://example.com/articles'  # Replace with the actual URL to scrape
    conn = create_database()
    
    scraped_data = scrape_data(url)
    
    for data in scraped_data:
        save_to_database(conn, *data)
    
    print("Data scraped and saved to the database successfully.")
    
    conn.close()

if _name_ == "_main_":
    main()
