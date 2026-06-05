# All-weatherFarming
Farming app according to their weather conditions and market strategy.
import requests

API_KEY = "YOUR_OPENWEATHER_API_KEY"
CITY = "Sambalpur_Odisha_India"

url = f"https://api.openweathermap.org/data/2.5/weather?q={CITY}&appid={API_KEY}&units=metric"

response = requests.get(url)
data = response.json()

temp = data["main"]["temp"]
humidity = data["main"]["humidity"]
weather = data["weather"][0]["main"]

print(f"Temperature: {temp}°C")
print(f"Humidity: {humidity}%")
print(f"Weather: {weather}")

if temp > 35:
    print("Recommendation: Increase irrigation.")
elif weather.lower() == "rain":
    print("Recommendation: Stop irrigation and monitor drainage.")
elif humidity > 80:
    print("Recommendation: Check for fungal diseases.")
else:
    print("Recommendation: Normal farming operations.")
