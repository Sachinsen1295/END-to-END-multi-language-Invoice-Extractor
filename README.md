# Q&A Chatbot

This repository contains a Q&A chatbot application that utilizes Google Gemini's generative AI model to analyze images and provide responses based on user prompts. The application is built using Streamlit for the frontend and relies on the Gemini API for the backend AI capabilities.

## Setup Instructions

### Prerequisites

1. **Python 3.10 or higher**: Ensure you have Python installed. You can download it from [python.org](https://www.python.org/).
2. **Virtual Environment**: It is recommended to use a virtual environment to manage dependencies.

### Installation

1. **Clone the Repository**

   ```bash
   git clone https://github.com/your-repo/qna-chatbot.git
   cd qna-chatbot
   ```

2. **Create and Activate Virtual Environment**

   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows, use `venv\Scripts\activate`
   ```

3. **Install Dependencies**

   ```bash
   pip install -r requirements.txt
   ```

4. **Set Up Environment Variables**

   Create a `.env` file in the root directory of the project and add your Gemini API key:

   ```
   GEMINI_API_KEY=your_gemini_api_key
   ```

### Running the Application

1. **Run the Streamlit App**

   ```bash
   streamlit run app.py
   ```

2. **Access the Application**

   Open your web browser and navigate to `http://localhost:8501` to view and interact with the application.

## Code Overview

### Environment Setup

The application begins by loading environment variables from a `.env` file:

```python
from dotenv import load_dotenv
import os

load_dotenv()  # Take environment variables from .env.
```

### Importing Libraries

Essential libraries for the application are imported:

```python
import streamlit as st
import pathlib
from PIL import Image
import google.generativeai as genai
```

### Configuring the Gemini API

The Gemini API is configured using the API key from the environment variables:

```python
env = os.getenv("GEMINI_API_KEY")
genai.configure(api_key=env)
```

### Defining Functions

#### `get_gemini_response`

This function loads the Gemini model and generates a response based on the user input and uploaded image:

```python
def get_gemini_response(input, image, prompt):
    model = genai.GenerativeModel('gemini-pro-vision')
    response = model.generate_content([input, image[0], prompt])
    return response.text
```

#### `input_image_setup`

This function processes the uploaded image and prepares it for the API:

```python
def input_image_setup(uploaded_file):
    if uploaded_file is not None:
        bytes_data = uploaded_file.getvalue()
        image_parts = [
            {
                "mime_type": uploaded_file.type,
                "data": bytes_data
            }
        ]
        return image_parts
    else:
        raise FileNotFoundError("No file uploaded")
```

### Streamlit App Initialization

The Streamlit app is initialized with a title and header:

```python
st.set_page_config(page_title="Gemini Image Demo")
st.header("Gemini Application")
```

### User Input

User inputs for text prompt and image upload are defined:

```python
input = st.text_input("Input Prompt: ", key="input")
uploaded_file = st.file_uploader("Choose an image...", type=["jpg", "jpeg", "png"])
```

If an image is uploaded, it is displayed:

```python
if uploaded_file is not None:
    image = Image.open(uploaded_file)
    st.image(image, caption="Uploaded Image.", use_column_width=True)
```

### Processing and Displaying Response

Upon clicking the "Tell me about the image" button, the application processes the input and displays the response:

```python
submit = st.button("Tell me about the image")

input_prompt = """
    You are an expert in understanding invoices.
    You will receive input images as invoices &
    you will have to answer questions based on the input image
    """

if submit:
    image_data = input_image_setup(uploaded_file)
    response = get_gemini_response(input_prompt, image_data, input)
    st.subheader("The Response is")
    st.write(response)
```

## Conclusion

This application demonstrates how to integrate Google Gemini's generative AI model with a Streamlit frontend to create a Q&A chatbot that can analyze and respond to image-based prompts. For further customization and enhancement, refer to the [Google Gemini API documentation](https://developers.google.com/gemini) and [Streamlit documentation](https://docs.streamlit.io/).