from flask import Flask, request, jsonify
from PIL import Image
import pytesseract
import os

app = Flask(__name__)

@app.route('/', methods=['GET'])
def hello():
    return "OCR server running!"

@app.route('/upload', methods=['POST'])
def upload_image():
    if 'image' not in request.files:
        return "No image uploaded", 400

    image = request.files['image']
    image.save("received.jpg")

    try:
        img = Image.open("received.jpg")
        text = pytesseract.image_to_string(img)
        text = text.lower()

        if "azithromycin" in text:
            return "Azithromycin"
        elif "paracetamol" in text:
            return "Paracetamol"
        else:
            return "Not Found"
    except Exception as e:
        return f"Error: {str(e)}", 500

if __name__ == '__main__':
    app.run(host="0.0.0.0", port=5000)
