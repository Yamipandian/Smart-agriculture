Key Code Snippets 
Python: 
from flask import Flask, render_template, request 
import pandas as pd 
import matplotlib.pyplot as plt 
import io 
import base64 
app = Flask(_name_) 
# Load the dataset (replace with your actual file path) 
data = pd.read_csv('data/crop_data.csv') 
# Ensure correct data types (if needed) 
data['Temperature'] 
= 
errors='coerce') 
pd.to_numeric(data['Temperature'], 
data['Humidity'] = pd.to_numeric(data['Humidity'], errors='coerce') 
data['Yield'] = pd.to_numeric(data['Yield'], errors='coerce') 
@app.route('/') 
def index(): 
return render_template('index.html') 
12 
 
 
13  
 
@app.route('/predict', methods=['POST']) 
def predict(): 
    # Get user input from the form 
    temp = float(request.form['temperature']) 
    humidity = float(request.form['humidity']) 
    weather = request.form['weather'] 
    season = request.form['season'] 
    crop = request.form['crop_name'] 
    visualization_type = request.form['visualization'] 
 
    # Call the function to predict crop yield and visualize 
    plot_url = visualize_crop_yield(temp, humidity, weather, season, 
crop, visualization_type) 
     
    return render_template('result.html', plot_url=plot_url) 
 
def visualize_crop_yield(temp, humidity, weather, season, crop, 
visualization_type): 
    # Filter dataset based on input values 
    filtered_data = data[ 
        (data['Temperature'].between(temp-5, temp+5)) &  # Temperature 
within +-5°C 
        (data['Humidity'].between(humidity-10, humidity+10)) &  # 
Humidity within +-10% 
        (data['Weather'].str.contains(weather, case=False)) &  
        (data['Season'].str.contains(season, case=False)) &  
        (data['Crop'].str.contains(crop, case=False)) 
 
 
14  
    ] 
     
    # If no data matches, generate a plot with default data or suggest the 
user adjust inputs 
    if filtered_data.empty: 
        filtered_data = data  # Use all data for a general visualization 
        message = "No exact matches found. Displaying general crop yield 
data." 
    else: 
        message = "Displaying data based on your inputs." 
 
    # Create the plot based on the selected type 
    plt.figure() 
     
    if visualization_type == 'line': 
        plt.plot(filtered_data['Temperature'], filtered_data['Yield'], 
marker='o', label='Temperature vs Yield') 
        plt.title(f'Yield Prediction for {crop} during {season}') 
        plt.xlabel('Temperature (°C)') 
        plt.ylabel('Yield (kg/hectare)') 
         
    elif visualization_type == 'bar': 
        plt.bar(filtered_data['Temperature'], filtered_data['Yield'], 
label='Temperature vs Yield') 
        plt.title(f'Yield Prediction for {crop} during {season}') 
        plt.xlabel('Temperature (°C)') 
        plt.ylabel('Yield (kg/hectare)') 
         
 
 
15  
    elif visualization_type == 'scatter': 
        plt.scatter(filtered_data['Temperature'], filtered_data['Yield'], 
label='Temperature vs Yield') 
        plt.title(f'Yield Prediction for {crop} during {season}') 
        plt.xlabel('Temperature (°C)') 
        plt.ylabel('Yield (kg/hectare)') 
 
    # Save plot to a string buffer 
    buffer = io.BytesIO() 
    plt.savefig(buffer, format='png') 
    buffer.seek(0) 
    image = base64.b64encode(buffer.getvalue()).decode('utf-8') 
 
    return f'data:image/png;base64,{image}' 
 
if _name_ == '_main_': 
    app.run(debug=True) 
import pandas as pd 
 
def load_dataset(): 
    return pd.read_csv('D://smart_farming_app//data//crop_data.csv') 
 
def predict_yield(temp, humidity, weather, season, crop): 
    data = load_dataset() 
    # Filter the data based on input and calculate crop yield 
    filtered_data = data[(data['Temperature'] == temp) & 
                         (data['Humidity'] == humidity) & 
                         (data['Weather'] == weather) & 
 
 
16  
                         (data['Season'] == season) & 
                         (data['Crop'] == crop)] 
    return filtered_data['Yield'].mean()  # Example: return average yield 
 
HTML: 
<!DOCTYPE html> 
<html lang="en"> 
<head> 
    <meta charset="UTF-8"> 
    <meta name="viewport" content="width=device-width, initial
scale=1.0"> 
    <title>Smart Farming App</title> 
    <style> 
        /* Add your styling here */ 
        body { 
            font-family: 'Arial', sans-serif; 
            background-color: #e0f7fa; 
            color: #333; 
            margin: 0; 
            padding: 0; 
        } 
 
        h1 { 
            text-align: center; 
            color: #00796b; 
            margin-top: 40px; 
        } 
 
 
 
17  
        .form-container { 
            width: 50%; 
            margin: 40px auto; 
            padding: 20px; 
            background-color: #ffffff; 
            border-radius: 8px; 
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); 
        } 
 
        .form-container label { 
            font-size: 16px; 
            margin-bottom: 10px; 
            display: block; 
            color: #00796b; 
        } 
 
        .form-container input, 
        .form-container select { 
            width: 100%; 
            padding: 10px; 
            margin-bottom: 20px; 
            font-size: 16px; 
            border: 1px solid #00796b; 
            border-radius: 4px; 
            box-sizing: border-box; 
        } 
 
        .form-container button { 
 
 
18  
            width: 100%; 
            padding: 12px; 
            background-color: #00796b; 
            color: white; 
            border: none; 
            border-radius: 4px; 
            font-size: 16px; 
            cursor: pointer; 
        } 
 
        .form-container button:hover { 
            background-color: #004d40; 
        } 
 
        .form-container p { 
            text-align: center; 
            font-size: 16px; 
            color: #555; 
        } 
 
        .form-container a { 
            text-decoration: none; 
            color: #00796b; 
            font-size: 16px; 
        } 
    </style> 
</head> 
<body> 
 
 
19  
    <h1>Smart Farming - Crop Yield Prediction</h1> 
    <div class="form-container"> 
        <form action="/predict" method="POST"> 
            <label for="temperature">Temperature (°C):</label> 
            <input type="text" id="temperature" name="temperature" 
required><br> 
 
            <label for="humidity">Humidity (%):</label> 
            <input type="text" id="humidity" name="humidity" 
required><br> 
 
            <label for="weather">Weather (e.g., Sunny, Rainy):</label> 
            <input type="text" id="weather" name="weather" required><br> 
 
            <label for="season">Season (e.g., Summer, Monsoon):</label> 
            <input type="text" id="season" name="season" required><br> 
 
            <label for="crop_name">Crop Name (e.g., Rice, Corn):</label> 
            <input type="text" id="crop_name" name="crop_name" 
required><br> 
 
            <label for="visualization">Choose Visualization Type:</label> 
            <select id="visualization" name="visualization" required> 
                <option value="line">Line Plot</option> 
                <option value="bar">Bar Chart</option> 
                <option value="scatter">Scatter Plot</option> 
            </select><br> 
 
<button type="submit">Predict Yield</button> 
</form> 
</div> 
</body> 
</html> 
