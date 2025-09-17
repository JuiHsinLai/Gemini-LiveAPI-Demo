# Gemini LiveAPI Demo

In this tutorial, you will be building a web application that enables you to use your voice and camera to talk to Gemini through the [LiveAPI reference](https://cloud.google.com/vertex-ai/generative-ai/docs/model-reference/multimodal-live).

The [Gemini LiveAPI](https://cloud.google.com/vertex-ai/generative-ai/docs/model-reference/multimodal-live) is a low-latency bidirectional streaming API that supports audio and video streaming inputs and can output audio.

## Architecture

- **Backend (Python WebSockets Server):** Handles authentication and acts as an intermediary between your frontend and the Gemini API.
- **Frontend (HTML/JavaScript):** Provides the user interface and interacts with the backend via WebSockets.

## Pre-requisites

While some web development experience, particularly with localhost, port numbers, and the distinction between WebSockets and HTTP requests, can be beneficial for this tutorial, don't worry if you're not familiar with these concepts. We'll provide guidance along the way to ensure you can successfully follow along.

### File Structure

- `backend/main.py`: The Python backend code
- `backend/requirements.txt`: Lists the required Python dependencies

- `frontend/index.html`: The frontend HTML app
- `frontend/script.js`: Main frontend JavaScript code
- `frontend/gemini-live-api.js`: Script for interacting with the Gemini API
- `frontend/live-media-manager.js`: Script for handling media input and output
- `frontend/pcm-processor.js`: Script for processing PCM audio
- `frontend/cookieJar.js`: Script for managing cookies

![Demo](/img/ui.jpg)

## Setup instructions

You can set up this app locally or via Cloud Shell.

### Setup locally

1. Clone the repository and cd into the correct directory

    ```sh
    git clone https://github.com/JuiHsinLai/Gemini-LiveAPI-Demo.git
    cd Gemini-LiveAPI-Demo
    ```

1. Create a new virtual environment and activate it. The code is tested under `Python 3.11` and above.
    ```sh
    python3 -m venv env
    source env/bin/activate
    ```

1. Install dependencies:

    ```sh
    pip install -r backend/requirements.txt
    ```

1. Start the Python WebSocket server:

    ```sh
    python backend/main.py
    ```

1. Start the frontend:

    - Navigate to `script.js` (around on line 9), `const PROXY_URL = "wss://[THE_URL_YOU_COPIED_WITHOUT_HTTP]";` and replace `PROXY_URL` value with `ws://localhost:8000`. It should look like: `const PROXY_URL = "ws://localhost:8000";`. Note the absence of the second "s" in "wss" as "ws" indicates a non-secure WebSocket connection.
    - Navigate to `script.js` (around on line 10), update `PROJECT_ID` with your Google Cloud project ID.
    - Save the changes you've made to `script.js`
    - Now make sure to open a **separate** terminal window from the backend to run this command (keep the backend server running in the first terminal).

    ```sh
    cd frontend
    python -m http.server
    ```

1. Point your browser to the demo app UI based on the output of the terminal. (e.g., it may be `http://localhost:8000`, or it may use a different port. Make sure you go to the page through `http://localhost:<port>`
instead of using the the pop-up message's `http://[::]:8000/`, the latter will direct you to a restricted 
page without sufficient permissions to start microphone/camera, which further leads to silent exceptions
in the web page.) **In addition, make sure your browser doesn't cache data for localhost. On Mac, Cmd+Option+I and go to the `Network` tab and then check the box of "Disable Cache".**

1. Get your Google Cloud access token:
   Run the following command in a terminal with gcloud installed to set your project, and to retrieve your access token.

    ```sh
    gcloud components update
    gcloud components install beta
    gcloud config set project YOUR-PROJECT-ID
    gcloud auth print-access-token
    ```

1. Copy the access token from the previous step into the UI that you have open in your browser.

1. Enter the model ID in the UI:
   Replace `YOUR-PROJECT-ID` in the input with your Google Cloud Project ID.

1. Connect and interact with the demo:

    - After entering your Access Token and Model ID, press the connect button to connect your web app. Now you should be able to interact with Gemini 2.0 with the Multimodal Live API.

    - **Caution**: to enable `branded voice` by uploading a `.wav` file, make sure you upload the file when the app is at `Disconnected` state, and then click
    the `connect` button. This restriction is because our code instantite the client only when the `Connect` button is clicked, during which it sends LiveAPI a setup message with the sample voice. 
    This is also true if you want to record you own voice as "branded voice".


1. To interact with the app, you can do the following:

    - Text input: You can write a text prompt to send to the model by entering your message in the box and pressing the send arrow. The model will then respond via audio (turn up your volume!).
    - Voice input: Press the microphone button to stop speaking. The model will respond via audio. If you would like to mute your microphone, press the button with a slash through the microphone.
    - Video input: The model will also capture your camera input and send it to Gemini. You can ask questions about current or previous video footage. For more details on how this works, visit the [documentation page for the LiveAPI](https://cloud.google.com/vertex-ai/generative-ai/docs/model-reference/multimodal-live).
