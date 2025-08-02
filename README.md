# **TDS Virtual TA**

A smart, AI-powered Virtual Teaching Assistant for the "Tools in Data Science" course at IIT Madras. This project provides an API that can automatically answer student questions based on course content and historical discussion forum posts.

## **✨ Features**

* **Retrieval-Augmented Generation (RAG)**: Provides answers grounded in actual course content, preventing the AI from making up information.  
* **Multimodal Support**: Can understand and answer questions that include both text and images (e.g., screenshots of error messages).  
* **Source-Cited Answers**: Every answer is backed by links to the original course material or discourse post it was derived from.  
* **RESTful API**: A clean, easy-to-use API built with FastAPI that can be integrated into any frontend application.  
* **Comprehensive Knowledge Base**: Built from scraped course materials and discourse forum data, ensuring a wide range of topics are covered.

## **⚙️ How It Works**

The project follows a two-stage process: a one-time data preparation step and a real-time query-answering service.

1. **Data Preprocessing (preprocess.py)**:  
   * Scrapes raw data from course content (Markdown files) and discourse forums (JSON files).  
   * Cleans and processes the text, breaking it down into smaller, manageable chunks.  
   * Uses an AI model (text-embedding-3-small) to generate numerical representations (embeddings) for each chunk, capturing its semantic meaning.  
   * Stores the text chunks and their embeddings in a local SQLite database (knowledge\_base.db).  
2. **API Service (app.py)**:  
   * A student sends a question (and optionally an image) to the API.  
   * The API generates an embedding for the incoming question. If an image is included, a vision model describes it to enrich the context.  
   * It performs a similarity search on the database to find the most relevant text chunks from the knowledge base.  
   * The question and the relevant chunks are passed to a powerful Large Language Model (gpt-4o-mini).  
   * The LLM generates a comprehensive answer based *only* on the provided context and cites the source URLs.  
   * The final answer and sources are returned to the user.

## **📂 Project Structure**

.  
├── downloaded\_threads/ \# Directory for raw discourse forum JSON files  
├── markdown\_files/     \# Directory for raw course content Markdown files  
├── app.py              \# The main FastAPI application and API logic  
├── preprocess.py       \# Script for data cleaning, chunking, and embedding  
├── knowledge\_base.db   \# SQLite database to store the knowledge base  
├── requirements.txt    \# Python dependencies for the project  
├── .env                \# For storing secret API keys (not committed to Git)  
└── README.md           \# You are here\!

## **🚀 Getting Started**

Follow these instructions to set up and run the project locally.

### **1\. Prerequisites**

* Python 3.8+  
* An API key for an OpenAI-compatible service (the code is configured for aipipe.org).

### **2\. Clone the Repository**

git clone \[(https://github.com/23f2003050/TDS-Virtual-TA.git)]

### **3\. Set Up a Virtual Environment**

It's recommended to use a virtual environment to manage dependencies.  
\# For Windows  
python \-m venv venv  
venv\\Scripts\\activate

\# For macOS/Linux  
python3 \-m venv venv  
source venv/bin/activate

### **4\. Install Dependencies**

pip install \-r requirements.txt

### **5\. Configure Environment Variables**

Create a file named .env in the root of the project directory and add your API key:  
API\_KEY="your-secret-api-key-here"

### **6\. Build the Knowledge Base**

Before you can run the API, you need to process your data and build the knowledge base.

1. Place your raw discourse forum JSON files in the downloaded\_threads directory.  
2. Place your course content Markdown files in the markdown\_files directory.  
3. Run the preprocess.py script. This may take some time as it needs to generate embeddings for all your content.

python preprocess.py

This will create the knowledge\_base.db file, which is your AI's "brain."

## **▶️ Running the Application**

Once the knowledge base is built, you can start the API server.  
uvicorn app:app \--host 0.0.0.0 \--port 8000

The API will now be running and accessible at http://localhost:8000.

## **📝 API Endpoints**

### **Health Check**

A simple endpoint to verify that the API is running and connected to the database.

* **URL**: /health  
* **Method**: GET  
* **Success Response (200)**:  
  {  
    "status": "healthy",  
    "database": "connected",  
    "api\_key\_set": true,  
    "discourse\_chunks": 5000,  
    "markdown\_chunks": 1200,  
    "discourse\_embeddings": 5000,  
    "markdown\_embeddings": 1200  
  }

### **Query the TA**

The main endpoint for asking questions.

* **URL**: /query  
* **Method**: POST  
* **Request Body**:  
  {  
    "question": "How do I set up a virtual environment in Python?",  
    "image": "OPTIONAL\_BASE64\_ENCODED\_IMAGE\_STRING"  
  }

* **Success Response (200)**:  
  {  
    "answer": "To set up a virtual environment in Python, you can use the \`venv\` module. First, navigate to your project directory and run \`python \-m venv venv\` to create a new environment named 'venv'. Then, activate it using \`source venv/bin/activate\` on macOS/Linux or \`venv\\\\Scripts\\\\activate\` on Windows.",  
    "links": \[  
      {  
        "url": "\[https://discourse.onlinedegree.iitm.ac.in/t/topic-slug/123/1\](https://discourse.onlinedegree.iitm.ac.in/t/topic-slug/123/1)",  
        "text": "Guide to setting up your Python environment..."  
      },  
      {  
        "url": "\[https://docs.onlinedegree.iitm.ac.in/python-setup\](https://docs.onlinedegree.iitm.ac.in/python-setup)",  
        "text": "Official documentation for Python setup..."  
      }  
    \]  
  }

## **☁️ Deployment**

This application is ready to be deployed on any platform that supports Python applications. The vercel.json file is included for easy deployment to [Vercel](https://vercel.com/).  
To deploy, ensure your API\_KEY is set as an environment variable in your deployment platform's settings.

## **📄 License**

This project is licensed under the MIT License. See the [LICENSE](https://www.google.com/search?q=LICENSE) file for details.
