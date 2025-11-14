Sentiment Analysis & Fake Reveiw Detection

This project uses Natural Language Processing (NLP) and Machine Learning to classify user reviews by Sentiment
(positive or negative) and detect potentially fake reviews. Built with Python and trained on the Yelp dataset

Features
- Sentiment classification (positive/negative)
- Fake review detection using NLP + ML
- Preprocessing pipeline for text data
- Train model ready for inference

Sentiment-Analysis/
-Data : Contains sample datasets (e.g Yelp Reviews)
notebooks : Jupyter notebooks for EDA and modeling
models : Trained model files (.pk1, .h5, etc.)
app : Flask or Streamlit app code


Summary of this project

Phase 1: Model Development and Training (in Google Colab)

Goal: Train a machine learning model to classify reviews as genuine or fake. Steps: Set up the Colab environment, installing necessary libraries like nltk, tensorflow, keras, etc. Mounted Google Drive to access your dataset (fake reviews dataset.csv). Loaded the dataset into a pandas DataFrame. Preprocessed the review text data (cleaning, lowercasing, removing punctuation, stemming). Tokenized the text data and padded sequences to prepare it for the neural network. Encoded the labels ('CG', 'OR') into numerical format using LabelEncoder. Split the data into training and testing sets. Built and trained a deep learning model using TensorFlow/Keras, specifically an LSTM network suitable for sequence data like text. Evaluated the model's performance using metrics like accuracy, precision, recall, and F1-score, and visualized the training progress and confusion matrix. Saved the trained Keras model (fake_review_model.h5) and the fitted Tokenizer object (tokenizer.pickle) to your Google Drive. These saved artifacts are crucial for using the model outside of the Colab environment.

Phase 2: Setting up the Local Prediction Server (using Flask)

Goal: Create a local web server that can load the trained model and tokenizer and provide an API endpoint for receiving review text and returning predictions. Steps: Discussed the concept of a web server (like Flask) to serve the model. Generated the initial Python code for a basic Flask application (app1.py). This script included loading the saved fake_review_model.h5 and tokenizer.pickle files. Added a Flask route (/predict_review) that accepts POST requests, extracts review_text from the request body, preprocesses the text, uses the loaded model to make a prediction, converts the numerical prediction back to 'CG' or 'OR', and returns the result as a JSON response. Troubleshooted initial errors related to file paths and loading the model/tokenizer locally. Enhanced Flask server error handling within the /predict_review endpoint to provide more informative error messages for invalid input or processing issues. Integrated structured logging into the Flask application (app1.py) for monitoring requests and internal processes. Transitioned from the Flask development server to using waitress-serve app1:app locally for a more production-like way to run the server, which is more robust than the built-in development server.

Phase 3: Developing the Local Web Scraping Script

Goal: Create a standalone Python script that can scrape review data from external websites (starting with BestBuy) using Selenium. Steps: Discussed the need for web scraping to get external reviews. Provided initial code for a Selenium-based scraper (bestbuy_scraper.py) to extract review titles and text from a BestBuy product page. This involved using webdriver-manager to handle the Chrome WebDriver. Encountered and discussed common issues with running Selenium in different environments (like Colab vs. local). Introduced the concept of a configuration file (scraper_config.json) to make the scraper more flexible and adaptable to different websites by externalizing selectors and parameters. Modified the scraping script (bestbuy_scraper.py) to load and use the configuration from scraper_config.json. Enhanced the scraping script with error handling (e.g., try...except blocks for Selenium exceptions, retry logic) to make it more robust against network issues or website changes.

Phase 4: Integrating Scraping, Prediction, and Data Storage

Goal: Create a "bridge" script that connects the scraper and the prediction server, and then add functionality to save the results persistently. Steps: Created the predict_scraped_reviews.py script. This script imports functions from bestbuy_scraper.py to get the reviews. Added logic to predict_scraped_reviews.py to iterate through the scraped reviews and send each one as a POST request to the /predict_review endpoint on your running Flask server. Discussed different data storage options (Flat files vs. Databases - SQLite, PostgreSQL). Decided to use SQLite as a starting point for ease of implementation locally. Generated a Python script (setup_database.py) to create an SQLite database file (reviews.db) and a table (scraped_reviews) to store the data. Modified the predict_scraped_reviews.py script to include sqlite3 functionality. After receiving a prediction from the Flask server, the script now connects to reviews.db and inserts the original review text, predicted label, and source URL into the scraped_reviews table. Integrated structured logging into the predict_scraped_reviews.py script to monitor the scraping, request, response, and saving process. Generated a separate script (view_reviews.py) to easily connect to reviews.db and view the contents of the scraped_reviews table using the command prompt. Troubleshooted various errors encountered during the integration process, including: JSON decoding errors in the config file. Import errors between scripts. Indentation errors. Connection errors (ConnectTimeoutError) when the scraping script couldn't reach the Flask server, which we debugged by checking the IP address and port (127.0.0.0 vs 127.0.0.1, and confirming the server port). Database errors ("no such table") which were resolved by ensuring the setup_database.py script was run correctly after deleting any old database file. Successfully tested the full pipeline: You recently confirmed that running predict_scraped_reviews.py while the Flask server is running results in reviews being scraped, sent to the server for prediction, and then saved into the reviews.db database, which you verified using view_reviews.py.

Current State:

You now have a working local system that can:

Scrape reviews from a configured website. Send those reviews to a local Flask server running your trained model. Get predictions ('CG' or 'OR') from the server. Save the original review, predicted label, and source URL into a local SQLite database (reviews.db). Allows you to view the saved data in the database. This is a significant accomplishment! You have built and integrated a machine learning model with web scraping and data persistence.

We are now in the "Enhance for Production" phase, with data storage successfully implemented for scraped data. The next steps involve making the system more robust and ready for enterprise use, starting with production deployment strategies.
